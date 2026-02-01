
### 🏗️ Let’s Build

9 | Python Backend for Portfolio
--------------------------------

Now let’s build a real Python backend!

### Step 01: Project Structure

Create this structure

```
portfolio-project/
├── index.html
├── styles/
│   └── main.css
├── scripts/
│   └── main.js
├── python-backend/          ← NEW
│   ├── app.py              ← Flask API
│   ├── portfolio_manager.py ← Data management
│   ├── requirements.txt     ← Dependencies
│   └── data/
│       └── portfolio.json   ← Data storage (auto-created)
```

### Step 02: Set Up Python Environment

Create the directory and navigate to it:

```
mkdir python-backend
cd python-backend
```

Create python-backend/requirements.txt:

```
Flask==3.0.0
Flask-CORS==4.0.0
python-dotenv==1.0.0
```

Set up virtual environment (recommended):

```
# Create virtual environment
python -m venv venv
# Activate it:
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate
# Install dependencies:
pip install -r requirements.txt
```

Without virtual environment

```
pip install -r requirements.txt
```

### Step 03: Create Portfolio Data Manager

Create python-backend/portfolio_manager.py:

```
"""
Portfolio Data Manager
Handles all portfolio data operations with JSON persistence.
This module provides a PortfolioManager class that manages:
- Skills and progress tracking
- Projects
- Learning log entries
- Contact form submissions
- Analytics and reporting
"""
import json
import os
from datetime import datetime
from pathlib import Path
from typing import List, Dict, Optional, Any
class PortfolioManager:
    """
    Manages portfolio data with CRUD operations and JSON persistence.
    
    Attributes:
        data_file (str): Path to the JSON data file
        data (dict): In-memory portfolio data
    """
    
    def __init__(self, data_file: str = 'data/portfolio.json'):
        """
        Initialize the PortfolioManager.
        
        Args:
            data_file: Path to the JSON file for data persistence
        """
        self.data_file = data_file
        self._ensure_data_directory()
        self.data = self._load_data()
    
    def _ensure_data_directory(self) -> None:
        """Create data directory if it doesn't exist."""
        directory = Path(self.data_file).parent
        directory.mkdir(parents=True, exist_ok=True)
    
    def _load_data(self) -> Dict[str, Any]:
        """
        Load portfolio data from JSON file.
        
        Returns:
            Dictionary containing portfolio data
        """
        if os.path.exists(self.data_file):
            try:
                with open(self.data_file, 'r', encoding='utf-8') as file:
                    data = json.load(file)
                    print(f"✅ Loaded data from {self.data_file}")
                    return data
            except json.JSONDecodeError as e:
                print(f"⚠️ Corrupted JSON file: {e}")
                print("📁 Creating new portfolio data file...")
                return self._create_default_data()
            except Exception as e:
                print(f"❌ Error loading data: {e}")
                return self._create_default_data()
        else:
            print(f"📁 Creating new portfolio data file: {self.data_file}")
            return self._create_default_data()
    
    def _create_default_data(self) -> Dict[str, Any]:
        """
        Create and save default portfolio data structure.
        
        Returns:
            Dictionary containing default portfolio data
        """
        default_data = {
            "personal_info": {
                "name": "Your Name",
                "title": "Cloud Developer in Training",
                "bio": "Learning essential languages for cloud computing",
                "email": "your.email@example.com",
                "github": "https://github.com/yourusername",
                "linkedin": "https://linkedin.com/in/yourprofile"
            },
            "skills": [                {"name": "English", "level": 100, "category": "Communication", "status": "Completed"},
                {"name": "Mathematics", "level": 100, "category": "Logic", "status": "Completed"},
                {"name": "HTML", "level": 100, "category": "Markup", "status": "Completed"},
                {"name": "CSS", "level": 100, "category": "Styling", "status": "Completed"},
                {"name": "JavaScript", "level": 75, "category": "Programming", "status": "Learning"},
                {"name": "Python", "level": 75, "category": "Programming", "status": "Learning"},
                {"name": "Java", "level": 0, "category": "Programming", "status": "Upcoming"},
                {"name": "Linux", "level": 0, "category": "Systems", "status": "Upcoming"},
                {"name": "SQL", "level": 0, "category": "Database", "status": "Upcoming"},
                {"name": "Kubernetes", "level": 0, "category": "DevOps", "status": "Upcoming"},
                {"name": "Git", "level": 0, "category": "Version Control", "status": "Upcoming"}
            ],
            "projects": [                {
                    "id": 1,
                    "name": "Portfolio Website",
                    "description": "Interactive portfolio built while learning cloud languages",
                    "technologies": ["HTML", "CSS", "JavaScript", "Python", "Flask"],
                    "status": "In Progress",
                    "github_url": "",
                    "live_url": "",
                    "created_at": datetime.now().isoformat()
                }
            ],
            "learning_log": [                {
                    "date": datetime.now().isoformat(),
                    "day": 6,
                    "topic": "Python Basics",
                    "entry": "Started learning Python for backend development and cloud automation"
                }
            ],
            "contact_messages": []
        }
        
        self._save_data(default_data)
        return default_data
    
    def _save_data(self, data: Optional[Dict[str, Any]] = None) -> bool:
        """
        Save portfolio data to JSON file.
        
        Args:
            data: Data to save (uses self.data if None)
            
        Returns:
            True if save was successful, False otherwise
        """
        data_to_save = data if data is not None else self.data
        
        try:
            with open(self.data_file, 'w', encoding='utf-8') as file:
                json.dump(data_to_save, file, indent=2, ensure_ascii=False)
            print(f"💾 Data saved to {self.data_file}")
            return True
        except Exception as e:
            print(f"❌ Error saving data: {e}")
            return False
    
    # ==================== SKILL OPERATIONS ====================
    
    def get_all_skills(self) -> List[Dict[str, Any]]:
        """
        Get all skills.
        
        Returns:
            List of skill dictionaries
        """
        return self.data.get("skills", [])
    
    def get_skill(self, skill_name: str) -> Optional[Dict[str, Any]]:
        """
        Get a specific skill by name (case-insensitive).
        
        Args:
            skill_name: Name of the skill to find
            
        Returns:
            Skill dictionary if found, None otherwise
        """
        for skill in self.data.get("skills", []):
            if skill["name"].lower() == skill_name.lower():
                return skill
        return None
    
    def update_skill_progress(self, skill_name: str, new_level: int) -> bool:
        """
        Update a skill's progress level.
        
        Args:
            skill_name: Name of the skill to update
            new_level: New progress level (0-100)
            
        Returns:
            True if update was successful, False if skill not found
            
        Raises:
            ValueError: If level is not between 0 and 100
        """
        if not isinstance(new_level, int) or not 0 <= new_level <= 100:
            raise ValueError("Level must be an integer between 0 and 100")
        
        for skill in self.data.get("skills", []):
            if skill["name"].lower() == skill_name.lower():
                old_level = skill["level"]
                skill["level"] = new_level
                skill["last_updated"] = datetime.now().isoformat()
                
                # Update status based on level
                if new_level >= 75:
                    skill["status"] = "Completed"
                elif new_level > 0:
                    skill["status"] = "Learning"
                else:
                    skill["status"] = "Upcoming"
                
                # Log the progress update
                self.add_learning_log(
                    entry=f"Updated {skill_name} progress from {old_level}% to {new_level}%",
                    topic=skill_name
                )
                
                self._save_data()
                return True
        
        return False
    
    def get_skills_by_category(self) -> Dict[str, List[Dict[str, Any]]]:
        """
        Group skills by category.
        
        Returns:
            Dictionary with category names as keys and lists of skills as values
        """
        categories: Dict[str, List[Dict[str, Any]]] = {}
        
        for skill in self.data.get("skills", []):
            category = skill.get("category", "Other")
            if category not in categories:
                categories[category] = []
            categories[category].append(skill)
        
        return categories
    
    def get_skills_by_status(self, status: str) -> List[Dict[str, Any]]:
        """
        Get skills filtered by status.
        
        Args:
            status: Status to filter by (Completed, Learning, Upcoming)
            
        Returns:
            List of skills matching the status
        """
        return [            skill for skill in self.data.get("skills", [])
            if skill.get("status", "").lower() == status.lower()
        ]
    
    # ==================== PROJECT OPERATIONS ====================
    
    def get_all_projects(self) -> List[Dict[str, Any]]:
        """
        Get all projects.
        
        Returns:
            List of project dictionaries
        """
        return self.data.get("projects", [])
    
    def get_project(self, project_id: int) -> Optional[Dict[str, Any]]:
        """
        Get a specific project by ID.
        
        Args:
            project_id: ID of the project to find
            
        Returns:
            Project dictionary if found, None otherwise
        """
        for project in self.data.get("projects", []):
            if project.get("id") == project_id:
                return project
        return None
    
    def add_project(
        self,
        name: str,
        description: str,
        technologies: List[str],
        status: str = "In Progress"
    ) -> Dict[str, Any]:
        """
        Add a new project.
        
        Args:
            name: Project name
            description: Project description
            technologies: List of technologies used
            status: Project status (default: "In Progress")
            
        Returns:
            The created project dictionary
        """
        # Generate new ID
        existing_ids = [p.get("id", 0) for p in self.data.get("projects", [])]
        new_id = max(existing_ids, default=0) + 1
        
        project = {
            "id": new_id,
            "name": name,
            "description": description,
            "technologies": technologies,
            "status": status,
            "github_url": "",
            "live_url": "",
            "created_at": datetime.now().isoformat()
        }
        
        if "projects" not in self.data:
            self.data["projects"] = []
        
        self.data["projects"].append(project)
        self._save_data()
        
        return project
    
    # ==================== LEARNING LOG ====================
    
    def add_learning_log(
        self,
        entry: str,
        topic: Optional[str] = None,
        day: Optional[int] = None
    ) -> Dict[str, Any]:
        """
        Add an entry to the learning log.
        
        Args:
            entry: Log entry text
            topic: Topic of the entry (optional)
            day: Day number (auto-calculated if not provided)
            
        Returns:
            The created log entry dictionary
        """
        if "learning_log" not in self.data:
            self.data["learning_log"] = []
        
        log_entry = {
            "date": datetime.now().isoformat(),
            "day": day if day is not None else len(self.data["learning_log"]) + 1,
            "topic": topic or "General",
            "entry": entry
        }
        
        self.data["learning_log"].append(log_entry)
        self._save_data()
        
        return log_entry
    
    def get_learning_log(self, limit: Optional[int] = None) -> List[Dict[str, Any]]:
        """
        Get learning log entries.
        
        Args:
            limit: Maximum number of entries to return (returns last N entries)
            
        Returns:
            List of log entry dictionaries
        """
        logs = self.data.get("learning_log", [])
        
        if limit is not None and limit > 0:
            return logs[-limit:]
        
        return logs
    
    # ==================== CONTACT MESSAGES ====================
    
    def save_contact_message(
        self,
        name: str,
        email: str,
        message: str,
        subject: Optional[str] = None
    ) -> Dict[str, Any]:
        """
        Save a contact form submission.
        
        Args:
            name: Sender's name
            email: Sender's email
            message: Message content
            subject: Message subject (optional)
            
        Returns:
            The created contact message dictionary
        """
        if "contact_messages" not in self.data:
            self.data["contact_messages"] = []
        
        # Generate new ID
        existing_ids = [m.get("id", 0) for m in self.data["contact_messages"]]
        new_id = max(existing_ids, default=0) + 1
        
        contact = {
            "id": new_id,
            "name": name,
            "email": email,
            "subject": subject or "No Subject",
            "message": message,
            "timestamp": datetime.now().isoformat(),
            "read": False
        }
        
        self.data["contact_messages"].append(contact)
        self._save_data()
        
        return contact
    
    def get_contact_messages(
        self,
        unread_only: bool = False
    ) -> List[Dict[str, Any]]:
        """
        Get contact messages.
        
        Args:
            unread_only: If True, return only unread messages
            
        Returns:
            List of contact message dictionaries
        """
        messages = self.data.get("contact_messages", [])
        
        if unread_only:
            return [m for m in messages if not m.get("read", False)]
        
        return messages
    
    # ==================== ANALYTICS ====================
    
    def generate_progress_report(self) -> Dict[str, Any]:
        """
        Generate a comprehensive progress report.
        
        Returns:
            Dictionary containing various analytics metrics
        """
        skills = self.data.get("skills", [])
        
        if not skills:
            return {
                "total_skills": 0,
                "completed_skills": 0,
                "learning_skills": 0,
                "upcoming_skills": 0,
                "average_progress": 0,
                "completion_rate": 0,
                "total_projects": 0,
                "learning_log_entries": 0,
                "contact_messages": 0
            }
        
        total_skills = len(skills)
        completed = len([s for s in skills if s.get("level", 0) >= 75])
        learning = len([s for s in skills if 0 < s.get("level", 0) < 75])
        upcoming = len([s for s in skills if s.get("level", 0) == 0])
        
        total_progress = sum(s.get("level", 0) for s in skills)
        average_progress = total_progress / total_skills if total_skills > 0 else 0
        completion_rate = (completed / total_skills * 100) if total_skills > 0 else 0
        
        return {
            "total_skills": total_skills,
            "completed_skills": completed,
            "learning_skills": learning,
            "upcoming_skills": upcoming,
            "average_progress": round(average_progress, 1),
            "completion_rate": round(completion_rate, 1),
            "total_projects": len(self.data.get("projects", [])),
            "learning_log_entries": len(self.data.get("learning_log", [])),
            "contact_messages": len(self.data.get("contact_messages", []))
        }
    
    # ==================== PERSONAL INFO ====================
    
    def get_personal_info(self) -> Dict[str, str]:
        """
        Get personal information.
        
        Returns:
            Dictionary containing personal info
        """
        return self.data.get("personal_info", {})
    
    def update_personal_info(self, **kwargs) -> Dict[str, str]:
        """
        Update personal information.
        
        Args:
            **kwargs: Key-value pairs to update
            
        Returns:
            Updated personal info dictionary
        """
        if "personal_info" not in self.data:
            self.data["personal_info"] = {}
        
        for key, value in kwargs.items():
            if value is not None:
                self.data["personal_info"][key] = value
        
        self._save_data()
        return self.data["personal_info"]
# ==================== TESTING ====================
if __name__ == "__main__":
    """Test the PortfolioManager when run directly."""
    
    print("\n" + "=" * 50)
    print("🧪 Testing PortfolioManager")
    print("=" * 50)
    
    # Create manager instance
    manager = PortfolioManager()
    
    # Test 1: Get all skills
    print("\n📋 All Skills:")
    skills = manager.get_all_skills()
    for skill in skills:
        status_emoji = "✅" if skill["level"] >= 75 else "⏳" if skill["level"] > 0 else "📅"
        print(f"  {status_emoji} {skill['name']}: {skill['level']}%")
    
    # Test 2: Update a skill
    print("\n📊 Updating Python skill to 80%...")
    success = manager.update_skill_progress("Python", 80)
    print(f"  Result: {'Success' if success else 'Failed'}")
    
    # Test 3: Get skills by category
    print("\n🎯 Skills by Category:")
    by_category = manager.get_skills_by_category()
    for category, cat_skills in by_category.items():
        print(f"\n  {category}:")
        for skill in cat_skills:
            print(f"    - {skill['name']}: {skill['level']}%")
    
    # Test 4: Generate progress report
    print("\n📈 Progress Report:")
    report = manager.generate_progress_report()
    for key, value in report.items():
        print(f"  {key}: {value}")
    
    # Test 5: Add a learning log entry
    print("\n📝 Adding learning log entry...")
    log = manager.add_learning_log(
        entry="Tested PortfolioManager class successfully",
        topic="Python Testing"
    )
    print(f"  Added: {log['entry']}")
    
    print("\n" + "=" * 50)
    print("✅ All tests completed!")
    print("=" * 50 + "\n")
```

### Step 04: Create Flask API

Create python-backend/app.py:

```
"""
Portfolio Backend API
Flask REST API for portfolio management.
This API provides endpoints for:
- Health check
- Skills CRUD operations
- Projects management
- Contact form handling
- Learning log
- Analytics
Run with: python app.py
API will be available at: http://localhost:5000
"""
from flask import Flask, jsonify, request, Response
from flask_cors import CORS
from datetime import datetime
from typing import Tuple, Any
import json
# Import our portfolio manager
from portfolio_manager import PortfolioManager
# ==================== APP INITIALIZATION ====================
app = Flask(__name__)
# Enable CORS for all routes (allows frontend to connect)
CORS(app, resources={
    r"/api/*": {
        "origins": ["http://localhost:*", "http://127.0.0.1:*", "null"],
        "methods": ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
        "allow_headers": ["Content-Type", "Authorization"]
    }
})
# Initialize portfolio manager
portfolio = PortfolioManager()
# ==================== HELPER FUNCTIONS ====================
def success_response(
    data: Any = None,
    message: str = None,
    status_code: int = 200
) -> Tuple[Response, int]:
    """
    Create a standardized success response.
    
    Args:
        data: Response data
        message: Success message
        status_code: HTTP status code
        
    Returns:
        Tuple of (JSON response, status code)
    """
    response = {"success": True}
    
    if data is not None:
        response["data"] = data
    
    if message:
        response["message"] = message
    
    return jsonify(response), status_code
def error_response(
    error: str,
    status_code: int = 400,
    details: Any = None
) -> Tuple[Response, int]:
    """
    Create a standardized error response.
    
    Args:
        error: Error message
        status_code: HTTP status code
        details: Additional error details
        
    Returns:
        Tuple of (JSON response, status code)
    """
    response = {
        "success": False,
        "error": error
    }
    
    if details is not None:
        response["details"] = details
    
    return jsonify(response), status_code
def validate_email(email: str) -> bool:
    """
    Basic email validation.
    
    Args:
        email: Email address to validate
        
    Returns:
        True if email appears valid, False otherwise
    """
    if not email or not isinstance(email, str):
        return False
    
    # Basic check: contains @ and at least one . after @
    if '@' not in email:
        return False
    
    parts = email.split('@')
    if len(parts) != 2:
        return False
    
    local, domain = parts
    if not local or not domain or '.' not in domain:
        return False
    
    return True
# ==================== ROOT ENDPOINT ====================
@app.route('/', methods=['GET'])
def index():
    """
    Root endpoint - API documentation.
    
    Returns:
        JSON with API information and available endpoints
    """
    return success_response(
        data={
            "name": "Portfolio Backend API",
            "version": "1.0.0",
            "description": "REST API for portfolio management",
            "documentation": {
                "health": {
                    "endpoint": "/api/health",
                    "method": "GET",
                    "description": "Check API health status"
                },
                "skills": {
                    "list": {"endpoint": "/api/skills", "method": "GET"},
                    "get": {"endpoint": "/api/skills/<name>", "method": "GET"},
                    "update": {"endpoint": "/api/skills/<name>", "method": "PUT"},
                    "by_category": {"endpoint": "/api/skills/category/<category>", "method": "GET"},
                    "by_status": {"endpoint": "/api/skills/status/<status>", "method": "GET"}
                },
                "projects": {
                    "list": {"endpoint": "/api/projects", "method": "GET"},
                    "create": {"endpoint": "/api/projects", "method": "POST"}
                },
                "contact": {
                    "submit": {"endpoint": "/api/contact", "method": "POST"}
                },
                "analytics": {
                    "progress": {"endpoint": "/api/analytics/progress", "method": "GET"}
                },
                "learning_log": {
                    "list": {"endpoint": "/api/learning-log", "method": "GET"},
                    "create": {"endpoint": "/api/learning-log", "method": "POST"}
                }
            }
        },
        message="Welcome to the Portfolio API! See documentation for available endpoints."
    )
# ==================== HEALTH CHECK ====================
@app.route('/api/health', methods=['GET'])
def health_check():
    """
    Check if API is running.
    
    Returns:
        JSON with health status and timestamp
    """
    return success_response(
        data={
            "status": "healthy",
            "timestamp": datetime.now().isoformat(),
            "uptime": "API is running normally"
        },
        message="Portfolio API is healthy"
    )
# ==================== SKILLS ENDPOINTS ====================
@app.route('/api/skills', methods=['GET'])
def get_skills():
    """
    Get all skills.
    
    Returns:
        JSON with list of all skills
    """
    try:
        skills = portfolio.get_all_skills()
        return success_response(
            data=skills,
            message=f"Retrieved {len(skills)} skills"
        )
    except Exception as e:
        return error_response(str(e), 500)
@app.route('/api/skills/<skill_name>', methods=['GET'])
def get_skill(skill_name: str):
    """
    Get a specific skill by name.
    
    Args:
        skill_name: Name of the skill (URL parameter)
        
    Returns:
        JSON with skill data or error if not found
    """
    try:
        skill = portfolio.get_skill(skill_name)
        
        if skill:
            return success_response(data=skill)
        else:
            return error_response(
                f"Skill '{skill_name}' not found",
                404,
                {"available_skills": [s["name"] for s in portfolio.get_all_skills()]}
            )
    except Exception as e:
        return error_response(str(e), 500)
@app.route('/api/skills/<skill_name>', methods=['PUT'])
def update_skill(skill_name: str):
    """
    Update a skill's progress level.
    
    Args:
        skill_name: Name of the skill (URL parameter)
        
    Request Body:
        {"level": int} - New level between 0-100
        
    Returns:
        JSON with updated skill data or error
    """
    try:
        # Get and validate request data
        data = request.get_json()
        
        if not data:
            return error_response("Request body is required", 400)
        
        if 'level' not in data:
            return error_response(
                "Missing 'level' in request body",
                400,
                {"expected": {"level": "integer between 0 and 100"}}
            )
        
        new_level = data['level']
        
        # Validate level
        if not isinstance(new_level, int):
            return error_response(
                "Level must be an integer",
                400,
                {"received": type(new_level).__name__, "expected": "integer"}
            )
        
        if not 0 <= new_level <= 100:
            return error_response(
                "Level must be between 0 and 100",
                400,
                {"received": new_level, "valid_range": "0-100"}
            )
        
        # Update skill
        success = portfolio.update_skill_progress(skill_name, new_level)
        
        if success:
            updated_skill = portfolio.get_skill(skill_name)
            return success_response(
                data=updated_skill,
                message=f"Updated {skill_name} to {new_level}%"
            )
        else:
            return error_response(
                f"Skill '{skill_name}' not found",
                404,
                {"available_skills": [s["name"] for s in portfolio.get_all_skills()]}
            )
            
    except ValueError as e:
        return error_response(str(e), 400)
    except Exception as e:
        return error_response(str(e), 500)
@app.route('/api/skills/category/<category>', methods=['GET'])
def get_skills_by_category(category: str):
    """
    Get skills filtered by category.
    
    Args:
        category: Category name (URL parameter)
        
    Returns:
        JSON with skills in the specified category
    """
    try:
        all_categories = portfolio.get_skills_by_category()
        
        # Case-insensitive search
        matching_category = None
        for cat_name in all_categories.keys():
            if cat_name.lower() == category.lower():
                matching_category = cat_name
                break
        
        if matching_category:
            skills = all_categories[matching_category]
            return success_response(
                data={
                    "category": matching_category,
                    "skills": skills,
                    "count": len(skills)
                }
            )
        else:
            return error_response(
                f"Category '{category}' not found",
                404,
                {"available_categories": list(all_categories.keys())}
            )
    except Exception as e:
        return error_response(str(e), 500)
@app.route('/api/skills/status/<status>', methods=['GET'])
def get_skills_by_status(status: str):
    """
    Get skills filtered by status.
    
    Args:
        status: Status name (URL parameter) - Completed, Learning, or Upcoming
        
    Returns:
        JSON with skills matching the status
    """
    try:
        valid_statuses = ["Completed", "Learning", "Upcoming"]
        
        # Case-insensitive matching
        matching_status = None
        for valid in valid_statuses:
            if valid.lower() == status.lower():
                matching_status = valid
                break
        
        if not matching_status:
            return error_response(
                f"Invalid status '{status}'",
                400,
                {"valid_statuses": valid_statuses}
            )
        
        skills = portfolio.get_skills_by_status(matching_status)
        return success_response(
            data={
                "status": matching_status,
                "skills": skills,
                "count": len(skills)
            }
        )
    except Exception as e:
        return error_response(str(e), 500)
# ==================== PROJECTS ENDPOINTS ====================
@app.route('/api/projects', methods=['GET'])
def get_projects():
    """
    Get all projects.
    
    Returns:
        JSON with list of all projects
    """
    try:
        projects = portfolio.get_all_projects()
        return success_response(
            data=projects,
            message=f"Retrieved {len(projects)} projects"
        )
    except Exception as e:
        return error_response(str(e), 500)
@app.route('/api/projects', methods=['POST'])
def add_project():
    """
    Add a new project.
    
    Request Body:
        {
            "name": str,
            "description": str,
            "technologies": list[str],
            "status": str (optional)
        }
        
    Returns:
        JSON with created project data
    """
    try:
        data = request.get_json()
        
        if not data:
            return error_response("Request body is required", 400)
        
        # Validate required fields
        required_fields = ['name', 'description', 'technologies']
        missing = [f for f in required_fields if f not in data or not data[f]]
        
        if missing:
            return error_response(
                f"Missing required fields: {', '.join(missing)}",
                400,
                {"required_fields": required_fields}
            )
        
        # Validate technologies is a list
        if not isinstance(data['technologies'], list):
            return error_response(
                "Technologies must be a list",
                400,
                {"expected": ["HTML", "CSS", "JavaScript"]}
            )
        
        # Create project
        project = portfolio.add_project(
            name=data['name'],
            description=data['description'],
            technologies=data['technologies'],
            status=data.get('status', 'In Progress')
        )
        
        return success_response(
            data=project,
            message="Project added successfully",
            status_code=201
        )
        
    except Exception as e:
        return error_response(str(e), 500)
# ==================== CONTACT ENDPOINT ====================
@app.route('/api/contact', methods=['POST'])
def handle_contact():
    """
    Handle contact form submissions.
    
    Request Body:
        {
            "name": str,
            "email": str,
            "message": str,
            "subject": str (optional)
        }
        
    Returns:
        JSON with confirmation message
    """
    try:
        data = request.get_json()
        
        if not data:
            return error_response("Request body is required", 400)
        
        # Validate required fields
        required_fields = ['name', 'email', 'message']
        missing = [f for f in required_fields if not data.get(f)]
        
        if missing:
            return error_response(
                f"Missing required fields: {', '.join(missing)}",
                400,
                {"required_fields": required_fields}
            )
        
        # Validate email
        if not validate_email(data['email']):
            return error_response(
                "Invalid email address",
                400,
                {"received": data['email']}
            )
        
        # Validate message length
        if len(data['message'].strip()) < 10:
            return error_response(
                "Message must be at least 10 characters",
                400,
                {"received_length": len(data['message'].strip())}
            )
        
        # Save contact message
        contact = portfolio.save_contact_message(
            name=data['name'].strip(),
            email=data['email'].strip(),
            message=data['message'].strip(),
            subject=data.get('subject', '').strip() or None
        )
        
        # Log to console (in production, you'd send an email)
        print("\n" + "=" * 50)
        print("📧 NEW CONTACT MESSAGE")
        print("=" * 50)
        print(f"From: {contact['name']} ({contact['email']})")
        if contact.get('subject'):
            print(f"Subject: {contact['subject']}")
        print(f"Message: {contact['message']}")
        print(f"Time: {contact['timestamp']}")
        print("=" * 50 + "\n")
        
        return success_response(
            data={
                "id": contact['id'],
                "timestamp": contact['timestamp']
            },
            message=f"Thank you, {data['name']}! Your message has been received."
        )
        
    except Exception as e:
        return error_response(str(e), 500)
# ==================== ANALYTICS ENDPOINT ====================
@app.route('/api/analytics/progress', methods=['GET'])
def get_progress_report():
    """
    Get progress analytics.
    
    Returns:
        JSON with comprehensive progress report
    """
    try:
        report = portfolio.generate_progress_report()
        return success_response(
            data=report,
            message="Progress report generated"
        )
    except Exception as e:
        return error_response(str(e), 500)
# ==================== LEARNING LOG ENDPOINTS ====================
@app.route('/api/learning-log', methods=['GET'])
def get_learning_log():
    """
    Get learning log entries.
    
    Query Parameters:
        limit (int, optional): Maximum number of entries to return
        
    Returns:
        JSON with learning log entries
    """
    try:
        limit = request.args.get('limit', type=int)
        logs = portfolio.get_learning_log(limit)
        
        return success_response(
            data=logs,
            message=f"Retrieved {len(logs)} log entries"
        )
    except Exception as e:
        return error_response(str(e), 500)
@app.route('/api/learning-log', methods=['POST'])
def add_learning_log():
    """
    Add a learning log entry.
    
    Request Body:
        {
            "entry": str,
            "topic": str (optional),
            "day": int (optional)
        }
        
    Returns:
        JSON with created log entry
    """
    try:
        data = request.get_json()
        
        if not data:
            return error_response("Request body is required", 400)
        
        if 'entry' not in data or not data['entry'].strip():
            return error_response(
                "Missing 'entry' in request body",
                400,
                {"expected": {"entry": "string", "topic": "string (optional)", "day": "integer (optional)"}}
            )
        
        log = portfolio.add_learning_log(
            entry=data['entry'].strip(),
            topic=data.get('topic'),
            day=data.get('day')
        )
        
        return success_response(
            data=log,
            message="Learning log entry added",
            status_code=201
        )
        
    except Exception as e:
        return error_response(str(e), 500)
# ==================== PERSONAL INFO ENDPOINT ====================
@app.route('/api/personal-info', methods=['GET'])
def get_personal_info():
    """
    Get personal information.
    
    Returns:
        JSON with personal info
    """
    try:
        info = portfolio.get_personal_info()
        return success_response(data=info)
    except Exception as e:
        return error_response(str(e), 500)
# ==================== COST CALCULATOR ENDPOINT ====================
@app.route('/api/calculate-cost', methods=['POST'])
def calculate_aws_cost():
    """
    Calculate estimated AWS monthly costs.
    
    Request Body:
        {
            "storageGB": int,
            "hours": int (optional, default 730)
        }
        
    Returns:
        JSON with cost breakdown
    """
    try:
        data = request.get_json()
        
        if not data:
            return error_response("Request body is required", 400)
        
        storage_gb = data.get('storageGB', 0)
        hours = data.get('hours', 730)
        
        if not isinstance(storage_gb, (int, float)) or storage_gb < 0:
            return error_response(
                "storageGB must be a non-negative number",
                400
            )
        
        # AWS pricing (example rates)
        ec2_rate = 0.0116  # per hour
        db_rate = 0.034     # per hour
        storage_rate = 0.023  # per GB per month
        
        # Calculate costs (y = mx + c pattern from Day 2!)
        ec2_cost = ec2_rate * hours
        db_cost = db_rate * hours
        storage_cost = storage_rate * storage_gb
        total_cost = ec2_cost + db_cost + storage_cost
        
        return success_response(
            data={
                "breakdown": {
                    "ec2": round(ec2_cost, 2),
                    "database": round(db_cost, 2),
                    "storage": round(storage_cost, 2)
                },
                "totalCost": round(total_cost, 2),
                "parameters": {
                    "storageGB": storage_gb,
                    "hours": hours
                }
            },
            message=f"Estimated monthly cost: ${round(total_cost, 2)}"
        )
        
    except Exception as e:
        return error_response(str(e), 500)
# ==================== ERROR HANDLERS ====================
@app.errorhandler(404)
def not_found(error):
    """Handle 404 Not Found errors."""
    return error_response(
        "Endpoint not found. Visit / for API documentation.",
        404
    )
@app.errorhandler(405)
def method_not_allowed(error):
    """Handle 405 Method Not Allowed errors."""
    return error_response(
        f"Method not allowed for this endpoint",
        405
    )
@app.errorhandler(500)
def internal_error(error):
    """Handle 500 Internal Server errors."""
    return error_response(
        "Internal server error. Please try again later.",
        500
    )
# ==================== RUN SERVER ====================
if __name__ == '__main__':
    print("\n" + "=" * 60)
    print("🚀 PORTFOLIO BACKEND API")
    print("=" * 60)
    print()
    print("📍 Server URL:     http://localhost:5000")
    print()
    print("📚 Available Endpoints:")
    print("   GET  /                      - API documentation")
    print("   GET  /api/health            - Health check")
    print("   GET  /api/skills            - Get all skills")
    print("   GET  /api/skills/<name>     - Get specific skill")
    print("   PUT  /api/skills/<name>     - Update skill progress")
    print("   GET  /api/projects          - Get all projects")
    print("   POST /api/projects          - Add new project")
    print("   POST /api/contact           - Submit contact form")
    print("   GET  /api/analytics/progress - Get progress report")
    print("   POST /api/calculate-cost    - Calculate AWS costs")
    print()
    print("🔧 Running in DEBUG mode")
    print("=" * 60 + "\n")
    
    app.run(debug=True, host='0.0.0.0', port=5000)
```

### Step 05: Test Your API

**Start the server:**

```
cd python-backend
python app.py
```

**You should see:**

```
🚀 PORTFOLIO BACKEND API
==============================================================
📍 Server URL:     http://localhost:5000
📚 Available Endpoints:
   GET  /                      - API documentation
   GET  /api/health            - Health check
   GET  /api/skills            - Get all skills
   ...
```

**Test in browser:**

*   [http://localhost:5000/](http://localhost:5000/)
*   [http://localhost:5000/api/health](http://localhost:5000/api/health)
*   [http://localhost:5000/api/skills](http://localhost:5000/api/skills)
*   [http://localhost:5000/api/analytics/progress](http://localhost:5000/api/analytics/progress)

**Test with curl (command line):**

```
# Root endpoint (API documentation)
curl http://localhost:5000/
# Health check
curl http://localhost:5000/api/health
# Get all skills
curl http://localhost:5000/api/skills
# Get specific skill
curl http://localhost:5000/api/skills/Python
# Get skills by category
curl http://localhost:5000/api/skills/category/Programming
# Get skills by status
curl http://localhost:5000/api/skills/status/Completed
# Update skill (PUT request)
curl -X PUT http://localhost:5000/api/skills/Python \
  -H "Content-Type: application/json" \
  -d '{"level": 80}'
# Get all projects
curl http://localhost:5000/api/projects
# Add new project (POST request)
curl -X POST http://localhost:5000/api/projects \
  -H "Content-Type: application/json" \
  -d '{"name":"AWS Cost Calculator","description":"Calculate cloud costs","technologies":["Python","JavaScript","HTML"]}'
# Submit contact form (POST request)
  curl -X POST http://localhost:5000/api/contact \
    -H "Content-Type: application/json" \
    -d '{"name":"Test User","email":"test@example.com","message":"Hello from the API test!"}'
# Calculate AWS costs
curl -X POST http://localhost:5000/api/calculate-cost \
  -H "Content-Type: application/json" \
  -d '{"storageGB": 100}'
# Get progress report
curl http://localhost:5000/api/analytics/progress
# Get learning log
curl http://localhost:5000/api/learning-log
# Add learning log entry
curl -X POST http://localhost:5000/api/learning-log \
  -H "Content-Type: application/json" \
  -d '{"entry":"Completed Python API testing","topic":"Python"}'
```

Expected responses

**Health Check:**

```
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2024-01-15T10:30:00.000000",
    "uptime": "API is running normally"
  },
  "message": "Portfolio API is healthy"
}
```

**Get Skills:**

```
{
  "success": true,
  "data": [    {"name": "English", "level": 100, "category": "Communication", "status": "Completed"},
    {"name": "Python", "level": 75, "category": "Programming", "status": "Learning"}
  ],
  "message": "Retrieved 11 skills"
}
```

**Error Response (Invalid Endpoint):**

```
{
  "success": false,
  "error": "Endpoint not found. Visit / for API documentation."
}
```

### Step 06: Connect Frontend to Backend

Now let’s update your JavaScript to communicate with the Python API.

Update scripts/main.js — Add these functions:

```
/* ===================================
   API CONFIGURATION
   =================================== */
const API_BASE_URL = 'http://localhost:5000/api';
// Helper function for API calls
async function apiCall(endpoint, options = {}) {
    const url = `${API_BASE_URL}${endpoint}`;
    
    const defaultOptions = {
        headers: {
            'Content-Type': 'application/json'
        }
    };
    
    const mergedOptions = {
        ...defaultOptions,
        ...options,
        headers: {
            ...defaultOptions.headers,
            ...options.headers
        }
    };
    
    try {
        const response = await fetch(url, mergedOptions);
        const data = await response.json();
        
        if (!response.ok) {
            throw new Error(data.error || `HTTP error: ${response.status}`);
        }
        
        return data;
    } catch (error) {
        console.error(`API Error (${endpoint}):`, error);
        throw error;
    }
}
/* ===================================
   LOAD SKILLS FROM PYTHON BACKEND
   =================================== */
async function loadSkillsFromAPI() {
    try {
        console.log('📡 Fetching skills from Python backend...');
        
        const result = await apiCall('/skills');
        
        if (result.success) {
            console.log('✅ Skills loaded:', result.data);
            updateSkillsDisplay(result.data);
            return result.data;
        } else {
            throw new Error(result.error);
        }
    } catch (error) {
        console.error('❌ Failed to load skills from backend:', error.message);
        console.log('ℹ️ Using default progress bar animation instead');
        // Fall back to existing animation
        return null;
    }
}
function updateSkillsDisplay(skills) {
    skills.forEach((skill, index) => {
        // Find progress bar by data-skill attribute
        const skillName = skill.name.toLowerCase();
        const progressBar = document.querySelector(`[data-skill="${skillName}"]`);
        
        if (progressBar) {
            // Animate progress bar with stagger
            setTimeout(() => {
                progressBar.style.width = skill.level + '%';
                progressBar.textContent = skill.level + '%';
                
                // Update status text if it exists
                const skillItem = progressBar.closest('.skill-item');
                if (skillItem) {
                    const statusElement = skillItem.querySelector('strong');
                    if (statusElement && statusElement.textContent.includes('Status')) {
                        const statusEmoji = skill.level >= 75 ? '✅' : skill.level > 0 ? '⏳' : '📅';
                        statusElement.nextSibling.textContent = ` ${statusEmoji} ${skill.status}`;
                    }
                }
            }, index * 100);
        }
    });
}
/* ===================================
   SUBMIT CONTACT FORM TO PYTHON BACKEND
   =================================== */
async function submitContactFormToAPI(formData) {
    try {
        console.log('📧 Submitting contact form to Python backend...');
        
        const result = await apiCall('/contact', {
            method: 'POST',
            body: JSON.stringify(formData)
        });
        
        if (result.success) {
            console.log('✅ Contact form submitted:', result);
            return result;
        } else {
            throw new Error(result.error);
        }
    } catch (error) {
        console.error('❌ Failed to submit contact form:', error.message);
        throw error;
    }
}
// Update initContactForm to use Python backend
function initContactFormWithAPI() {
    const form = document.querySelector('form');
    
    if (!form) {
        console.log('⚠️ No contact form found');
        return;
    }
    
    form.addEventListener('submit', async function(e) {
        e.preventDefault();
        
        // Get form data
        const formData = new FormData(form);
        const data = {
            name: formData.get('name'),
            email: formData.get('email'),
            subject: formData.get('subject') || '',
            message: formData.get('message')
        };
        
        // Client-side validation
        if (!data.name || data.name.trim().length < 2) {
            showFormMessage('Please enter your name (at least 2 characters)', 'error');
            return;
        }
        
        if (!data.email || !isValidEmail(data.email)) {
            showFormMessage('Please enter a valid email address', 'error');
            return;
        }
        
        if (!data.message || data.message.trim().length < 10) {
            showFormMessage('Please enter a message (at least 10 characters)', 'error');
            return;
        }
        
        // Disable submit button while processing
        const submitButton = form.querySelector('button[type="submit"]');
        const originalText = submitButton.textContent;
        submitButton.disabled = true;
        submitButton.textContent = 'Sending...';
        
        try {
            const result = await submitContactFormToAPI(data);
            showFormMessage(result.message, 'success');
            form.reset();
        } catch (error) {
            // Try to show specific error from API, or fallback message
            const errorMessage = error.message || 'Failed to send message. Please try again.';
            showFormMessage(errorMessage, 'error');
        } finally {
            // Re-enable button
            submitButton.disabled = false;
            submitButton.textContent = originalText;
        }
    });
    
    console.log('✅ Contact form connected to Python backend');
}
/* ===================================
   LOAD PROGRESS ANALYTICS
   =================================== */
async function loadProgressAnalytics() {
    try {
        const result = await apiCall('/analytics/progress');
        
        if (result.success) {
            console.log('📊 Progress analytics:', result.data);
            displayProgressAnalytics(result.data);
            return result.data;
        }
    } catch (error) {
        console.error('❌ Failed to load analytics:', error.message);
    }
}
function displayProgressAnalytics(analytics) {
    // You could display this in a dashboard section
    console.log(`
📊 Portfolio Progress Report:
   Total Skills: ${analytics.total_skills}
   Completed: ${analytics.completed_skills}
   Learning: ${analytics.learning_skills}
   Upcoming: ${analytics.upcoming_skills}
   Average Progress: ${analytics.average_progress}%
   Completion Rate: ${analytics.completion_rate}%
    `);
}
/* ===================================
   AWS COST CALCULATOR
   =================================== */
async function calculateAWSCost(storageGB) {
    try {
        const result = await apiCall('/calculate-cost', {
            method: 'POST',
            body: JSON.stringify({ storageGB: parseInt(storageGB) })
        });
        
        if (result.success) {
            console.log('💰 Cost calculation:', result.data);
            return result.data;
        }
    } catch (error) {
        console.error('❌ Failed to calculate cost:', error.message);
        throw error;
    }
}
/* ===================================
   CHECK API HEALTH
   =================================== */
async function checkAPIHealth() {
    try {
        const result = await apiCall('/health');
        
        if (result.success && result.data.status === 'healthy') {
            console.log('✅ Python backend is healthy');
            return true;
        }
        return false;
    } catch (error) {
        console.log('⚠️ Python backend is not available');
        console.log('ℹ️ Start it with: cd python-backend && python app.py');
        return false;
    }
}
/* ===================================
   INITIALIZE WITH API
   =================================== */
async function initializeWithAPI() {
    // Check if backend is available
    const isHealthy = await checkAPIHealth();
    
    if (isHealthy) {
        // Load skills from Python backend
        await loadSkillsFromAPI();
        
        // Connect contact form to backend
        initContactFormWithAPI();
        
        // Load analytics
        await loadProgressAnalytics();
        
        console.log('🚀 Frontend connected to Python backend!');
    } else {
        // Use client-side only functionality
        console.log('📴 Running in offline mode (no backend)');
        initContactForm(); // Use original client-side form
        initProgressBars(); // Use original animation
    }
}
// Update DOMContentLoaded to use API initialization
document.addEventListener('DOMContentLoaded', function() {
    console.log('✅ Portfolio JavaScript loaded');
    
    // Initialize all features
    initSmoothScrolling();
    initSkillsAnimation();
    initProjectHoverEffects();
    initScrollToTop();
    
    // Try to connect to Python backend
    initializeWithAPI();
});
```

### Step 07: Test Full-Stack Integration

1.  **Start the Python backend:**

```
cd python-backend
python app.py
```

**2. Start the Python backend:**

*   Open index.html directly or use a local server
*   Check browser console (F12 → Console tab)

**Expected console output:**

```
💼 Cloud Developer Portfolio
Built with HTML, CSS, and JavaScript
⚡ All systems operational!
✅ Portfolio JavaScript loaded successfully!
✅ Smooth scrolling enabled
✅ Progress bar animations ready
✅ Contact form validation enabled
✅ Skills animation ready
✅ Project hover effects enabled
✅ Scroll-to-top button created
✅ All interactive features initialized!
📡 Fetching skills from Python backend...
✅ Portfolio JavaScript loaded
✅ Smooth scrolling enabled
✅ Skills animation ready
✅ Project hover effects enabled
✅ Scroll-to-top button created
✅ Skills loaded: Array(11)
✅ Python backend is healthy
📡 Fetching skills from Python backend...
✅ Skills loaded: Array(11)
✅ Contact form connected to Python backend
📊 Progress analytics: Object
📊 Portfolio Progress Report:
   Total Skills: 11
   Completed: 6
   Learning: 0
   Upcoming: 5
   Average Progress: 50%
   Completion Rate: 54.5%
    
🚀 Frontend connected to Python backend!
```

**3. Test the contact form:**

*   Fill out the form with valid data
*   Submit and check for success message
*   Check Python terminal for the logged message

### ⛔ End of Building Tutorial⛔

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Python in the Cloud](https://medium.com/@ntombizakhona/python-in-the-cloud-1462f61e3293)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**01 February 2026**
