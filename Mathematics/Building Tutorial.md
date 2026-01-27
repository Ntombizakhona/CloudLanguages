### 🏗️ Let’s Build

Plan Your Cloud Portfolio with Mathematical Thinking
----------------------------------------------------

Let’s apply everything you’ve learned to plan your portfolio website systematically.

### Step 01: Define the Problem (Like a Math Word Problem)

**Given:**

*   You need a portfolio to showcase cloud computing skills
*   Recruiters spend an average of 7 seconds on first impression
*   Portfolio must be accessible online 24/7
*   Budget: $0–5/month for hosting

Find:

*   Website structure that highlights your strengths
*   Optimal user experience flow
*   Deployment solution within budget

Solution Strategy:
**Apply decomposition, Boolean logic, and algorithms**

### Step 02: Create Your User Journey Algorithm

```
ALGORITHM: Portfolio User Experience
``````
**INPUT: Visitor arrives at your website URL**
``````
**PROCESS:
**1. LOAD homepage (< 3 seconds for good UX)
   │
2. DISPLAY hero section with your name and title
   ├─ IF visitor likes what they see → THEN continue scrolling
   └─ IF visitor not interested → THEN leave (we want to minimize this!)
   │
3. PRESENT "About Me" section
   ├─ Show professional photo
   ├─ Display compelling bio
   └─ Highlight cloud computing passion
   │
4. SHOWCASE skills with visual progress bars
   ├─ Cloud platforms: AWS (80%), Azure (60%), GCP (40%)
   ├─ Languages: HTML (90%), CSS (85%), JavaScript (75%)
   └─ Tools: Git (70%), Docker (50%)
   │
5. DISPLAY projects portfolio
   ├─ FOR each project:
   │   ├─ Show project thumbnail
   │   ├─ Display project description
   │   ├─ Add "View Live" button
   │   └─ Add "View Code" button (GitHub link)
   │
6. OFFER contact form
   ├─ IF form is valid → SEND email → SHOW success message
   └─ IF form is invalid → HIGHLIGHT errors → PROMPT correction
   │
**OUTPUT: Visitor impressed, contacts you, or bookmarks your site**
``````
**SUCCESS METRICS:`**
- Time on site > 2 minutes = engaged visitor
- Contact form submission = potential opportunity
- GitHub profile click = technically interested recruiter
```

### Step 03: Apply Boolean Logic to Content Display

```
// Decide what to show based on visitor behavior
function displayContent(section, scrollDepth, timeOnSite, deviceType) {
    
    // Show animated skill bars only when visitor scrolls to skills section
    if (section === "skills" && scrollDepth > 40) {
        animateSkillBars(); // Makes it more engaging
    }
    
    // Show contact CTA if visitor is engaged
    if (timeOnSite > 120 && scrollDepth > 80) {
        showContactCallToAction(); // They're interested!
    }
    
    // Optimize for mobile
    if (deviceType === "mobile") {
        showMobileMenu(); // Hamburger menu instead of full nav
        reduceFontSize(); // Better readability on small screens
    } else {
        showDesktopMenu(); // Full navigation bar
        useFullFontSize();
    }
    
    // Show GitHub link only if visitor viewed projects
    if (section === "projects" && timeOnSite > 60) {
        highlightGitHubButton(); // Encourage them to see your code
    }
}
```

### Step 4: Calculate Your Learning Path (Using Math!)

Let’s create a realistic learning timeline using basic math:

**Formula: Total Learning Time = Σ (Topic Hours × Complexity Factor**

```
const learningPlan = {
    html: { hours: 15, complexityFactor: 1.0 },     // Beginner-friendly
    css: { hours: 20, complexityFactor: 1.2 },      // Slightly more complex
    javascript: { hours: 40, complexityFactor: 1.5 }, // More challenging
    git: { hours: 10, complexityFactor: 1.1 },      // New concept but manageable
    aws: { hours: 30, complexityFactor: 1.4 }       // Lots of services to learn
};
function calculateLearningTime(plan) {
    let totalHours = 0;
    
    for (let topic in plan) {
        const adjustedHours = plan[topic].hours * plan[topic].complexityFactor;
        totalHours += adjustedHours;
        
        console.log(`${topic}: ${adjustedHours} hours`);
    }
    
    return totalHours;
}
// Calculate total
const total = calculateLearningTime(learningPlan);
console.log(`Total learning time: ${total} hours`);
// If you study 2 hours per day:
const hoursPerDay = 2;
const daysNeeded = total / hoursPerDay;
console.log(`Days needed (at 2hrs/day): ${daysNeeded} days`);
console.log(`Weeks needed: ${(daysNeeded / 7).toFixed(1)} weeks`);
/* Output:
html: 15 hours
css: 24 hours
javascript: 60 hours
git: 11 hours
aws: 42 hours
Total learning time: 152 hours
Days needed (at 2hrs/day): 76 days
Weeks needed: 10.9 weeks
*/
```

**_Key Insight: With consistent 2-hour daily study, you’ll have cloud portfolio skills in about 11 weeks. That’s achievable!_**

### Step 05: Portfolio Structure Blueprint

Let’s create a mathematical breakdown of your portfolio:

```
## Portfolio Website Mathematics
### Content Distribution (Percentages must add to 100%)
Hero/Header: 15% of page
About Me: 20% of page
Skills: 25% of page (most important!)
Projects: 30% of page (showcase your work)
Contact: 10% of page
Total: 15 + 20 + 25 + 30 + 10 = 100% ✓
### Color Scheme (Using ratios)
Primary Color (60%): Professional blue (#2C3E50)
Secondary Color (30%): Accent teal (#16A085)
Accent Color (10%): Highlight gold (#F39C12)
Ratio: 60:30:10 (industry-standard design ratio)
### Responsive Breakpoints (Media queries in pixels)
- Mobile: 0 - 768px (smartphones)
- Tablet: 769 - 1024px (iPads)
- Desktop: 1025px+ (laptops, monitors)
IF screen width ≤ 768px THEN apply mobile styles
ELSE IF screen width ≤ 1024px THEN apply tablet styles
ELSE apply desktop styles
```

### Step 06: Update Your Learning Journal (With Math!)

**Add this to your learning-journal.md**

```
## Day 2: Mathematics: Logic and Problem Solving
### Date: [Today's Date]
### What I Learned:
#### Mathematical Concepts:
1. **Linear Equations (y = mx + c)**
   - Applied to AWS cost calculator: `Cost = (pricePerGB × storage) + baseFee`
   - Learned how slopes represent rates of change in programming
   
2. **Boolean Logic (AND, OR, NOT)**
   - Everything in computing is TRUE or FALSE
   - Created truth tables for authentication logic
   - Applied to portfolio content display decisions
   
3. **Percentages and Ratios**
   - Resource utilization: `(used / total) × 100 = percentage`
   - Design ratios: 60:30:10 color scheme
   - Content distribution across portfolio sections
#### Problem-Solving Techniques:
1. **Algorithms**: Step-by-step recipes for solving problems
   - Created coffee-making algorithm
   - Designed AWS deployment algorithm
   - Built user journey algorithm for portfolio
2. **Decomposition**: Breaking big problems into small pieces
   - "Build portfolio" → 5 major components → 20+ subtasks
   - Calculated total project time: 15 hours
   - Made intimidating project feel achievable!
3. **Pattern Recognition**: Seeing similarities across different problems
   - Recognized authentication pattern (AWS, GitHub, portfolio form)
   - Identified CRUD pattern (Create, Read, Update, Delete)
   - Same logic applies to different technologies
### Portfolio Planning Progress:
✅ **Structure Defined:**
- Header (15%), About (20%), Skills (25%), Projects (30%), Contact (10%)
✅ **User Journey Mapped:**
- Visitor lands → Views hero → Reads about → Sees skills → Checks projects → Contacts
✅ **Learning Timeline Calculated:**
- Total hours needed: 152 hours
- At 2 hours/day: 76 days (~11 weeks)
- Realistic and achievable goal set!
### Mathematical Problems I Solved Today:
1. **AWS Cost Calculation:**
```javascript
function calculateCost(storageGB) {
    return (0.023 * storageGB) + 5;
}
// 100GB = $7.30/month
2. **CPU Usage Alert:**
function checkUsage(used, total) {
    const percent = (used / total) * 100;
    return percent > 80 ? "WARNING" : "NORMAL";
}
3. Learning Time Projection:
Total hours = Σ(topic hours × complexity)
= 152 hours
= 11 weeks at 2hrs/day
```

### ⛔ End of Building Tutorial⛔


# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Mathematics In The Cloud](https://ntombizakhona.medium.com/mathematics-in-the-cloud-2aa05addeb68?postPublishedType=initial)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**22 January 2026**
