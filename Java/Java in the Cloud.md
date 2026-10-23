Java in the Cloud
=================

The Enterprise Development Language
-----------------------------------

> **_Java is one of the very first languages I have ever learned, I just had to sneak it in here, also because it is robust and… my favourite. So, it’s a save the best for “_lastish” _kind of situation._**


So, you’ve built and orchestrated a cloud-native portfolio with Python, SQL, Docker, and Kubernetes.

But cloud computing is not only about startups and small services. Banks, telecommunications companies, retailers, and governments run systems that must process millions of requests reliably for years.

Think about it:

*   _Mission-critical transactions?_ → **They need reliability and strong typing**
*   _Large engineering teams?_ → **They need maintainable, well-structured code**
*   _Distributed cloud services?_ → **They need mature frameworks and observability**
*   _Enterprise integrations?_ → **They need a proven ecosystem**

**This is where Java comes in.**

Java remains the backbone of enterprise cloud applications. From Spring Boot microservices to Apache Kafka streaming platforms, Java powers mission-critical cloud infrastructure worldwide.

_In this post, Java joins our portfolio. We won’t throw away the Python backend. We’ll add Java_ **_next to it_**_, the way real companies do: two small Java programs that talk to the Flask API over HTTP, run in Docker, and deploy to the same Kubernetes cluster from the previous post._

Topics
------

1.  How Java runs: JDK, JVM, and bytecode
2.  Setting up Java on Windows
3.  Java fundamentals: A Portfolio Health Checker
4.  Compiling, Inspecting bytecode, And Packaging a JAR
5.  Containerising Java with Docker
6.  Running Java as a Kubernetes Job
7.  Spring Boot: A Java Microservice For The Portfolio
8.  Deploying the Spring Boot Service To Kubernetes
9.  Java’s Cloud Ecosystem And Best Practices
10.  Troubleshooting

1 | How Java Runs
-----------------

Python runs your `.py` file directly through the Python interpreter. Java adds a compile step:

```
PortfolioHealthCheck.java ──javac──► PortfolioHealthCheck.class ──java──► JVM
  source code you write              bytecode: the same file          runs it on Windows,
                                     for every operating system       Linux, macOS, x86, ARM
```

*   **Source code** (`.java` files) is what you write.
*   `**javac**`, the Java compiler, checks your code and turns it into **bytecode** (`.class` files). Bytecode isn't machine code for a real CPU. It's a set of instructions for the JVM.
*   **The JVM** (Java Virtual Machine) loads the bytecode and runs it. While your program runs, the JVM’s **JIT (just-in-time) compiler** turns frequently used code into fast native machine code, and its **garbage collector** frees memory you no longer use.

That’s Java’s famous promise: _write once, run anywhere_. You compile on your Windows laptop, and the same bytecode runs in a Linux container in the cloud.

### JDK vs. JRE vs. JVM

The **JVM** (_Java Virtual Machine_) is the engine that runs Java bytecode and is always present as part of the JRE and JDK.

The **JRE** (_Java Runtime Environment_) includes the JVM and the standard library, making it suitable for running Java applications, for example, in a production container.

The **JDK** (_Java Development Kit_) includes the runtime environment and development tools such as `javac`, `jar`, `javap`, and `jshell`, which you need to write, compile, build, and debug Java applications on your laptop.

### Why This Matters in the Cloud

*   **Portability:** one build artifact (a JAR file) runs on any cloud, operating system, or CPU architecture that has a JVM.
*   **Long-running performance:** the JIT optimizes code while a service runs, so busy APIs get faster after they warm up.
*   **The trade-off:** JVM apps start slower and use more memory than a small Python script. Where startup time matters, such as serverless functions, tools like GraalVM native images and AWS Lambda SnapStart help.

### Java Versions and LTS

A new Java version ships every six months.

Every two years, one of them is a **Long-Term Support (LTS)** release that receives security updates for years. Java 17, 21, and 25 are LTS releases, and cloud teams standardize on LTS versions. This post uses **Java 21**, which every tool and base image in this post supports.

### Java for Python Developers

You learned Python already, so here’s a map from one to the other:

```
+-------------------+----------------------------+------------------------------------------+
| Feature           | Python                     | Java                                     |
+-------------------+----------------------------+------------------------------------------+
| Typing            | Dynamic: level = 75        | Static: int level = 75; (or var level)   |
| Code blocks       | Indentation                | Curly braces { } and semicolons          |
| Running           | python app.py              | javac then java, or java -jar app.jar    |
| Dependencies      | pip + requirements.txt     | Maven + pom.xml (or Gradle)              |
| Web framework     | Flask                      | Spring Boot                              |
| A route           | @app.route('/api/health')  | @GetMapping("/api/health")               |
| "No value"        | None                       | null                                     |
| Handling errors   | try / except               | try / catch                              |
+-------------------+----------------------------+------------------------------------------+
```

2 | Set Up Java on Windows
--------------------------

Install Eclipse Temurin JDK 21, a free, open-source build of OpenJDK:

```
winget install EclipseAdoptium.Temurin.21.JDK
```

If you prefer a regular installer, download the JDK 21 `.msi` from [adoptium.net](https://adoptium.net/) and enable **Set JAVA_HOME variable** during setup.

Close and reopen your terminal so it picks up the updated `PATH`, then check both tools:

```
java --version
javac --version
```

**Both should report version 21.**

You **don’t need to install Maven**.

The Spring Boot project we generate in Section 7 includes the _Maven Wrapper_ (`mvnw.cmd`), which downloads the right Maven version for you. You'll also use Docker Desktop and `kubectl` from Post 10.

9 | Java’s Cloud Ecosystem
--------------------------

These two programs use a small slice of what Java offers. Here’s where to go next:

### Web APIs, Microservices, and Databases

*   **Web APIs and microservices** are commonly built using robust frameworks like **Spring Boot**, **Quarkus**, and **Micronaut**.
*   **Database interactions** are handled through core technologies like **JDBC** or object-relational mapping frameworks like **Spring Data JPA and Hibernate**, which can be used against systems like your **MySQL database**.
*   **Database migrations** and version control for schemas are managed using tools like **Flyway** or **Liquibase**.

### Messaging, Security, and Observability

*   **Messaging and streaming** architectures rely on clients for **Apache Kafka** (which itself is written in Java and Scala) or **RabbitMQ**.
*   **Application security**, including authentication and authorization via **OAuth2** and **JWT**, is typically implemented using **Spring Security**.
*   **Observability**, metrics, and distributed tracing are configured using **Spring Boot Actuator**, **Micrometer**, and **OpenTelemetry**.

### Cloud, Operations, and Building

*   **Cloud SDKs** allow seamless integration with major platforms through the **AWS SDK for Java**, **Azure SDK for Java**, and **Google Cloud client libraries**.
*   **Serverless deployment** targets platforms like **AWS Lambda Java runtimes** utilizing **SnapStart**, as well as **Azure Functions**.
*   **Fast startup times** and reduced memory footprints are achieved by compiling to a **GraalVM native image** or using **OpenJDK CRaC** (Coordinated Restore at Checkpoint).
*   **Project builds** and dependency management are handled by the industry-standard build tools **Maven** or **Gradle**.

### Best Practices for Java in the Cloud

*   **Use an LTS JDK and pin your image tags** (`eclipse-temurin:21-jre`, not `latest`) so builds are repeatable.
*   **Ship the JRE, not the JDK,** in production images, and run as a non-root user.
*   **Read configuration from the environment.** Keep passwords in Kubernetes Secrets as in Post 10, never in code or in `application.properties`.
*   **Set timeouts on every outbound call.** The health checker does. A production version of `StatusController` should too.
*   **Expose health endpoints and connect them to probes.** Actuator does most of the work.
*   **Size memory on purpose.** Set a container memory limit and tune `MaxRAMPercentage` to match it.
*   **Log to standard output.** Kubernetes collects it for `kubectl logs`, so don't write log files inside the container.

10 | Troubleshooting
--------------------

### Local Environment and Compilation Issues

*   **If** `**java**` **is not recognized**, the likely cause is that the terminal was opened before Java was installed. You can fix this by **opening a new terminal** and checking the installation with `Get-Command java`.
*   **When** `**mvnw.cmd**` **complains about** `**JAVA_HOME**`, the installer did not set the environment variable. To resolve this, **run** `**$env:JAVA_HOME = Split-Path (Split-Path (Get-Command java).Source)**` in that terminal, or set it permanently under Environment Variables in your Windows settings.
*   **If you get an error stating that** `**class PortfolioHealthCheck is public, should be declared in a file named PortfolioHealthCheck.java**`, the file name and the class name do not match. You must **rename the file** to match the class name exactly, including identical capital letters.

### Connection and Network Issues

*   **When every check shows** `**DOWN**` **with a** `**ConnectException**`, Flask either is not running or the configured URL is incorrect. To fix this, **start Flask** and verify your environment variable by checking `$env:PORTFOLIO_API_URL`.
*   **If the container cannot reach** `**localhost:5000**`, it is because `localhost` refers to the container itself. You should change the connection URL to use `**http://host.docker.internal:5000**` instead.
*   **If you find that port** `**8080**` **is already in use**, another program or a second copy of the service is occupying that port. You need to **stop the conflicting program**, or start your service on a different port by running `$env:SERVER_PORT = "8081"`.

### Kubernetes Deployment Issues

*   **When encountering** `**ErrImagePull**` **or** `**ImagePullBackOff**` **errors**, your cluster cannot see your local container image. If you are using Minikube, fix this by running `**minikube image load <image>.**` Otherwise, push the image to a registry and update the `image:` field in your deployment configuration.
*   **If a Pod status shows** `**OOMKilled**`, the memory limit configured in Kubernetes is too low for the Java Virtual Machine (JVM). You can resolve this by **raising** `**limits.memory**` in your pod specification or lowering the `MaxRAMPercentage` in your Java options.
*   **When re-applying a Job fails with an error stating a field is immutable**, it means Kubernetes Jobs cannot be modified after they are created. To fix this, **delete the existing Job** and apply your configuration file again.

### Final Thoughts

Java adds a proven enterprise layer to your cloud toolkit. You compiled source code into bytecode, packaged it as a JAR, shipped it in a container, and ran it on the same Kubernetes platform you learned in the previous article.

Then you built a Spring Boot microservice that talks to your Python API over HTTP.

The syntax is only part of the lesson.

**Java’s real value in the cloud comes from its mature ecosystem, predictable structure, and ability to support large systems and engineering teams over time.** As your portfolio now shows, it doesn’t have to replace anything: Java and Python run side by side, connected by HTTP and Kubernetes DNS.

Concluding Remarks
------------------

Java completes the application language layer of our cloud journey. Next, we move into **Infrastructure as Code with Terraform:** codifying the cloud itself.

Languages Portfolio Progress
----------------------------

✅ **01 — English**: Technical Communication And Prompt Engineering

✅ **02 — Mathematics:** Logic And Problem-Solving Fundamentals

✅ **03 — HTML:** Web Structure And Semantic Markup

✅ **04 — CSS:** Styling And Responsive Design

✅ **05 — JavaScript:** Interactive Web Development

✅ **06 — Python:** Backend Logic And Cloud Automation

✅ **07 — Git:** Version Control And Deployment

✅ **08 — Linux:** The Cloud Operating System

✅ **09 — SQL:** Cloud Data Management

✅ **10 — Kubernetes:** Container Orchestration At Scale

✅ **11 — Java:** Enterprise Cloud Development

⬜ **12 — Terraform:** Infrastructure as Code

⬜ **Bonus — Agentic IDEs:** AI-Powered Cloud Development

Additional Resources
--------------------

### [**The Java™ Tutorials**](https://docs.oracle.com/javase/tutorial/)

> The Java Tutorials are practical guides for programmers who want to use the Java programming language to create applications.
> 
> They include hundreds of complete, working examples, and dozens of lessons. Groups of related lessons are organized into “trails”.

### [Java](https://developer.mozilla.org/en-US/docs/Glossary/Java)

> Java is a [compiled](https://developer.mozilla.org/en-US/docs/Glossary/Compile), [object-oriented](https://developer.mozilla.org/en-US/docs/Glossary/OOP), highly portable [programming](https://developer.mozilla.org/en-US/docs/Glossary/Computer_Programming) language.
> 
> Java is statically typed and features a similar syntax to C. It comes with a large library of readily usable functions, the Java Software Development Kit (SDK).
>

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Java in the Cloud](https://ntombizakhona.medium.com/java-in-the-cloud-216ebe022a1b?postPublishedType=repub)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**23 October 2026**
