### 🏗️ Let’s Build

3 | A Portfolio Health Checker
------------------------------

Our first Java program checks the Flask API.

It calls four endpoints, prints the result of each one, and **exits with code 0 if everything is up, or 1 if anything failed**.

Exit codes are how scripts, CI pipelines, and Kubernetes Jobs know whether a program succeeded, so this small program is a real cloud tool.

Create `LanguagesPortfolio/java/health-check/PortfolioHealthCheck.java`:

```
import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.time.Duration;
import java.util.List;
/**
 * Checks the portfolio's Flask API and reports which endpoints are up.
 * Exit code 0 = all checks passed, 1 = at least one check failed.
 */
public class PortfolioHealthCheck {
    // A record is a compact, immutable data class. Java generates the
    // constructor, accessor methods, equals(), hashCode() and toString().
    record CheckResult(String path, boolean up, String detail, long millis) {}
    // static final = one shared constant. List.of() creates an unmodifiable list.
    static final List<String> ENDPOINTS = List.of(
            "/api/health",
            "/api/skills",
            "/api/projects",
            "/api/analytics/progress"
    );
    public static void main(String[] args) {
        // Read configuration from the environment, with a local default.
        String baseUrl = System.getenv().getOrDefault("PORTFOLIO_API_URL", "http://localhost:5000");
        HttpClient client = HttpClient.newBuilder()
                .connectTimeout(Duration.ofSeconds(3))
                .build();
        System.out.println("Checking portfolio API at " + baseUrl);
        int failures = 0;
        for (String path : ENDPOINTS) {
            CheckResult result = check(client, baseUrl, path);
            System.out.printf("  %-4s  %-25s %-20s %5d ms%n",
                    result.up() ? "UP" : "DOWN", result.path(), result.detail(), result.millis());
            if (!result.up()) {
                failures++;
            }
        }
        System.out.println(failures == 0 ? "All checks passed." : failures + " check(s) failed.");
        System.exit(failures == 0 ? 0 : 1);
    }
    static CheckResult check(HttpClient client, String baseUrl, String path) {
        HttpRequest request = HttpRequest.newBuilder(URI.create(baseUrl + path))
                .timeout(Duration.ofSeconds(5))
                .GET()
                .build();
        long start = System.nanoTime();
        try {
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            long millis = (System.nanoTime() - start) / 1_000_000;
            boolean up = response.statusCode() == 200;
            return new CheckResult(path, up, "HTTP " + response.statusCode(), millis);
        } catch (IOException | InterruptedException e) {
            // Connection refused, timeout, unknown host: the endpoint is down.
            long millis = (System.nanoTime() - start) / 1_000_000;
            return new CheckResult(path, false, e.getClass().getSimpleName(), millis);
        }
    }
}
```

### What This Code Means

*   **Classes**: All Java code lives in classes, and a public class (like `**public class PortfolioHealthCheck**`) must match its file name.
*   **Main Method**: The `**public static void main(String[] args)**` method serves as the official entry point that the JVM calls to start the program.
*   **Static Typing**: Explicit declarations like `**String baseUrl**` **and** `**int failures**` allow the compiler to catch type mistakes before the code ever runs.
*   **Generics**: Container types like `**List<String>**` enforce strict rules so you cannot accidentally insert a number into a list of strings.
*   **Records**: The `**CheckResult**` **record** handles immutable data without boilerplate, letting you read values cleanly via `result.up()`.
*   **Checked Exceptions**: The language forces you to safely handle unpredictable errors by **catching** `**IOException**` **and** `**InterruptedException**`.
*   **Timeouts**: Network configurations like `**connectTimeout()**` prevent a single hung server from freezing the entire program indefinitely.
*   **Environment Config**: Using `**System.getenv(...)**` ensures the exact same code runs seamlessly across your laptop, Docker, and Kubernetes.
*   **Exit Codes**: Triggering `**System.exit(...)**` tells the calling pipeline or terminal whether the health check passed or failed.

### Run It

**Start the Flask backend:**

```
# Terminal 1, in LanguagesPortfolio\backend
.\venv\Scripts\Activate.ps1
python app.py
```

**Then run the Java program:**

```
# Terminal 2, in LanguagesPortfolio\java\health-check
java PortfolioHealthCheck.java
$LASTEXITCODE
```

`java PortfolioHealthCheck.java` _compiles the file in memory and runs it straight away. Java 11 added this shortcut for single-file programs._

**You should see something like:**

```
Checking portfolio API at http://localhost:5000
  UP    /api/health               HTTP 200                12 ms
  UP    /api/skills               HTTP 200                 8 ms
  UP    /api/projects             HTTP 200                 6 ms
  UP    /api/analytics/progress   HTTP 200                 9 ms
All checks passed.
0
```

Now stop Flask with Ctrl+C and run the program again.

Every line shows `DOWN` with an error such as `ConnectException`, the last line says `4 check(s) failed.`, and `$LASTEXITCODE` prints `1`.

The program is doing its job.

4 | Compile, Inspect, and Package
---------------------------------

The shortcut hides the compile step. Do it by hand to see what the JVM actually runs:

```
# In LanguagesPortfolio\java\health-check
# Compile: .java source -> .class bytecode, written to the "classes" folder
javac -d classes PortfolioHealthCheck.java
# List the output: one .class file per class
Get-ChildItem classes
# Run the bytecode (-cp tells the JVM where to find classes)
java -cp classes PortfolioHealthCheck
# Look inside the bytecode
javap -c -cp classes PortfolioHealthCheck
```

`classes` _now contains_ `PortfolioHealthCheck.class` _and_ `PortfolioHealthCheck$CheckResult.class`, b_ecause the record is a class of its own. In the_ `javap` _output you'll see JVM instructions such as_ `getstatic`, `invokestatic`, _and_ `invokevirtual`. _You never write these by hand, but this is the portable format that every JVM runs._

**Now package the classes into a JAR:**

```
jar --create --file health-check.jar --main-class PortfolioHealthCheck -C classes .
java -jar health-check.jar
```

_A_ **JAR** (Java ARchive) _is a ZIP file of_ `.class` _files plus a_ `META-INF/MANIFEST.MF` _file._ `--main-class` _records the entry point in the manifest, so_ `java -jar` _knows which_ `main` _method to run. The JAR is Java's unit of deployment: you build it once and ship the same file everywhere._

Build output doesn’t belong in Git.

**Add these lines to** `LanguagesPortfolio/.gitignore`:

```
java/health-check/classes/
java/health-check/*.jar
java/insights/target/
```

5 | Containerise It
-------------------

Create `LanguagesPortfolio/java/health-check/Dockerfile`:

```
# Stage 1: build with the full JDK (compiler and tools)
FROM eclipse-temurin:21-jdk AS build
WORKDIR /src
COPY PortfolioHealthCheck.java .
RUN javac -d classes PortfolioHealthCheck.java \
 && jar --create --file health-check.jar --main-class PortfolioHealthCheck -C classes .
# Stage 2: run with only the JRE (smaller image, no compiler)
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /src/health-check.jar .
# Run as a regular user instead of root
USER 1000
ENTRYPOINT ["java", "-jar", "health-check.jar"]
```

_This is a_ **_multi-stage build_**_. The first stage uses the JDK to compile the code. The second stage starts fresh from the smaller JRE image and copies in only the finished JAR. The compiler and the source code never reach the final image, so it’s smaller and gives an attacker fewer tools to work with._

> **_Why Eclipse Temurin?_** _Older tutorials use the_ `_openjdk_` _Docker image, but that image is deprecated._ `_eclipse-temurin_` _is a Docker Official Image of the Adoptium project's OpenJDK builds, with both_ `_-jdk_` _and_ `_-jre_` _tags._

**Build and run it while Flask is running on your machine:**

```
# In LanguagesPortfolio\java\health-check
docker build -t portfolio-health-check:1.0 .
docker run --rm -e PORTFOLIO_API_URL=http://host.docker.internal:5000 portfolio-health-check:1.0
```

Inside a container, `localhost` means the container itself, not your laptop. Docker Desktop provides the hostname `host.docker.internal` for reaching your machine. In Kubernetes we'll use a Service name instead.

### The JVM Knows It’s in a Container

Modern JVMs read the container’s memory limit. By default, the JVM uses at most **25% of the container’s memory** for its heap, the area where your objects live. That’s cautious for a container that runs only one Java app, so cloud teams commonly raise it:

```
-XX:MaxRAMPercentage=75.0
```

We’ll pass this option through the `JAVA_TOOL_OPTIONS` environment variable in Kubernetes. The JVM reads that variable at startup and prints a `Picked up JAVA_TOOL_OPTIONS` line, so you can confirm it took effect.

6 | Run It as a Kubernetes Job
------------------------------

In Post 10 we used **Deployments**, which keep a program running forever. A **Job** runs a program until it finishes, and the program’s exit code decides whether the Job succeeded. That fits our health checker exactly.

Create `LanguagesPortfolio/k8s/java-health-check.yaml`:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: java-health-check
  namespace: languages-portfolio
spec:
  backoffLimit: 0                 # Don't retry: a failed check should stay visible
  ttlSecondsAfterFinished: 3600   # Delete the finished Job after one hour
  template:
    spec:
      restartPolicy: Never        # Jobs must use Never or OnFailure
      containers:
      - name: health-check
        image: portfolio-health-check:1.0
        imagePullPolicy: IfNotPresent    # Use the local image instead of pulling from Docker Hub
        env:
        - name: PORTFOLIO_API_URL
          value: http://backend-service:5000    # Kubernetes DNS name of the Flask Service
        resources:
          limits:
            memory: "256Mi"
```

`backend-service` _is the Service defined in_ `k8s/backend.yaml`. _Inside the_ `languages-portfolio` _namespace, Kubernetes DNS resolves that name to the Service's IP address, and the Service forwards each request to one of the Flask pods. The Java program finds the Python API without knowing any pod's IP address._

**Deploy it and check the result:**

```
# In LanguagesPortfolio
# Minikube only: copy your local image into the cluster
minikube image load portfolio-health-check:1.0
kubectl apply -f k8s\java-health-check.yaml
kubectl get jobs -n languages-portfolio
kubectl logs job/java-health-check -n languages-portfolio
```

_The Job takes a few seconds to finish._ `COMPLETIONS 1/1` _means the program exited with code 0._ _If it stays at_ `0/1`_, at least one check failed. The logs show the same table you saw on your laptop, so you can see which endpoint was down._

**You can’t change a Job after creating it, so delete it before running it again:**

```
kubectl delete job java-health-check -n languages-portfolio
kubectl apply -f k8s\java-health-check.yaml
```

To run the check on a schedule, put the same pod template inside a **CronJob** with `schedule: "*/15 * * * *"` (every 15 minutes).

7 | From Script to Service: Spring Boot
---------------------------------------

The health checker is a script: it runs and then exits. Enterprise systems are mostly **services**, programs that run all the time and answer requests. In Java, the standard way to build one is **Spring Boot**.

Spring Boot gives you:

*   **An embedded web server.** Tomcat is packaged inside your JAR, so `java -jar` starts a complete web service.
*   **Dependency injection.** A class lists what it needs in its constructor, and Spring creates and connects the objects for you.
*   **Auto-configuration.** Add a dependency, and Spring Boot configures it with sensible defaults.
*   **Actuator.** Built-in health and metrics endpoints that Kubernetes can probe.

We’ll build **java-insights**, a small service that reports its own status and whether the Flask backend is reachable.

### Step 1: Generate the Project

Go to [start.spring.io](https://start.spring.io/) and:

**Project** is set to Maven.
**Language** is chosen as Java.
**Spring Boot** version uses the default stable release.
**Group** identifier is configured as `com.portfolio`.
**Artifact** name is specified as `insights`.
**Packaging** type is defined as Jar.
**Java** version is selected as 21.
**Dependencies** include Spring Web and Spring Boot Actuator.

Click **Generate** and unzip the download into `LanguagesPortfolio\java`.

The zip contains an `insights` folder, so the project ends up in`LanguagesPortfolio\java\insights`.

**Open** `src/main/java/com/portfolio/insights/InsightsApplication.java`. **Spring Initializr generated it for you:**

```
@SpringBootApplication
public class InsightsApplication {
    public static void main(String[] args) {
        SpringApplication.run(InsightsApplication.class, args);
    }
}
```

It’s an ordinary `main` method, like the one in our health checker. `SpringApplication.run` starts Spring, which finds your classes in this package, configures everything, and starts the web server. `@SpringBootApplication` switches all of that on.

### Step 2: Write a REST Controller

Create `src/main/java/com/portfolio/insights/StatusController.java`:

```
package com.portfolio.insights;
import java.time.Instant;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestClient;
import org.springframework.web.client.RestClientException;
@RestController
@RequestMapping("/api/java")
public class StatusController {
    // The JSON shape this endpoint returns. Spring converts records to JSON automatically.
    public record Status(String service, String javaVersion, String flaskBackend, String checkedAt) {}
    private final RestClient flaskApi;
    // Spring calls this constructor and passes in the configured Flask URL.
    public StatusController(@Value("${portfolio.api.url}") String portfolioApiUrl) {
        this.flaskApi = RestClient.create(portfolioApiUrl);
    }
    @GetMapping("/status")
    public Status status() {
        String flaskBackend;
        try {
            // Java calling Python: GET <flask-url>/api/health
            flaskApi.get().uri("/api/health").retrieve().toBodilessEntity();
            flaskBackend = "UP";
        } catch (RestClientException e) {
            // Flask is down, unreachable, or returned an error status
            flaskBackend = "DOWN";
        }
        return new Status(
                "java-insights",
                System.getProperty("java.version"),
                flaskBackend,
                Instant.now().toString());
    }
}
```

### What The Annotation Means

*   **The** `**@RestController**` **annotation** marks a class as a controller where every method automatically returns JSON data in response to HTTP requests, which functions similarly to Flask functions using `@app.route` that return a `jsonify(...)` response.
*   **The** `**@RequestMapping("/api/java")**` **annotation** defines a base URL prefix for all endpoints within that class, operating much like a Flask `Blueprint` with a designated `url_prefix`.
*   **The** `**@GetMapping("/status")**` **annotation** specifies that a method will handle incoming HTTP GET requests for the combined path (e.g., `/api/java/status`), which serves the same purpose as Flask’s `@app.route('/api/java/status', methods=['GET'])`.
*   **The** `**@Value("${portfolio.api.url}")**` **annotation** injects a configuration value directly into a variable from the application properties, matching the behavior of loading environment variables in Flask via `os.getenv(...)` within a `config.py` file.

This small class carries two cloud lessons:

*   **Service-to-service calls.** `RestClient` is Spring's HTTP client. Java and Python don't share any code. They share an HTTP contract, so either side can be rewritten without the other noticing.
*   **Failure isolation.** If Flask is down, this service still answers and reports `DOWN` instead of crashing. One broken service shouldn't take others down with it.

`RestClient` requires Spring Boot 3.2 or newer, and Initializr's default version is newer than that.

### Step 3: Configure It

Replace the contents of `src/main/resources/application.properties` with:

```
spring.application.name=insights
server.port=8080
# Where the Flask API lives. Uses the PORTFOLIO_API_URL environment
# variable if it's set, otherwise falls back to localhost.
portfolio.api.url=${PORTFOLIO_API_URL:http://localhost:5000}
# Expose /actuator/health/liveness and /actuator/health/readiness
# for Kubernetes probes (and when running locally).
management.endpoint.health.probes.enabled=true
```

In a `.properties` file, comments must be on their own line. Anything after the `=` is part of the value.

This follows the same rule as the Flask backend’s `config.py`: the code reads its settings from the environment, so one JAR works on your laptop, in Docker, and in Kubernetes.

### Step 4: Run and Test

With Flask still running, start the Java service:

```
# In LanguagesPortfolio\java\insights
.\mvnw.cmd spring-boot:run
```

The first run downloads Maven and the project’s libraries, so it takes a few minutes. When you see `Started InsightsApplication`, test it from another terminal. `curl.exe` comes with Windows and prints the raw JSON:

```
curl.exe http://localhost:8080/api/java/status
curl.exe http://localhost:8080/actuator/health
```

The first command prints something like the line below. Spring turned the `Status` record into JSON, with one field per record component:

```
{"service":"java-insights","javaVersion":"21.0.x","flaskBackend":"UP","checkedAt":"2026-10-03T09:30:12.345678Z"}
```

The second returns Actuator’s health report, which includes `"status":"UP"`. Now stop Flask and call `/api/java/status` again. `flaskBackend` changes to `DOWN`, but the Java service keeps answering.

8 | Deploy the Spring Boot Service to Kubernetes
------------------------------------------------

### Package the JAR

Stop the running service with Ctrl+C, then build and run the JAR:

```
# In LanguagesPortfolio\java\insights
.\mvnw.cmd clean package
java -jar target\insights-0.0.1-SNAPSHOT.jar
```

`package` compiles the code, runs the test Initializr generated, and builds `target\insights-0.0.1-SNAPSHOT.jar`. This is a Spring Boot "fat JAR": it contains your classes, every library, and the embedded Tomcat server. That single file is the whole application.

Stop it with Ctrl+C once you've seen it start.

### Containerise

Create `LanguagesPortfolio/java/insights/Dockerfile`:

```
FROM eclipse-temurin:21-jre
WORKDIR /app
# The JAR built by "mvnw.cmd clean package"
COPY target/*.jar app.jar
USER 1000
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

This time Maven builds the JAR on your machine, and the Dockerfile only packages it, so one stage is enough. The multi-stage pattern from Section 5 works here too, and CI pipelines usually use it.

```
# In LanguagesPortfolio\java\insights
docker build -t portfolio-java-insights:1.0 .
```

### Deploy

Create `LanguagesPortfolio/k8s/java-insights.yaml`:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-insights
  namespace: languages-portfolio
  labels:
    app: java-insights
spec:
  replicas: 1
  selector:
    matchLabels:
      app: java-insights
  template:
    metadata:
      labels:
        app: java-insights
    spec:
      containers:
      - name: java-insights
        image: portfolio-java-insights:1.0
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
        env:
        - name: PORTFOLIO_API_URL
          value: http://backend-service:5000
        - name: JAVA_TOOL_OPTIONS
          value: "-XX:MaxRAMPercentage=75.0"
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 20
---
apiVersion: v1
kind: Service
metadata:
  name: java-insights-service
  namespace: languages-portfolio
spec:
  selector:
    app: java-insights
  ports:
  - port: 8080
    targetPort: 8080
  type: ClusterIP
```

What’s different from the Flask Deployment:

*   `**PORTFOLIO_API_URL**` points at the Flask Service, exactly like the Job does.
*   **The probes use Actuator.** The Flask backend’s probes in `k8s/backend.yaml` call `/api/health`. Here, readiness tells Kubernetes when to send traffic to the pod, and liveness tells it when to restart the container.
*   **The initial delays are longer** because the JVM and Spring need several seconds to start.
*   `**JAVA_TOOL_OPTIONS**` lets the heap use 75% of the 512Mi memory limit.

**Deploy and test:**

```
# In LanguagesPortfolio
# Minikube only
minikube image load portfolio-java-insights:1.0
kubectl apply -f k8s\java-insights.yaml
kubectl get pods -n languages-portfolio -l app=java-insights
kubectl port-forward service/java-insights-service 8080:8080 -n languages-portfolio
```

Then, in another terminal:

```
curl.exe http://localhost:8080/api/java/status
```

`"flaskBackend":"UP"` proves that Java reached Python through Kubernetes DNS inside the cluster. Your portfolio's backend now runs two languages.

### ⛔ End of Building Tutorial⛔

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Java in the Cloud](https://ntombizakhona.medium.com/java-in-the-cloud-216ebe022a1b?postPublishedType=repub)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**23 October 2026**
