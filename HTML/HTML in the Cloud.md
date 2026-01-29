HTML in the Cloud
=================

The Markup Language
-------------------

**HTML** (_HyperText Markup Language_) is the skeleton of every website you’ve ever visited. Think of it like building a house:

*   **HTML:** _The frame and walls_ (structure)
*   **CSS:** _The paint and decorations_ (we’ll learn this next)
*   **JavaScript:** _The electricity and plumbing_ (functionality)

You can’t paint walls that don’t exist, and you can’t add electricity to a house without rooms. That’s why we start with **HTML.**

Why HTML Matters in Cloud Computing
-----------------------------------

**_“Wait, I thought cloud computing was about servers and AWS?”_**

Yes! But here’s the reality:

*   **AWS Console?** _Built with HTML_
*   **Cloud documentation you read?** _HTML_
*   **Monitoring dashboards?** _HTML + JavaScript_
*   **Your portfolio to get hired?** _HTML, CSS, JavaScript_

Even as a Cloud Engineer, you’ll build internal tools, dashboards, and documentation sites. HTML is unavoidable and essential.

Topics
------

1.  HTML Document Structure And Syntax
2.  Essential HTML Elements And Attributes
3.  Semantic HTML For Better Accessibility
4.  Portfolio Page
5.  HTML In The Browser

1 | Understanding HTML Fundamentals
-----------------------------------

### What Exactly is HTML?

HTML is a **markup** language, not a _programming_ language. The difference:

**Programming Languages** _(JavaScript, Python)_:

*   Give instructions: “If user clicks button, do this”
*   Make decisions: “If password is wrong, show error”
*   Perform calculations: “Add these numbers”

**Markup Languages** _(HTML)_:

*   Describe structure: “This is a heading”
*   Define content: “This is a paragraph”
*   Create relationships: “This link goes to that page”

### The Basic HTML Pattern

Every HTML document follows the same structure. Let’s break it down:

```
html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
</head>
<body>
    <!-- Your content goes here -->
</body>
</html>
```

**Let’s decode this like a letter:**

```
<!DOCTYPE html>              ← "This envelope contains HTML"
<html lang="en">             ← "Written in English"
<head>                       ← The envelope (metadata)
    <meta charset="UTF-8">   ← "Use all international characters"
    <title>Page Title</title>← "Letter subject line" (browser tab)
</head>
<body>                       ← The actual letter content
    <!-- Content here -->    ← Your message
</body>
</html>                      ← "End of letter"
```

### Understanding Tags

HTML uses tags to mark up content. Most tags come in pairs:

```
<tagname>Content goes here</tagname>
   ↑                          ↑
Opening tag            Closing tag
```

**Examples:**

```
<h1>This is a heading</h1>
<p>This is a paragraph.</p>
<a href="url">This is a link</a>
```

**Self-closing tags (no content inside):**

```
<img src="photo.jpg" alt="Description">
<br>  <!-- Line break -->
<hr>  <!-- Horizontal rule/line -->
```

2 | Essential HTML Elements
---------------------------

### Headings

**The Hierarchy**

Headings create structure, like chapter titles in a book:

```
<h1>Main Title (Use only ONE per page)</h1>
<h2>Major Section</h2>
<h3>Subsection</h3>
<h4>Sub-subsection</h4>
<h5>Rarely used</h5>
<h6>Very rarely used</h6>
```

**Example:**

```
<h1>My Cloud Portfolio</h1>
    <h2>About Me</h2>
        <h3>My Background</h3>
        <h3>My Goals</h3>
    <h2>Skills</h2>
        <h3>Cloud Platforms</h3>
        <h3>Programming Languages</h3>
```

**_Think of headings like an outline. Don’t skip levels (don’t go from h1 to h3 without an h2 in between)._**

### Paragraphs and Text

```
<!-- Paragraphs -->
<p>This is a paragraph. It's the most common text container.</p>
<p>Each paragraph tag creates a new block of text with spacing.</p>
<!-- Line break within a paragraph -->
<p>First line<br>Second line (same paragraph)</p>
<!-- Bold and italic -->
<strong>This text is important (bold)</strong>
<em>This text is emphasized (italic)</em>
<!-- Code snippets -->
<code>const greeting = "Hello World";</code>
<!-- Highlighted text -->
<mark>This text is highlighted</mark>
```

### Links

**Connecting Pages**

Links are how the web became a “_web_”:

```
<!-- External link (opens another website) -->
<a href="https://aws.amazon.com">Visit AWS</a>
<!-- Internal link (jumps to section on same page) -->
<a href="#contact">Jump to Contact Section</a>
<!-- Email link -->
<a href="mailto:you@example.com">Email Me</a>
<!-- Open in new tab -->
<a href="https://github.com" target="_blank">My GitHub</a>
```

**Anatomy of a link:**

```
<a href="destination" target="_blank" title="Tooltip text">
   Link text that user clicks
</a>
href = "hypertext reference" (where it goes)
target = where to open (same tab or new tab)
title = tooltip on hover
```

### Images

**Visual Content**

```
<!-- Basic image -->
<img src="profile-photo.jpg" alt="My professional headshot">\
<!-- Image with dimensions -->
<img src="logo.png" alt="Company logo" width="200" height="100">
<!-- Image as a link -->
<a href="https://linkedin.com/in/yourname">
    <img src="linkedin-icon.png" alt="LinkedIn profile">
</a>
```

**Always include alt text:**

*   Screen readers use it for visually impaired users
*   Shows if image fails to load
*   Helps search engines understand your images

### Lists

**Organizing Information**

Unordered lists (bullet points)

```
<ul>
    <li>AWS</li>
    <li>Azure</li>
    <li>Google Cloud</li>
</ul>
```

Ordered lists (numbered):

```
<ol>
    <li>Set up AWS account</li>
    <li>Create S3 bucket</li>
    <li>Upload website files</li>
    <li>Enable static hosting</li>
</ol>
```

Nested lists:

```
<ul>
    <li>Cloud Platforms
        <ul>
            <li>AWS - Intermediate</li>
            <li>Azure - Beginner</li>
        </ul>
    </li>
    <li>Programming
        <ul>
            <li>HTML - Advanced</li>
            <li>CSS - Learning</li>
        </ul>
    </li>
</ul>
```

### Containers

**Grouping Content**

Generic container:

```
<div class="skills-section">
    <h2>My Skills</h2>
    <p>I'm learning cloud computing...</p>
</div>
```

Semantic containers (better for accessibility and SEO):

```
<header>Top of page navigation</header>
<nav>Navigation menu</nav>
<main>Primary page content</main>
<section>Thematic grouping</section>
<article>Self-contained content</article>
<aside>Sidebar or tangential content</aside>
<footer>Bottom of page info</footer>
```

3 | Semantic HTML
-----------------

### What is Semantic HTML?

Non-semantic (bad):

```
<div id="header">
    <div id="menu">
        <div class="menu-item">Home</div>
        <div class="menu-item">About</div>
    </div>
</div>
<div id="content">
    <div class="post">Blog post content</div>
</div>
<div id="footer">Copyright info</div>
```

Semantic (good):

```
<header>
    <nav>
        <a href="/">Home</a>
        <a href="/about">About</a>
    </nav>
</header>
<main>
    <article>Blog post content</article>
</main>
<footer>Copyright info</footer>
```

### Why Semantic HTML Matters

Benefits:

1.  **Accessibility:** Screen readers understand page structure
2.  **SEO:** Search engines rank semantic HTML higher
3.  **Maintainability:** Other developers understand your code faster
4.  **Future-proof:** Works better with new technologies

Real-world impact:

*   Google favors semantic HTML in search rankings
*   15% of the world has some form of disability that benefits from semantic markup
*   Hiring managers look for semantic HTML in portfolio code

4 | Forms
---------

### Basic Form Structure

```
<form action="/submit" method="POST">
    
    <!-- Text input -->
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required>
    
    <!-- Email input -->
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required>
    
    <!-- Password input -->
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required>
    
    <!-- Textarea for longer text -->
    <label for="message">Message:</label>
    <textarea id="message" name="message" rows="4"></textarea>
    
    <!-- Checkbox -->
    <input type="checkbox" id="subscribe" name="subscribe">
    <label for="subscribe">Subscribe to newsletter</label>
    
    <!-- Radio buttons -->
    <input type="radio" id="aws" name="cloud" value="aws">
    <label for="aws">AWS</label>
    
    <input type="radio" id="azure" name="cloud" value="azure">
    <label for="azure">Azure</label>
    
    <!-- Submit button -->
    <button type="submit">Send</button>
</form>
```

### Input Types

```
<input type="text">       <!-- Plain text -->
<input type="email">      <!-- Validates email format -->
<input type="password">   <!-- Hides characters -->
<input type="number">     <!-- Number input -->
<input type="date">       <!-- Date picker -->
<input type="tel">        <!-- Phone number -->
<input type="url">        <!-- Website URL -->
<input type="file">       <!-- File upload -->
```
6 | Understanding What You Built
--------------------------------

### The Document Structure (Tree Model)

Your HTML creates a tree structure:

```
html
├── head
│   ├── meta (charset)
│   ├── meta (viewport)
│   ├── title
│   └── meta (description)
└── body
    ├── header
    │   └── nav
    │       ├── h1
    │       ├── p
    │       └── ul
    │           ├── li → a
    │           ├── li → a
    │           ├── li → a
    │           └── li → a
    ├── main
    │   ├── section (hero)
    │   ├── section (about)
    │   ├── section (skills)
    │   │   └── article (× 11 skills)
    │   ├── section (projects)
    │   │   └── article (× 3 projects)
    │   └── section (contact)
    │       └── form
    └── footer
```

This is called the **DOM** (_Document Object Model)_. Understanding this tree structure is crucial for:

*   CSS styling (next post)
*   JavaScript manipulation (later)
*   Debugging issues

### Semantic Choices We Made

Why we used these specific tags:

```
<header>    ← Top of page (navigation)
<nav>       ← Navigation links
<main>      ← Primary content (only one per page)
<section>   ← Thematic content groupings
<article>   ← Self-contained pieces (skills, projects)
<footer>    ← Bottom of page (copyright, links)
```

Non-semantic alternative (avoid this):

```
<div class="header">
<div class="nav">
<div class="main">
<div class="section">
<div class="article">
<div class="footer">
```

Both work visually, but semantic HTML is:

*   Better for **accessibility:** _screen readers_
*   Better for **SEO:** _search engines_
*   Better for **maintainability:** _other developers_

7 | Common HTML Mistakes And How to Avoid Them
----------------------------------------------

### Mistake 01: Not Closing Tags

```
<!-- ❌ Wrong -->
<p>This is a paragraph
<p>This is another paragraph
<!-- ✅ Correct -->
<p>This is a paragraph</p>
<p>This is another paragraph</p>
```

### Mistake 02: Nesting Tags Incorrectly

```
<!-- ❌ Wrong -->
<strong><em>Bold and italic</strong></em>
<!-- ✅ Correct -->
<strong><em>Bold and italic</em></strong>
```

**Rule:** _Last opened, first closed (like Russian nesting dolls)_

### Mistake 03: Using Multiple H1 Tags

```
<!-- ❌ Wrong -->
<h1>My Portfolio</h1>
<h1>About Me</h1>
<h1>Projects</h1>
<!-- ✅ Correct -->
<h1>My Portfolio</h1>
<h2>About Me</h2>
<h2>Projects</h2>
```

**Rule:** _Only ONE <h1> per page (your main page title)_

### Mistake 04: Forgetting Alt Text on Images

```
<!-- ❌ Wrong -->
<img src="profile.jpg">
<!-- ✅ Correct -->
<img src="profile.jpg" alt="Professional headshot of Your Name">
```

### Mistake 05: Using Divs for Everything

```
<!-- ❌ Wrong (div soup) -->
<div class="navigation">...</div>
<div class="main-content">...</div>
<div class="bottom">...</div>
<!-- ✅ Correct (semantic) -->
<nav>...</nav>
<main>...</main>
<footer>...</footer>
```

8 | Update Your Learning Journal
--------------------------------

Add this to your learning-journal.md (personalize it):

```
## Day 3: HTML - Building Web Structure
### Date: [Today's Date]
### What I Learned:
#### HTML Fundamentals:
- HTML is a markup language (describes structure, not instructions)
- Every HTML document follows the same basic pattern (DOCTYPE, html, head, body)
- Tags come in pairs: opening and closing
- Some tags are self-closing (img, br, hr)
#### Essential Elements Mastered:
- **Structure:** html, head, body, header, nav, main, section, article, footer
- **Content:** h1-h6, p, a, img, ul, ol, li
- **Forms:** form, input, textarea, button, label
- **Semantic containers:** nav, main, section, article, aside, footer
#### Key Concepts:
1. **Semantic HTML:** Using meaningful tags (nav, main, article) instead of generic divs
2. **Accessibility:** Alt text, proper heading hierarchy, semantic structure
3. **DOM Tree:** HTML creates a tree structure of parent/child elements
4. **Validation:** Always validate HTML with W3C validator
### Portfolio Progress:
✅ **Complete HTML Structure:**
- Navigation header with 4 links
- Hero section with call-to-action
- About section with bio
- Skills tracker showing 3/11 complete
- Projects showcase with 3 project cards
- Contact form with 5 input fields
- Footer with links and copyright
✅ **Accessibility Features:**
- Semantic HTML throughout
- Alt text ready for images
- Proper heading hierarchy (h1 → h2 → h3)
- Form labels linked to inputs
- Descriptive link text
### Challenges Faced:
- Initially confused about when to use `<div>` vs `<section>` vs `<article>`
- Learned: section = thematic grouping, article = standalone content, div = last resort
- Had to remember to close all tags properly
- Needed to double-check HTML validator for syntax errors
### Aha! Moments:
- 💡 HTML is like the outline of an essay - it's all about structure!
- 💡 Semantic tags make code self-documenting
- 💡 The browser is very forgiving (shows page even with errors), but I should still validate
- 💡 Forms don't work without backend processing (need JavaScript/Python later)
### Real-World Connections:
- AWS Console is built with HTML
- Cloud documentation sites use semantic HTML
- Every dashboard or monitoring tool I'll build needs HTML foundations
### Testing Completed:
- ✅ Opened index.html in Chrome, Firefox, Safari
- ✅ Tested all navigation anchor links
- ✅ Verified form displays correctly
- ✅ Ran W3C HTML Validator - 0 errors
- ✅ Checked that all semantic elements are properly nested
### Metrics:
- **Lines of HTML written:** ~250
- **Elements used:** 30+ different tags
- **Validation errors:** 0 (after fixes)
- **Time invested:** 2.5 hours
### Next Steps:
✅ Completed: HTML structure and content
⏳ Next: CSS to style this plain HTML into a professional portfolio
📅 Goal: Transform black-and-white text into a visually stunning website
### Questions for Further Research:
- How do professional developers organize large HTML files?
- What are HTML5 semantic elements I haven't used yet?
- How does HTML relate to accessibility compliance (WCAG)?
- What's the difference between block and inline elements?
### Daily Reflection:
Today I built something **real** - an actual website that works in a browser! Yes, it's plain and unstyled, but it's structured correctly and follows best practices. I can see how HTML is the foundation for everything on the web. Excited to make it look professional with CSS tomorrow. The 11-week journey is feeling very achievable now! 💪
### Progress Summary:
- **Cumulative learning time:** 7 hours
- **Days completed:** 3/60
- **Skills mastered:** 3/11 (English, Math, HTML)
- **Percentage complete:** 5% of journey
- **Momentum:** Strong! 🚀
```

9 | Practice Exercises
----------------------

### Exercise 01: Add Your Personal Information

Task: Customize the portfolio with your real information

1.  Replace “Your Name” with your actual name
2.  Add a real email address in the contact section
3.  Write a unique bio in the About section (3–4 sentences)
4.  If you have them, add your LinkedIn, GitHub, Twitter links

### Exercise 02: Add a New Section

Task: Create a “Blog” section

```
<section id="blog">
    <h2>Learning Blog</h2>
    <p>Documenting my daily progress:</p>
    
    <article class="blog-post">
        <h3>Day 3: Built My First Website!</h3>
        <p><time datetime="2026-01-15">January 15, 2026</time></p>
        <p>
            Today I created the HTML structure for my portfolio. 
            It's unstyled, but it works! Next up: CSS styling.
        </p>
    </article>
</section>
```

_Add this between your Projects and Contact sections._

### Exercise 03: Create a Second Page

Task: Create an about.html page with more detailed information

1.  Create a new file: about.html
2.  Copy the basic HTML structure from index.html
3.  Create a detailed About page with:

*   Your background
*   Why you’re learning cloud computing
*   Your learning methodology
*   Your career goals

Link to it from your navigation:

```
<li><a href="about.html">About</a></li>
```

10 | Troubleshooting Common Issues
----------------------------------

### Issue 01: Images Not Showing

Problem: **<img src=”profile.jpg” alt=”My photo”>** shows broken image

Solutions:

```
<!-- ✅ Check file path is correct -->
<img src="assets/images/profile.jpg" alt="My photo">
<!-- ✅ Ensure image file exists in that location -->
<!-- ✅ Check file extension matches (jpg vs jpeg vs png) -->
<img src="assets/images/profile.png" alt="My photo">
```

### Issue 02: Links Not Working

Problem: Clicking navigation links doesn’t jump to sections

Solution: Make sure href matches id:

```
<!-- ❌ Won't work -->
<a href="#contact">Contact</a>
<section id="contact-section">
<!-- ✅ Works -->
<a href="#contact">Contact</a>
<section id="contact">
```

### Issue 03: Form Submits But Nothing Happens

This is normal! Forms need backend processing:

*   **JavaScript** (to handle on the page)
*   Or server-side language (Python, PHP, Node.js)

We’ll add this functionality in later posts.

For now, prevent form submission with:

```
<form onsubmit="return false;">
```

### Final Thoughts

1.  **HTML is structure, not style:** It describes what something is, not how it looks
2.  **Semantic HTML matters:** Use meaningful tags (nav, main, article) instead of divs
3.  **Validation catches errors:** Always validate your HTML with W3C
4.  **Accessibility is essential:** Alt text, proper hierarchy, semantic structure help everyone
5.  **DOM is a tree:** Understanding parent/child relationships is crucial for CSS and JavaScript

Concluding Remarks
------------------

You’ve just built your first real website! Yes, it’s plain right now, but:

✅ It’s properly structured

✅ It’s semantic and accessible

✅ It’s validated and error-free

✅ It’s ready for professional styling

You’re not just learning HTML, you’re building a real portfolio that will help you get hired in cloud computing.

Take a moment to appreciate what you’ve accomplished. Three days ago, you started with zero knowledge. Now you have a working website structure.

**Next up:** We’ll make it beautiful with CSS. Get ready to see your portfolio transform! 🚀

![HTML — The Markup Language](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*JVw6gXzNYpnvlkyqXWdaNA.png)

Additional Resources
--------------------

### [Cloud Glossary](https://medium.com/list/cloud-glossary-528956a3c181)

> Cloud Computing Simplified: A Cloud Glossary For Beginners

### [Cloud Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34703)

> In a world where nearly every industry is moving toward cloud-first strategies, having a baseline understanding of these principles is no longer optional but essential.

### [AI Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34979)

> Whether you’re an Executive, Developer, Engineer, or Project Manager, having a baseline understanding of AI principles is no longer a _nice to have_, it’s **absolutely essential.**

### [MDN HTML Tutorial](https://developer.mozilla.org/en-US/docs/Learn/HTML)

> The gold standard reference

### [W3Schools HTML](https://www.w3schools.com/html/)

> Interactive examples

### [HTML Validator](https://validator.w3.org/)

> Check your code

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [HTML in the Cloud](https://medium.com/@ntombizakhona/html-in-the-cloud-dc23a04bfe51)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**29 January 2026**
