Python in the Cloud
===================

The Language of Backend Logic And Automation
--------------------------------------------

You’ve built a beautiful, interactive website. But there’s a problem: it can’t actually do anything beyond the browser.

Your contact form? It doesn’t send emails.
Your portfolio data? Hardcoded in HTML.
Deployment? Manual file uploads.

**Python** changes everything.

Think of your portfolio so far:

*   Frontend **(HTML/CSS/JS)**: _What users see and interact with_
*   Backend **(Python)**: _The brain that processes, stores, automates, and connects to the cloud_

Python is the number one language for cloud computing because:

*   **AWS Lambda?** _Python is the most popular runtime_
*   **Automation scripts?** _Python dominates DevOps_
*   **Data processing?** _Python with pandas, NumPy_
*   **Machine Learning?** _Python with TensorFlow, PyTorch_
*   **API development?** _Python with Flask, FastAPI, Django_

**_Every major cloud provider has first-class Python support. If you want to work in cloud computing, Python is non-negotiable._**

Topics
------

1.  Python syntax and core concepts
2.  Files, JSON, and data structures
3.  Automation scripts for repetitive tasks
4.  RESTful APIs with Flask
5.  Process form data and send emails
6.  Connect Python backend to your JavaScript frontend
7.  Deploy Python to AWS Lambda
8.  Automate portfolio deployment with scripts

1 | Why Python
--------------

### JavScript vs Python

**_“I just learned_** [**_JavaScript._**](https://medium.com/javascript-in-the-cloud-6dd068fecb77) **_Why Python too?”_**

While JavaScript and Python are both popular programming languages, they excel in different areas and environments.

JavaScript runs primarily in the browser on the client-side, making it ideal for building user interfaces and adding interactivity to websites, whereas Python runs on the server-side (backend) and excels at data processing, automation, and API development.

Syntactically, JavaScript follows a C-like style with curly braces and semicolons, while Python uses clean, readable indentation-based syntax that forces code organization.

JavaScript has asynchronous programming built into the language core with Promises and async/await, while Python requires additional libraries like asyncio for similar functionality.

In cloud computing environments, JavaScript powers AWS Lambda functions and Node.js servers, while Python dominates in AWS Lambda, automation scripts, and machine learning applications.

Both languages use dynamic typing, though Python offers optional type hints for improved code documentation and IDE support, making it slightly more explicit for large-scale projects.

### Example:

**Frontend (JavaScript)**

```
// User clicks "Calculate Cost" button
button.addEventListener('click', async () => {
    const storage = document.querySelector('#storage').value;
    
    // Send request to Python backend
    const response = await fetch('http://localhost:5000/api/calculate-cost', {
        method: 'POST',
        body: JSON.stringify({ storageGB: storage })
    });
    
    const result = await response.json();
    displayCost(result.totalCost);
});
```

**Backend (Python**

```
# Python receives request, calculates, returns result
@app.route('/api/calculate-cost', methods=['POST'])
def calculate_cost():
    data = request.json
    storage_gb = data['storageGB']
    
    # Complex calculation (same math from Day 2!)
    ec2_cost = 0.0116 * 730  # m × x
    db_cost = 0.034 * 730
    storage_cost = 0.023 * storage_gb
    total = ec2_cost + db_cost + storage_cost  # y = mx + c
    
    return jsonify({'totalCost': round(total, 2)}
```

**_See? JavaScript handles user interaction, Python handles heavy computation._**

2 | Python Fundamentals
-----------------------

### The Basics

**Installing Python**

Check if you have Python:

```
python --version
# or
python3 --version
```

If not installed:

*   **Windows:** Download from [python.org](https://www.python.org/downloads/)
*   **Mac:** brew install python3 (if you have Homebrew)
*   **Linux:** Usually pre-installed, or sudo apt install python3

### Your First Python Program

Create hello.py:

```
# This is a comment (like // in JavaScript)
# Print to console
print("Hello, Cloud Computing!")
# Variables (no let, const, or var needed!)
name = "Cloud Developer"
age = 35
is_learning = True
# Print with f-strings (like template literals in JS)
print(f"My name is {name} and I'm {age} years old")
# Run this file: python hello.py
```

Run it:

```
python hello.py
# Output: Hello, Cloud Computing!
#         My name is Cloud Developer and I'm 35 years old
```

3 | Python Syntax
-----------------

### Variables and Data Types

```
# Python is dynamically typed (like JavaScript)
# No need to declare type
# Strings
name = "Cloud Developer"
bio = 'Learning Python for cloud computing'
multiline = """This is a multiline string"""
# Numbers
age = 25
price = 19.99
percentage = 0.85
# Booleans (note: capitalized True/False, unlike JavaScript's true/false)
is_active = True
has_access = False
# Lists (like JavaScript arrays)
skills = ["HTML", "CSS", "JavaScript", "Python"]
numbers = [1, 2, 3, 4, 5]
# Dictionaries (like JavaScript objects)
user = {
    "name": "Cloud Developer",
    "age": 25,
    "skills": ["AWS", "Python", "JavaScript"],
    "is_active": True
}
# Tuples (immutable lists)
coordinates = (10, 20)
# Sets (unique values only)
unique_skills = {"Python", "JavaScript", "Python"}  # Second "Python" ignored
print(unique_skills)  # {'Python', 'JavaScript'}
# None (like JavaScript's null)
result = None
```

4 | Control Flow and Functions
------------------------------

### **If/Else Statements**

⚠️**Indentation Matters!** ⚠️

```
# Python uses indentation instead of { } braces
# This is NOT just style, it's syntax!
age = 25
if age >= 18:
    print("You can vote")
    print("You're an adult")
else:
    print("Too young to vote")
# Multiple conditions
score = 85
if score >= 90:
    print("Grade: A")
elif score >= 80:  # Note: elif, not "else if"
    print("Grade: B")
elif score >= 70:
    print("Grade: C")
else:
    print("Grade: F")
# Boolean logic (from Day 2!)
is_logged_in = True
is_admin = True
if is_logged_in and is_admin:  # and (not &&)
    print("Access granted to admin panel")
elif is_logged_in or is_admin:  # or (not ||)
    print("Partial access")
else:
    print("Access denied")
```

### Functions

```
# Basic function
def greet_user(name):
    return f"Hello, {name}!"
# Call it
message = greet_user("Cloud Developer")
print(message)  # Hello, Cloud Developer!]
# Function with default parameter
def calculate_cost(storage_gb, rate=0.023):
    return storage_gb * rate
print(calculate_cost(100))       # Uses default rate: 2.3
print(calculate_cost(100, 0.05)) # Custom rate: 5.0
# Multiple return values (using tuple)
def get_user_info():
    name = "Cloud Developer"
    age = 25
    skills = ["Python", "JavaScript"]
    return name, age, skills
# Unpack return values
user_name, user_age, user_skills = get_user_info()
print(f"{user_name} is {user_age} years old")
# Type hints (optional, but good practice)
def add_numbers(a: int, b: int) -> int:
    return a + b
# Lambda functions (like arrow functions in JS)
# JavaScript: const square = (x) => x * x
square = lambda x: x * x
print(square(5))  # 25
```

### AWS Cost Calculator

Remember this JavaScript?

```
function calculateAWSCost(storageGB) {
    const ec2Rate = 0.0116;
    const dbRate = 0.034;
    const storageRate = 0.023;
    const hours = 730;
    
    return (ec2Rate * hours) + (dbRate * hours) + (storageRate * storageGB);
}
```

**Python version:**

```
def calculate_aws_cost(storage_gb: int, hours: int = 730) -> float:
    """
    Calculate monthly AWS costs.
    Formula: y = mx + c (from Day 2!)
    """
    ec2_rate = 0.0116
    db_rate = 0.034
    storage_rate = 0.023
    
    # y = mx + c pattern
    ec2_cost = ec2_rate * hours        # m × x
    db_cost = db_rate * hours          # m × x
    storage_cost = storage_rate * storage_gb  # m × x
    
    total = ec2_cost + db_cost + storage_cost  # Sum all costs
    
    return round(total, 2)
# Test it
print(f"50GB costs: ${calculate_aws_cost(50)}/month")   # $34.62/month
print(f"200GB costs: ${calculate_aws_cost(200)}/month") # $38.07/month
```

**_Same math, different language! 🎓_**

5 | Loops and Iteration
-----------------------

### For Loops

```
# Loop through list
skills = ["HTML", "CSS", "JavaScript", "Python"]
for skill in skills:
    print(f"I know {skill}")
# Loop with index
for index, skill in enumerate(skills):
    print(f"{index + 1}. {skill}")
# 1. HTML
# 2. CSS
# 3. JavaScript
# 4. Python
# Range (like for(let i=0; i<5; i++) in JS)
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4
for i in range(1, 6):
    print(i)  # 1, 2, 3, 4, 5
for i in range(0, 10, 2):
    print(i)  # 0, 2, 4, 6, 8 (step by 2)
```

### List Comprehensions

```
# JavaScript way:
# const numbers = [1, 2, 3, 4, 5];
# const doubled = numbers.map(n => n * 2);
# Python list comprehension:
numbers = [1, 2, 3, 4, 5]
doubled = [n * 2 for n in numbers]
print(doubled)  # [2, 4, 6, 8, 10]
# With condition
even_numbers = [n for n in numbers if n % 2 == 0]
print(even_numbers)  # [2, 4]
# More complex
skills = ["HTML", "CSS", "JavaScript", "Python"]
uppercase_skills = [skill.upper() for skill in skills]
print(uppercase_skills)  # ['HTML', 'CSS', 'JAVASCRIPT', 'PYTHON']
# Nested comprehension
matrix = [[i * j for j in range(3)] for i in range(3)]
print(matrix)  # [[0, 0, 0], [0, 1, 2], [0, 2, 4]]
```

### While Loops

```
count = 0
while count < 5:
    print(count)
    count += 1
# 0, 1, 2, 3, 4
# Break and continue
for num in range(10):
    if num == 5:
        break  # Exit loop
    if num % 2 == 0:
        continue  # Skip to next iteration
    print(num)  # 1, 3
```

6 | Working with Files and JSON
-------------------------------

### Reading and Writing Files

```
# Write to file
with open('notes.txt', 'w') as file:
    file.write('Learning Python for cloud computing\n')
    file.write('Python is awesome!\n')
# Read from file
with open('notes.txt', 'r') as file:
    content = file.read()
    print(content)
# Read line by line
with open('notes.txt', 'r') as file:
    for line in file:
        print(line.strip())  # strip() removes newline
# Append to file
with open('notes.txt', 'a') as file:
    file.write('Added a new line!\n')
```

**_Why with? It automatically closes the file even if an error occurs (like finally in try/catch)._**

### Working with JSON

```
import json
# Python dictionary → JSON file
portfolio_data = {
    "name": "Cloud Developer",
    "skills": [        {"name": "Python", "level": 75},
        {"name": "JavaScript", "level": 75},
        {"name": "HTML", "level": 100}
    ],
    "projects": [        {
            "name": "Portfolio Website",
            "technologies": ["HTML", "CSS", "JavaScript", "Python"]
        }
    ]
}
# Save to JSON file
with open('portfolio.json', 'w') as file:
    json.dump(portfolio_data, file, indent=2)
# Read from JSON file
with open('portfolio.json', 'r') as file:
    data = json.load(file)
    print(f"Portfolio for: {data['name']}")
    
# Convert to JSON string (for APIs)
json_string = json.dumps(portfolio_data, indent=2)
print(json_string)
# Parse JSON string
parsed = json.loads(json_string)
print(parsed['name'])
```

7 | Object-Oriented Programming
-------------------------------

### Classes and Objects

```
class CloudDeveloper:
    """Represents a cloud developer with skills"""
    
    # Class variable (shared by all instances)
    platform = "Cloud Computing"
    
    # Constructor (like constructor() in JavaScript classes)
    def __init__(self, name, skills=None):
        # Instance variables (unique to each object)
        self.name = name
        self.skills = skills if skills else []
        self.projects = []
    
    # Instance method
    def add_skill(self, skill, level=0):
        self.skills.append({"name": skill, "level": level})
        print(f"{self.name} learned {skill}!")
    
    def add_project(self, project_name):
        self.projects.append(project_name)
    
    def get_completed_skills(self):
        return [s for s in self.skills if s["level"] >= 75]
    
    # String representation
    def __str__(self):
        return f"CloudDeveloper(name={self.name}, skills={len(self.skills)})"
    
    # Representation for debugging
    def __repr__(self):
        return f"CloudDeveloper('{self.name}', {self.skills})"
# Create instance
developer = CloudDeveloper("Your Name", [    {"name": "HTML", "level": 100},
    {"name": "CSS", "level": 100}
])
# Call methods
developer.add_skill("Python", 75)
developer.add_project("Portfolio Website")
print(developer)  # CloudDeveloper(name=Your Name, skills=3)
print(f"Completed: {developer.get_completed_skills()}")
```

### Inheritance

```
class Skill:
    """Base class for all skills"""
    def __init__(self, name, category):
        self.name = name
        self.category = category
        self.level = 0
    
    def practice(self, hours):
        # Each hour increases skill by 1%, max 100%
        self.level = min(100, self.level + hours)
        return self.level
class ProgrammingLanguage(Skill):
    """Specialized skill for programming languages"""
    def __init__(self, name, syntax_style):
        super().__init__(name, "Programming")
        self.syntax_style = syntax_style
    
    def practice(self, hours):
        # Programming languages improve faster with practice
        self.level = min(100, self.level + (hours * 2))
        return self.level
# Usage
python = ProgrammingLanguage("Python", "indentation-based")
python.practice(10)
print(f"{python.name}: {python.level}%")  # Python: 20%
```

8 | Error Handling
------------------

```
# Try/except (like try/catch in JavaScript)
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero!")
# Multiple exceptions
try:
    file = open('nonexistent.txt', 'r')
    data = json.load(file)
except FileNotFoundError:
    print("File not found!")
except json.JSONDecodeError:
    print("Invalid JSON!")
except Exception as e:
    print(f"Unexpected error: {e}")
# Finally (always runs)
try:
    file = open('data.txt', 'r')
    content = file.read()
except FileNotFoundError:
    print("File not found")
finally:
    print("Cleanup complete")
# Raising exceptions
def update_skill_level(level):
    if level < 0 or level > 100:
        raise ValueError("Level must be between 0 and 100")
    return level
try:
    update_skill_level(150)
except ValueError as e:
    print(f"Error: {e}")
```

10 | Common Python Mistakes
---------------------------

### Mistake 01: Indentation Errors

```
# ❌ Wrong - inconsistent indentation
def greet():
 print("Hello")     # 1 space
   print("World")   # 3 spaces - ERROR!
# ❌ Wrong - mixing tabs and spaces
def greet():
    print("Hello")  # 4 spaces
	print("World")  # 1 tab - ERROR!
# ✅ Correct - consistent 4 spaces
def greet():
    print("Hello")
    print("World")
```

**Fix:** _Configure your editor to use 4 spaces for indentation, not tabs._

### Mistake 02: Forgetting Colons

```
# ❌ Wrong - missing colon after condition
if age > 18
    print("Adult")
# ❌ Wrong - missing colon after function definition
def greet()
    return "Hello"
# ✅ Correct
if age > 18:
    print("Adult")
def greet():
    return "Hello"
```

### Mistake 03: Mutable Default Arguments

```
# ❌ Wrong - list is shared between all calls!
def add_skill(skill, skills=[]):
    skills.append(skill)
    return skills
print(add_skill("Python"))      # ['Python']
print(add_skill("JavaScript"))  # ['Python', 'JavaScript'] - Unexpected!
# ✅ Correct - use None as default
def add_skill(skill, skills=None):
    if skills is None:
        skills = []
    skills.append(skill)
    return skills
print(add_skill("Python"))      # ['Python']
print(add_skill("JavaScript"))  # ['JavaScript'] - Correct!
```

### Mistake 04: Not Using with for Files

```
# ❌ Wrong - file might not close if error occurs
file = open('data.txt', 'r')
content = file.read()
file.close()  # Might never run if error above!
# ✅ Correct - automatically closes even if error
with open('data.txt', 'r') as file:
    content = file.read()
# File is automatically closed here
```

### Mistake 05: Modifying List While Iterating

```
# ❌ Wrong - modifying list while looping
skills = ["Python", "JavaScript", "HTML"]
for skill in skills:
    if skill == "JavaScript":
        skills.remove(skill)  # Dangerous!
# ✅ Correct - create new list or iterate over copy
skills = ["Python", "JavaScript", "HTML"]
# Option 1: List comprehension (best)
skills = [s for s in skills if s != "JavaScript"]
# Option 2: Iterate over copy
for skill in skills[:]:  # [:] creates a copy
    if skill == "JavaScript":
        skills.remove(skill)
```

### Mistake 06: Using == for None Comparison

```
# ❌ Not recommended
if value == None:
    print("Value is None")
# ✅ Correct - use 'is' for None
if value is None:
    print("Value is None")
if value is not None:
    print("Value exists")
```

### Mistake 07: Forgetting self in Methods

```
# ❌ Wrong - missing self parameter
class User:
    def __init__(name):  # Missing 'self'!
        name = name
    
    def greet():  # Missing 'self'!
        return f"Hello, {name}"
# ✅ Correct
class User:
    def __init__(self, name):
        self.name = name
    
    def greet(self):
        return f"Hello, {self.name}"
```

### Mistake 08: Import Errors

```
# ❌ Wrong - importing from wrong location
from flask import Flask, json  # 'json' is not from flask!
# ✅ Correct
from flask import Flask, jsonify
import json  # Standard library json
```

11 | Update Your Learning Journal
---------------------------------

Add to your learning-journal.md:

```
## Day 6: Python - Backend Logic and Cloud Automation
### Date: [Today's Date]
### What I Learned:
#### Python Fundamentals:
- **Syntax:** Clean, readable, indentation-based (no braces!)
- **Variables:** Simple declaration without let/const/var
- **Data types:** Lists, dictionaries, tuples, sets, None
- **Functions:** def keyword, default parameters, type hints
- **Classes:** Object-oriented programming with __init__ and self
- **Error handling:** try/except/finally blocks
#### File and Data Handling:
- **File I/O:** Using `with` statement for automatic cleanup
- **JSON:** json.load(), json.dump(), json.loads(), json.dumps()
- **Data structures:** Dictionaries for complex nested data
- **List comprehensions:** Powerful one-line filtering and mapping
#### Web Development with Flask:
- **Flask framework:** Lightweight Python web framework
- **RESTful APIs:** GET, POST, PUT, DELETE endpoints
- **Route decorators:** @app.route() for URL mapping
- **Request handling:** request.get_json(), request.args
- **Response formatting:** jsonify() for JSON responses
- **CORS:** Cross-Origin Resource Sharing with Flask-CORS
- **Error handlers:** Custom 404, 405, 500 handlers
#### Object-Oriented Programming:
- **Classes and objects:** Encapsulation and organization
- **Constructors:** __init__ method for initialization
- **Instance methods:** Functions that operate on object data
- **Class variables:** Shared across all instances
- **Inheritance:** Extending base classes with super()
### Portfolio Transformation:
**Before Python (Days 1-5):**
- Beautiful frontend ✓
- Interactive JavaScript ✓
- **No backend** ✗
- Hardcoded data ✗
- No data persistence ✗
- Contact form doesn't work ✗
**After Python (Day 6):**
- ✅ Full RESTful API with 12+ endpoints
- ✅ Portfolio data manager with JSON persistence
- ✅ Contact form backend processing
- ✅ Skill progress tracking system
- ✅ Learning log with timestamps
- ✅ Analytics and progress reports
- ✅ AWS cost calculator endpoint
- ✅ Frontend-backend integration
- ✅ Proper error handling and validation
- ✅ API documentation at root endpoint)
### Aha! Moments:
- 💡 Python's simplicity is its superpower—no semicolons, braces, or cruft!
- 💡 Flask makes API development ridiculously easy (< 50 lines for basic API)
- 💡 List comprehensions replace map() and filter() in one readable line
- 💡 The `with` statement is brilliant for automatic resource cleanup
- 💡 Python's `self` is explicit (unlike JavaScript's implicit `this`)
- 💡 Type hints make Python code self-documenting without runtime overhead
- 💡 Same math concepts (y=mx+c) work in Python, JavaScript, any language!
- 💡 REST API patterns are universal—same concepts apply everywhere
Questions for Further Research:
How to deploy Flask to AWS Lambda with Zappa or Serverless Framework?
What's the difference between Flask, Django, and FastAPI?
How to connect Python to PostgreSQL using SQLAlchemy?
What are Python decorators and how do they work?
How to write unit tests for Flask APIs with pytest?
How to implement JWT authentication in Flask?\
Testing Completed:
✅ Flask server starts without errors
✅ All 14 API endpoints respond correctly
✅ JSON data persists across server restarts
✅ CORS allows frontend connections
✅ Contact form processes and saves messages
✅ Skill updates save and load correctly
✅ Error handling returns proper HTTP codes
✅ Progress analytics calculate accurately
✅ Frontend successfully calls Python backend
✅ API documentation displays at root URL
Portfolio Status:
Frontend: ✅ Complete (HTML/CSS/JavaScript)
Backend: ✅ Complete (Python/Flask API)
Database: ⏳ JSON files (upgrade to PostgreSQL later)
Authentication: 📅 Upcoming
Deployment: 📅 Upcoming (AWS Lambda + S3)
Email: 📅 Upcoming (SMTP integration)
Skills Progress:
English: 100% ✅
Mathematics: 100% ✅
HTML: 100% ✅
CSS: 100% ✅
JavaScript: 75% ✅
Python: 75% ✅ (fundamentals + Flask mastered!)
Java: 0% 📅
Linux: 0% 📅
SQL: 0% 📅
Kubernetes: 0% 📅
Git: 0% 📅
Progress: 6/11 skills (54.5% of foundational languages!)

```

### Automation Scripts

*   File organization scripts → DevOps automation
*   Portfolio manager → Cloud resource manager (same CRUD patterns)
*   JSON data handling → AWS CloudFormation templates, config files

### Data Processing:

*   List comprehensions → Data transformation pipelines
*   JSON parsing → API response handling
*   File I/O → Log processing, data exports

Portfolio Project Progress Tracker
----------------------------------

✅ **Day 1:** English — _Technical communication_

✅ **Day 2:** Mathematics — _Logic and algorithms_

✅ **Day 3:** HTML — _Web structure_

✅ **Day 4:** CSS — _Styling and layout_

✅ **Day 5:** JavaScript — _Frontend interactivity_

✅ **Day 6:** Python— _Backend logic and APIs_

⬜ **Day 7–8:** Git— _Version control_

⬜ **Day 9–10:** Linux — _Command line and servers_

⬜ **Day 11–12:** SQL — _Database management_

⬜ **Day 13–14:** Kubernetes — _Container orchestration_

⬜ **Day 15+:** Java— _Enterprise applications_

### Final Thoughts

1. **Python’s simplicity is its strength:** Clean syntax forces readable code

2. **Flask makes APIs easy:** Full REST API in ~400 lines of code

3. **Backend completes the picture:** Frontend + Backend = Full-stack developer

4. **Same concepts, different syntax**: Math, logic, algorithms apply everywhere

5. **JSON is the universal language:** Data interchange between any languages

6. **Python dominates cloud computing:** AWS Lambda, automation, ML, data processing

7. **Virtual environments prevent headaches:** Always isolate project dependencies

8. **Error handling is essential:** Users deserve helpful error messages

9. **REST patterns are universal**: Learn once, apply everywhere

Concluding Remarks
------------------

You’ve crossed the 50% mark of core languages! The hardest conceptual leaps are behind you. The remaining skills (Git, Linux, SQL, Kubernetes, Java) build on what you already know.

**Next up:** Git — the version control system that every professional developer uses.

You’ll learn to track changes, collaborate with teams, and deploy your portfolio to GitHub for the world to see.

**Keep this momentum going!** 💪☁️🐍🚀


Additional Resources
--------------------

### [Cloud Glossary](https://medium.com/list/cloud-glossary-528956a3c181)

> Cloud Computing Simplified: A Cloud Glossary For Beginners

### [Cloud Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34703)

> In a world where nearly every industry is moving toward cloud-first strategies, having a baseline understanding of these principles is no longer optional but essential.

### [AI Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34979)

> Whether you’re an Executive, Developer, Engineer, or Project Manager, having a baseline understanding of AI principles is no longer a _nice to have_, it’s **absolutely essential.**

### [PartyRock](http://partyrock.aws)

> PartyRock is a space where you can build AI-generated apps in a playground powered by Amazon Bedrock.
> 
> It’s a fast and fun way to learn about generative AI.

Python Learning
---------------

### [Python.org Official Tutorial](https://docs.python.org/3/tutorial/)

> Official documentation

### [Real Python](https://realpython.com/)

> In-depth tutorials

### [Automate the Boring Stuff](https://automatetheboringstuff.com/)

> Practical Python (free online)

### [Python Crash Course](https://nostarch.com/pythoncrashcourse2e)

> Beginner-friendly book

Flask
-----

### [Flask Official Documentation](https://flask.palletsprojects.com/)

> Reference docs

### [Flask Mega-Tutorial](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)

> Comprehensive guide

### [Flask-RESTful](https://flask-restful.readthedocs.io/)

> REST API extension

Practice
--------

### [LeetCode](https://leetcode.com/)

> Algorithm challenges (filter by Python)

### [HackerRank Python](https://www.hackerrank.com/domains/python)

> Python-specific problems

### [Exercism Python Track](https://exercism.org/tracks/python)

> Mentored exercises

### [Project Euler](https://projecteuler.net/)

### Math + programming challenges

Tools
-----

### [PyCharm](https://www.jetbrains.com/pycharm/)

### Full-featured Python IDE

### [VS Code Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

> Lightweight alternative

### [Postman](https://www.postman.com/)

> API testing tool

### [HTTPie](https://httpie.io/)

> Command-line HTTP client (nicer than curl)

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Python in the Cloud](https://medium.com/@ntombizakhona/python-in-the-cloud-1462f61e3293)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**01 February 2026**
