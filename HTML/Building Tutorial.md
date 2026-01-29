### 🏗️ Let’s Build

5 | Build Your Portfolio
------------------------

### Step 01: Set Up Your Project Directory

Create this folder structure on your computer:

```
portfolio-project/
├── index.html
├── about.html (optional second page)
├── styles/
│   └── main.css (we'll create this in the next post)
├── scripts/
│   └── main.js (we'll create this later)
├── assets/
│   └── images/
│       ├── profile.jpg
│       └── projects/
```

**How to Quickly create this with Windows:**

```
cmd
mkdir portfolio-project
cd portfolio-project
mkdir styles scripts assets\images assets\images\projects
```

Mac/Linux:

```
bash
mkdir -p portfolio-project/{styles,scripts,assets/images/projects}
cd portfolio-project
```

### Step 02: Create Your Portfolio HTML

Create a file called index.html and paste this complete portfolio structure:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Your Name - Cloud Developer Portfolio</title>
    <meta name="description" content="Portfolio showcasing my journey learning cloud computing and essential programming languages">
</head>
<body>
    
    <!-- Navigation Header -->
    <header>
        <nav>
            <h1>Your Name</h1>
            <p>Cloud Developer in Training</p>
            <ul>
                <li><a href="#about">About</a></li>
                <li><a href="#skills">Skills</a></li>
                <li><a href="#projects">Projects</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>
    <!-- Main Content -->
    <main>
        
        <!-- Hero Section -->
        <section id="hero">
            <h1>Building Cloud Expertise, One Language at a Time</h1>
            <p>Follow my journey from beginner to cloud-ready developer</p>
            <a href="#contact">Get In Touch</a>
        </section>
        <!-- About Section -->
        <section id="about">
            <h2>About My Journey</h2>
            <img src="assets/images/profile.jpg" alt="Your Name - Profile Photo" width="200">
            <p>
                I'm on a structured learning path to master the essential languages and tools for cloud computing. 
                This portfolio documents my progress through <strong>11 fundamental skills</strong>: English, 
                Mathematics, HTML, CSS, JavaScript, Python, Java, Linux, SQL, Kubernetes, and Git.
            </p>
            <p>
                My goal is to become a <strong>cloud engineer</strong> capable of designing, building, and 
                deploying scalable applications on platforms like AWS, Azure, and Google Cloud.
            </p>
        </section>
        <!-- Skills Section -->
        <section id="skills">
            <h2>Skills Progress Tracker</h2>
            <p>Tracking my learning journey across 11 essential languages and tools:</p>
            
            <div class="skill-list">
                
                <!-- Skill 1: English -->
                <article class="skill-item">
                    <h3>1. English - Technical Communication</h3>
                    <p>Writing clear documentation, README files, and technical explanations</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 100%">100%</div>
                    </div>
                    <p><strong>Status:</strong> ✅ Completed</p>
                </article>
                
                <!-- Skill 2: Mathematics -->
                <article class="skill-item">
                    <h3>2. Mathematics - Logic & Problem Solving</h3>
                    <p>Boolean logic, algorithms, problem decomposition, pattern recognition</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 100%">100%</div>
                    </div>
                    <p><strong>Status:</strong> ✅ Completed</p>
                </article>
                
                <!-- Skill 3: HTML -->
                <article class="skill-item">
                    <h3>3. HTML - Web Structure</h3>
                    <p>Semantic markup, forms, accessibility, document structure</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 100%">100%</div>
                    </div>
                    <p><strong>Status:</strong> ✅ Completed (This page!)</p>
                </article>
                
                <!-- Skill 4: CSS -->
                <article class="skill-item">
                    <h3>4. CSS - Styling & Layout</h3>
                    <p>Responsive design, flexbox, grid, animations</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> ⏳ Coming Next</p>
                </article>
                
                <!-- Skill 5: JavaScript -->
                <article class="skill-item">
                    <h3>5. JavaScript - Interactivity</h3>
                    <p>DOM manipulation, events, async programming, APIs</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
                <!-- Skill 6: Python -->
                <article class="skill-item">
                    <h3>6. Python - Automation & Backend</h3>
                    <p>Scripting, data processing, cloud automation, AWS Lambda</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
                <!-- Skill 7: Java -->
                <article class="skill-item">
                    <h3>7. Java - Enterprise Applications</h3>
                    <p>OOP principles, Spring Boot, microservices</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
                <!-- Skill 8: Linux -->
                <article class="skill-item">
                    <h3>8. Linux - Command Line & Systems</h3>
                    <p>Bash scripting, file system, process management, SSH</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
                <!-- Skill 9: SQL -->
                <article class="skill-item">
                    <h3>9. SQL - Database Management</h3>
                    <p>Queries, joins, database design, optimization</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
                <!-- Skill 10: Kubernetes -->
                <article class="skill-item">
                    <h3>10. Kubernetes - Container Orchestration</h3>
                    <p>Pods, services, deployments, scaling</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
                <!-- Skill 11: Git -->
                <article class="skill-item">
                    <h3>11. Git - Version Control</h3>
                    <p>Branching, merging, collaboration, GitHub workflows</p>
                    <div class="progress-bar">
                        <div class="progress" style="width: 0%">0%</div>
                    </div>
                    <p><strong>Status:</strong> 📅 Upcoming</p>
                </article>
                
            </div>
        </section>
        <!-- Projects Section -->
        <section id="projects">
            <h2>Learning Projects</h2>
            <p>Real projects I'm building while learning each language:</p>
            
            <div class="project-list">
                
                <!-- Project 1 -->
                <article class="project">
                    <h3>Portfolio Website (This Site!)</h3>
                    <p>
                        A fully responsive portfolio website built from scratch, showcasing my learning journey. 
                        Each skill I learn gets integrated into this living project.
                    </p>
                    <p><strong>Technologies:</strong> HTML, CSS (next), JavaScript (coming)</p>
                    <p><strong>Status:</strong> 🚧 In Progress</p>
                    <ul>
                        <li>✅ HTML structure complete</li>
                        <li>⏳ CSS styling (next post)</li>
                        <li>📅 JavaScript interactivity (upcoming)</li>
                        <li>📅 Deployment to AWS S3 (upcoming)</li>
                    </ul>
                </article>
                
                <!-- Project 2 (Placeholder) -->
                <article class="project">
                    <h3>AWS Cost Calculator</h3>
                    <p>
                        A web tool to calculate estimated AWS costs for common services. 
                        Applies mathematical formulas learned in Post 2.
                    </p>
                    <p><strong>Technologies:</strong> HTML, CSS, JavaScript, Math</p>
                    <p><strong>Status:</strong> 📅 Planned</p>
                </article>
                
                <!-- Project 3 (Placeholder) -->
                <article class="project">
                    <h3>Cloud Deployment Automation Script</h3>
                    <p>
                        Python script to automate website deployment to AWS S3 with one command.
                    </p>
                    <p><strong>Technologies:</strong> Python, AWS CLI, Bash</p>
                    <p><strong>Status:</strong> 📅 Planned</p>
                </article>
                
            </div>
        </section>
        <!-- Contact Section -->
        <section id="contact">
            <h2>Get In Touch</h2>
            <p>
                Interested in following my learning journey? Have advice or opportunities? 
                I'd love to hear from you!
            </p>
            
            <form action="#" method="POST">
                
                <div class="form-group">
                    <label for="name">Your Name:</label>
                    <input type="text" id="name" name="name" placeholder="John Doe" required>
                </div>
                
                <div class="form-group">
                    <label for="email">Your Email:</label>
                    <input type="email" id="email" name="email" placeholder="john@example.com" required>
                </div>
                
                <div class="form-group">
                    <label for="subject">Subject:</label>
                    <input type="text" id="subject" name="subject" placeholder="Regarding your portfolio">
                </div>
                
                <div class="form-group">
                    <label for="message">Message:</label>
                    <textarea id="message" name="message" rows="6" placeholder="Your message here..." required></textarea>
                </div>
                
                <div class="form-group">
                    <input type="checkbox" id="newsletter" name="newsletter">
                    <label for="newsletter">Send me updates about your learning journey</label>
                </div>
                
                <button type="submit">Send Message</button>
                <p><em>Note: Form will be functional after we add backend processing (Python/JavaScript)</em></p>
            </form>
            
            <div class="contact-info">
                <h3>Other Ways to Connect:</h3>
                <ul>
                    <li>📧 Email: <a href="mailto:your.email@example.com">your.email@example.com</a></li>
                    <li>💼 LinkedIn: <a href="https://linkedin.com/in/yourprofile" target="_blank">linkedin.com/in/yourprofile</a></li>
                    <li>💻 GitHub: <a href="https://github.com/yourusername" target="_blank">github.com/yourusername</a></li>
                    <li>🐦 Twitter: <a href="https://twitter.com/yourhandle" target="_blank">@yourhandle</a></li>
                </ul>
            </div>
        </section>
    </main>
    <!-- Footer -->
    <footer>
        <p>&copy; 2026 Your Name. Built while learning cloud computing languages.</p>
        <p>
            <a href="#about">About</a> | 
            <a href="#skills">Skills</a> | 
            <a href="#projects">Projects</a> | 
            <a href="#contact">Contact</a>
        </p>
        <p><small>This portfolio is a work in progress. Last updated: Day 3 of 60</small></p>
    </footer>
</body>
</html>
```

### Step 03: Open Your Portfolio in a Browser

**Method 1: Directly open the file**

1.  Navigate to your portfolio-project folder
2.  Right-click index.html
3.  Select “Open with” → Your browser (Chrome, Firefox, Safari, Edge)

**Method 2: Drag and drop**

1.  Open your web browser
2.  Drag index.html into the browser window

What you should see:

*   ✅ Navigation menu with links
*   ✅ Hero section with your headline
*   ✅ About section (placeholder for your photo)
*   ✅ Skills progress tracker (showing 3/11 complete)
*   ✅ Projects section with portfolio description
*   ✅ Contact form (not functional yet, but visible)
*   ✅ Footer with copyright

What it looks like:
**Plain, unstyled HTML, and that’s perfect!**

_We’ll add beautiful styling with CSS in the next post._

### Step 04: Test Your Navigation Links

Click each link in your navigation menu:

*   **About** → Should jump to About section
*   **Skills** → Should jump to Skills section
*   **Projects** → Should jump to Projects section
*   **Contact** → Should jump to Contact section

These are called **_anchor links_**, they jump to sections with matching id attributes

```
<a href="#skills">Skills</a>  ← Link
<section id="skills">         ← Destination
```

### Step 05: Validate Your HTML

Professional developers always validate their code. Use the W3C HTML Validator:

1.  Go to: [https://validator.w3.org/](https://validator.w3.org/)
2.  Click **Validate by File Upload**
3.  Upload your index.html
4.  Fix any errors shown

**Green Banner: Document checking completed. No errors or warnings to show.**

Common Beginner Errors:

*   **Unclosed tags:** <p>Text (missing </p>)
*   **Mismatched tags**: <strong><em>Text</strong></em> (wrong order)
*   **Missing required attributes:** <img src=”photo.jpg”> (missing alt)

### ⛔ End of Building Tutorial⛔

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [HTML in the Cloud](https://medium.com/@ntombizakhona/html-in-the-cloud-dc23a04bfe51)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**29 January 2026**
