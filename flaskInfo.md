# 🚀 Complete Flask Mastery Guide: Beginner to Advanced

I'll create a comprehensive Flask learning guide for you! This will cover everything from absolute basics to advanced concepts with hands-on examples.

---

## **PART 1: FOUNDATIONS**

### **1. Introduction to Flask**

#### **What is Flask?**
Flask is a **micro-framework** for Python web development. "Micro" doesn't mean it lacks functionality—it means Flask keeps the core simple while allowing extensions.

**Key Characteristics:**
- **Lightweight**: Minimal dependencies out of the box
- **Flexible**: You choose your tools (database, forms, etc.)
- **WSGI-based**: Uses Werkzeug WSGI toolkit
- **Jinja2 templating**: Powerful template engine included

**Flask vs Django:**
```
Flask:
✓ Minimalist, you add what you need
✓ Better for small to medium projects, APIs
✓ More flexibility, steeper learning curve for full apps

Django:
✓ "Batteries included" - everything built-in
✓ Better for large, complex applications
✓ Opinionated structure
```

**Understanding WSGI (Web Server Gateway Interface):**
- WSGI is a specification that describes how a web server communicates with web applications
- Flask apps are WSGI applications
- Flow: `Web Server (Nginx) → WSGI Server (Gunicorn) → Flask App`

**Request Lifecycle:**
```
1. Client sends HTTP request
2. Web server receives request
3. WSGI server forwards to Flask
4. Flask routing matches URL to function
5. View function processes request
6. Flask returns response
7. Response sent back to client
```

---

### **2. Flask Environment Setup**

#### **Step-by-Step Setup:**

**Step 1: Install Python**
```bash
# Check Python version (3.8+ recommended)
python --version
# or
python3 --version
```

**Step 2: Create Project Directory**
```bash
mkdir flask_mastery
cd flask_mastery
```

**Step 3: Create Virtual Environment**
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

**Why Virtual Environments?**
- Isolate project dependencies
- Avoid conflicts between projects
- Easy to recreate environments

**Step 4: Install Flask**
```bash
pip install Flask
```

**Step 5: Create Requirements File**
```bash
pip freeze > requirements.txt
```

#### **Professional Project Structure**

```
flask_mastery/
│
├── venv/                          # Virtual environment (don't commit)
│
├── app/
│   ├── __init__.py               # App factory
│   ├── models.py                 # Database models
│   ├── routes.py                 # Route definitions
│   ├── forms.py                  # Form classes
│   ├── utils.py                  # Helper functions
│   │
│   ├── templates/                # HTML templates
│   │   ├── base.html
│   │   ├── index.html
│   │   └── errors/
│   │       ├── 404.html
│   │       └── 500.html
│   │
│   ├── static/                   # Static files
│   │   ├── css/
│   │   │   └── style.css
│   │   ├── js/
│   │   │   └── main.js
│   │   └── images/
│   │
│   └── blueprints/               # Modular components
│       ├── auth/
│       │   ├── __init__.py
│       │   └── routes.py
│       └── api/
│           ├── __init__.py
│           └── routes.py
│
├── migrations/                    # Database migrations
├── tests/                         # Test files
│   ├── test_routes.py
│   └── test_models.py
│
├── config.py                      # Configuration
├── requirements.txt               # Dependencies
├── .env                          # Environment variables (don't commit)
├── .gitignore                    # Git ignore file
└── run.py                        # Application entry point
```

#### **Your First Flask App**

````python name=app.py
from flask import Flask

# Create Flask application instance
app = Flask(__name__)

# Define a route
@app.route('/')
def home():
    return "Hello, Flask World!"

# Run the application
if __name__ == '__main__':
    app.run(debug=True)
````

**Run the app:**
```bash
python app.py
```

Visit: `http://127.0.0.1:5000/`

#### **Environment Variables Setup**

````python name=.env
# Don't commit this file!
FLASK_APP=run.py
FLASK_ENV=development
SECRET_KEY=your-secret-key-here
DATABASE_URL=sqlite:///app.db
````

````python name=config.py
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'hard-to-guess-string'
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'sqlite:///app.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False

class DevelopmentConfig(Config):
    DEBUG = True

class ProductionConfig(Config):
    DEBUG = False

config = {
    'development': DevelopmentConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig
}
````

---

## **PART 2: CORE CONCEPTS**

### **3. Routing & URL Rules**

#### **Basic Routing**

````python name=basic_routes.py
from flask import Flask

app = Flask(__name__)

# Simple route
@app.route('/')
def index():
    return "Home Page"

# Route with specific path
@app.route('/about')
def about():
    return "About Page"

# Route with dynamic parameter
@app.route('/user/<username>')
def show_user(username):
    return f"User: {username}"

# Route with typed parameter
@app.route('/post/<int:post_id>')
def show_post(post_id):
    return f"Post ID: {post_id}"

# Route with multiple parameters
@app.route('/user/<username>/post/<int:post_id>')
def user_post(username, post_id):
    return f"User: {username}, Post: {post_id}"
````

#### **URL Converters**

```python
# string - accepts any text without slashes (default)
@app.route('/page/<string:name>')

# int - accepts integers
@app.route('/item/<int:id>')

# float - accepts floating point numbers
@app.route('/price/<float:amount>')

# path - accepts text including slashes
@app.route('/files/<path:filename>')

# uuid - accepts UUID strings
@app.route('/object/<uuid:id>')
```

#### **Multiple Routes for Same Function**

````python name=multiple_routes.py
@app.route('/')
@app.route('/home')
@app.route('/index')
def home():
    return "This is the home page"
````

#### **Endpoint Naming & URL Building**

````python name=url_building.py
from flask import Flask, url_for

app = Flask(__name__)

@app.route('/')
def index():
    return "Home"

@app.route('/user/<username>')
def profile(username):
    return f"Profile: {username}"

@app.route('/admin')
def admin():
    return "Admin Panel"

# URL building
@app.route('/test')
def test():
    # Generates URLs dynamically
    return f"""
    Home URL: {url_for('index')}
    Profile URL: {url_for('profile', username='john')}
    Admin URL: {url_for('admin')}
    """
````

**Why use `url_for()`?**
- URLs change, function names stay same
- Automatically handles URL escaping
- Generates absolute URLs when needed

---

### **4. HTTP Methods**

#### **Understanding HTTP Methods**

````python name=http_methods.py
from flask import Flask, request

app = Flask(__name__)

# GET - Retrieve data
@app.route('/api/users', methods=['GET'])
def get_users():
    # Fetch and return users
    return {'users': ['Alice', 'Bob', 'Charlie']}

# POST - Create new resource
@app.route('/api/users', methods=['POST'])
def create_user():
    # Create new user from request data
    data = request.get_json()
    return {'message': 'User created', 'user': data}, 201

# PUT - Update entire resource
@app.route('/api/users/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    data = request.get_json()
    return {'message': f'User {user_id} updated', 'data': data}

# PATCH - Partially update resource
@app.route('/api/users/<int:user_id>', methods=['PATCH'])
def partial_update(user_id):
    data = request.get_json()
    return {'message': f'User {user_id} partially updated'}

# DELETE - Remove resource
@app.route('/api/users/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    return {'message': f'User {user_id} deleted'}, 204
````

#### **Handling Multiple Methods**

````python name=multiple_methods.py
@app.route('/api/item/<int:item_id>', methods=['GET', 'POST', 'DELETE'])
def handle_item(item_id):
    if request.method == 'GET':
        return {'item_id': item_id, 'name': 'Sample Item'}
    
    elif request.method == 'POST':
        data = request.get_json()
        return {'message': 'Item created', 'data': data}, 201
    
    elif request.method == 'DELETE':
        return {'message': f'Item {item_id} deleted'}, 204
````

---

### **5. Request & Response Handling**

#### **Accessing Request Data**

````python name=request_handling.py
from flask import Flask, request, jsonify, make_response

app = Flask(__name__)

# Query Parameters (URL: /search?q=flask&page=1)
@app.route('/search')
def search():
    query = request.args.get('q', '')  # Get 'q' parameter
    page = request.args.get('page', 1, type=int)  # With type conversion
    return f"Searching for: {query}, Page: {page}"

# Form Data (POST forms)
@app.route('/login', methods=['POST'])
def login():
    username = request.form.get('username')
    password = request.form.get('password')
    return f"Username: {username}"

# JSON Data
@app.route('/api/data', methods=['POST'])
def receive_json():
    data = request.get_json()
    name = data.get('name')
    age = data.get('age')
    return jsonify({'received': data})

# Headers
@app.route('/headers')
def show_headers():
    user_agent = request.headers.get('User-Agent')
    auth_token = request.headers.get('Authorization')
    return f"User-Agent: {user_agent}"

# Cookies
@app.route('/check-cookie')
def check_cookie():
    username = request.cookies.get('username')
    return f"Cookie value: {username}"

# Files
@app.route('/upload', methods=['POST'])
def upload_file():
    if 'file' in request.files:
        file = request.files['file']
        filename = file.filename
        # file.save(f'/uploads/{filename}')
        return f"File uploaded: {filename}"
    return "No file uploaded"
````

#### **Response Types**

````python name=response_types.py
from flask import Flask, jsonify, make_response, render_template_string

app = Flask(__name__)

# 1. String Response (default)
@app.route('/string')
def string_response():
    return "Simple string response"

# 2. JSON Response
@app.route('/json')
def json_response():
    return jsonify({
        'status': 'success',
        'data': {'name': 'John', 'age': 30}
    })

# 3. Custom Status Code
@app.route('/created', methods=['POST'])
def created():
    return jsonify({'message': 'Resource created'}), 201

# 4. Custom Response Object
@app.route('/custom')
def custom_response():
    response = make_response(jsonify({'custom': 'response'}))
    response.status_code = 200
    response.headers['X-Custom-Header'] = 'Value'
    response.set_cookie('session_id', '12345')
    return response

# 5. Tuple Response (body, status, headers)
@app.route('/tuple')
def tuple_response():
    return (
        jsonify({'data': 'example'}),
        201,
        {'X-Custom': 'Header'}
    )

# 6. HTML Response
@app.route('/html')
def html_response():
    html = "<h1>Hello, Flask!</h1><p>This is HTML</p>"
    return render_template_string(html)
````

#### **Status Codes Reference**

```python
# Common Status Codes
200 - OK (Success)
201 - Created (Resource created successfully)
204 - No Content (Success, but no content to return)
400 - Bad Request (Client error)
401 - Unauthorized (Authentication required)
403 - Forbidden (Authenticated but not authorized)
404 - Not Found (Resource doesn't exist)
500 - Internal Server Error (Server error)
```

---

### **6. Templates (Jinja2)**

#### **Basic Template Usage**

````python name=template_app.py
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('index.html', title='Home')

@app.route('/user/<name>')
def user(name):
    return render_template('user.html', username=name)
````

````html name=templates/base.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}My Flask App{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
    <nav>
        <a href="{{ url_for('home') }}">Home</a>
        <a href="{{ url_for('about') }}">About</a>
    </nav>

    <main>
        {% block content %}{% endblock %}
    </main>

    <footer>
        <p>&copy; 2026 Flask Mastery</p>
    </footer>

    {% block scripts %}{% endblock %}
</body>
</html>
````

````html name=templates/index.html
{% extends "base.html" %}

{% block title %}Home - {{ super() }}{% endblock %}

{% block content %}
    <h1>Welcome to Flask!</h1>
    <p>This is the home page.</p>
{% endblock %}
````

#### **Jinja2 Features**

````html name=templates/features.html
{% extends "base.html" %}

{% block content %}
    <!-- 1. Variables -->
    <h1>Hello, {{ username }}!</h1>
    <p>Age: {{ age }}</p>

    <!-- 2. Filters -->
    <p>{{ text|upper }}</p>
    <p>{{ text|lower }}</p>
    <p>{{ text|capitalize }}</p>
    <p>{{ text|title }}</p>
    <p>{{ long_text|truncate(50) }}</p>
    <p>{{ html_content|safe }}</p>  <!-- Render HTML -->
    <p>{{ price|round(2) }}</p>

    <!-- 3. Conditionals -->
    {% if user.is_authenticated %}
        <p>Welcome back, {{ user.name }}!</p>
    {% elif user.is_guest %}
        <p>Welcome, Guest!</p>
    {% else %}
        <p>Please log in.</p>
    {% endif %}

    <!-- 4. Loops -->
    <ul>
    {% for item in items %}
        <li>{{ loop.index }}. {{ item.name }} - ${{ item.price }}</li>
    {% endfor %}
    </ul>

    <!-- Loop with else -->
    <ul>
    {% for user in users %}
        <li>{{ user.name }}</li>
    {% else %}
        <li>No users found.</li>
    {% endfor %}
    </ul>

    <!-- 5. Loop Variables -->
    {% for item in items %}
        <p>
            Index: {{ loop.index }}           (1-indexed)
            Index0: {{ loop.index0 }}         (0-indexed)
            First: {{ loop.first }}           (True/False)
            Last: {{ loop.last }}             (True/False)
            Length: {{ loop.length }}         (Total items)
        </p>
    {% endfor %}

    <!-- 6. Macros (Reusable Components) -->
    {% macro render_field(field) %}
        <div class="form-group">
            <label>{{ field.label }}</label>
            {{ field(**kwargs) }}
            {% if field.errors %}
                <ul class="errors">
                {% for error in field.errors %}
                    <li>{{ error }}</li>
                {% endfor %}
                </ul>
            {% endif %}
        </div>
    {% endmacro %}

    <!-- Use macro -->
    {{ render_field(form.username, class="form-control") }}

    <!-- 7. Include -->
    {% include 'partials/header.html' %}

    <!-- 8. Set Variables -->
    {% set total = price * quantity %}
    <p>Total: ${{ total }}</p>

    <!-- 9. Tests -->
    {% if value is defined %}
        <p>Value is defined</p>
    {% endif %}

    {% if value is none %}
        <p>Value is None</p>
    {% endif %}

    {% if items is iterable %}
        <p>Items is iterable</p>
    {% endif %}

    <!-- 10. Comments -->
    {# This is a comment and won't be rendered #}
{% endblock %}
````

#### **Advanced Template Example**

````python name=advanced_templates.py
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/dashboard')
def dashboard():
    user = {
        'name': 'John Doe',
        'email': 'john@example.com',
        'role': 'admin',
        'is_authenticated': True
    }
    
    products = [
        {'id': 1, 'name': 'Laptop', 'price': 999.99, 'stock': 15},
        {'id': 2, 'name': 'Mouse', 'price': 29.99, 'stock': 100},
        {'id': 3, 'name': 'Keyboard', 'price': 79.99, 'stock': 0}
    ]
    
    return render_template('dashboard.html', user=user, products=products)
````

````html name=templates/dashboard.html
{% extends "base.html" %}

{% block content %}
    <h1>Dashboard - {{ user.name }}</h1>
    
    {% if user.role == 'admin' %}
        <div class="admin-panel">
            <h2>Admin Controls</h2>
            <button>Manage Users</button>
        </div>
    {% endif %}

    <h2>Products</h2>
    <table>
        <thead>
            <tr>
                <th>#</th>
                <th>Name</th>
                <th>Price</th>
                <th>Status</th>
            </tr>
        </thead>
        <tbody>
        {% for product in products %}
            <tr class="{% if loop.index % 2 == 0 %}even{% else %}odd{% endif %}">
                <td>{{ loop.index }}</td>
                <td>{{ product.name }}</td>
                <td>${{ product.price }}</td>
                <td>
                    {% if product.stock > 0 %}
                        <span class="in-stock">In Stock ({{ product.stock }})</span>
                    {% else %}
                        <span class="out-of-stock">Out of Stock</span>
                    {% endif %}
                </td>
            </tr>
        {% else %}
            <tr>
                <td colspan="4">No products available.</td>
            </tr>
        {% endfor %}
        </tbody>
    </table>
{% endblock %}
````

---

### **7. Static Files**

#### **Static File Structure**

```
static/
├── css/
│   ├── style.css
│   └── bootstrap.min.css
├── js/
│   ├── main.js
│   └── jquery.min.js
├── images/
│   ├── logo.png
│   └── banner.jpg
└── fonts/
    └── custom-font.woff
```

#### **Using Static Files**

````html name=templates/static_example.html
<!DOCTYPE html>
<html>
<head>
    <!-- CSS Files -->
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
    
    <!-- Favicon -->
    <link rel="icon" href="{{ url_for('static', filename='images/favicon.ico') }}">
</head>
<body>
    <!-- Images -->
    <img src="{{ url_for('static', filename='images/logo.png') }}" alt="Logo">
    
    <!-- Background image in inline style -->
    <div style="background-image: url('{{ url_for('static', filename='images/banner.jpg') }}')">
        Content
    </div>
    
    <!-- JavaScript Files -->
    <script src="{{ url_for('static', filename='js/jquery.min.js') }}"></script>
    <script src="{{ url_for('static', filename='js/main.js') }}"></script>
</body>
</html>
````

````css name=static/css/style.css
/* Custom CSS */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
}

nav {
    background-color: #333;
    padding: 1rem;
}

nav a {
    color: white;
    text-decoration: none;
    margin-right: 1rem;
}

main {
    max-width: 1200px;
    margin: 2rem auto;
    padding: 0 1rem;
}
````

````javascript name=static/js/main.js
// Custom JavaScript
document.addEventListener('DOMContentLoaded', function() {
    console.log('Flask app loaded!');
    
    // Example: Form validation
    const form = document.querySelector('#myForm');
    if (form) {
        form.addEventListener('submit', function(e) {
            const input = document.querySelector('#username');
            if (input.value.trim() === '') {
                e.preventDefault();
                alert('Username is required!');
            }
        });
    }
});
````

#### **Custom Static Folder**

````python name=custom_static.py
from flask import Flask

# Custom static folder
app = Flask(__name__, 
            static_folder='assets',  # Instead of 'static'
            static_url_path='/assets')  # URL path

@app.route('/')
def home():
    # Now use /assets/ in URLs
    return '<link rel="stylesheet" href="/assets/css/style.css">'
````

---

## **PART 3: FORMS & DATA HANDLING**

### **8. Forms & Validation**

#### **Basic HTML Forms**

````python name=basic_forms.py
from flask import Flask, render_template, request, redirect, url_for, flash

app = Flask(__name__)
app.secret_key = 'your-secret-key'

@app.route('/contact', methods=['GET', 'POST'])
def contact():
    if request.method == 'POST':
        name = request.form.get('name')
        email = request.form.get('email')
        message = request.form.get('message')
        
        # Basic validation
        if not name or not email or not message:
            flash('All fields are required!', 'error')
            return redirect(url_for('contact'))
        
        # Process form data
        flash(f'Thank you, {name}! Your message has been received.', 'success')
        return redirect(url_for('contact'))
    
    return render_template('contact.html')
````

````html name=templates/contact.html
{% extends "base.html" %}

{% block content %}
    <h1>Contact Us</h1>
    
    <!-- Flash Messages -->
    {% with messages = get_flashed_messages(with_categories=true) %}
        {% if messages %}
            {% for category, message in messages %}
                <div class="alert alert-{{ category }}">
                    {{ message }}
                </div>
            {% endfor %}
        {% endif %}
    {% endwith %}
    
    <form method="POST" action="{{ url_for('contact') }}">
        <div class="form-group">
            <label for="name">Name:</label>
            <input type="text" id="name" name="name" required>
        </div>
        
        <div class="form-group">
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
        </div>
        
        <div class="form-group">
            <label for="message">Message:</label>
            <textarea id="message" name="message" required></textarea>
        </div>
        
        <button type="submit">Send Message</button>
    </form>
{% endblock %}
````

#### **Flask-WTF (Recommended)**

**Installation:**
```bash
pip install Flask-WTF email-validator
```

````python name=forms.py
from flask_wtf import FlaskForm
from wtforms import StringField, TextAreaField, PasswordField, SubmitField, SelectField
from wtforms.validators import DataRequired, Email, Length, EqualTo, ValidationError

class ContactForm(FlaskForm):
    name = StringField('Name', 
                       validators=[DataRequired(), Length(min=2, max=50)])
    
    email = StringField('Email', 
                        validators=[DataRequired(), Email()])
    
    subject = SelectField('Subject',
                         choices=[('general', 'General Inquiry'),
                                 ('support', 'Technical Support'),
                                 ('sales', 'Sales')])
    
    message = TextAreaField('Message',
                           validators=[DataRequired(), Length(min=10, max=500)])
    
    submit = SubmitField('Send Message')

class RegistrationForm(FlaskForm):
    username = StringField('Username',
                          validators=[DataRequired(), Length(min=4, max=20)])
    
    email = StringField('Email',
                       validators=[DataRequired(), Email()])
    
    password = PasswordField('Password',
                            validators=[DataRequired(), Length(min=8)])
    
    confirm_password = PasswordField('Confirm Password',
                                     validators=[DataRequired(), 
                                               EqualTo('password')])
    
    submit = SubmitField('Register')
    
    # Custom validator
    def validate_username(self, username):
        # Check if username already exists in database
        # user = User.query.filter_by(username=username.data).first()
        # if user:
        #     raise ValidationError('Username already taken.')
        pass
````

````python name=wtf_forms_app.py
from flask import Flask, render_template, redirect, url_for, flash
from forms import ContactForm, RegistrationForm

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key-here'

@app.route('/contact', methods=['GET', 'POST'])
def contact():
    form = ContactForm()
    
    if form.validate_on_submit():
        name = form.name.data
        email = form.email.data
        subject = form.subject.data
        message = form.message.data
        
        # Process the form data
        flash(f'Thank you, {name}! Your message has been sent.', 'success')
        return redirect(url_for('contact'))
    
    return render_template('contact_wtf.html', form=form)

@app.route('/register', methods=['GET', 'POST'])
def register():
    form = RegistrationForm()
    
    if form.validate_on_submit():
        username = form.username.data
        email = form.email.data
        password = form.password.data
        
        # Create user (we'll implement this with database later)
        flash('Registration successful!', 'success')
        return redirect(url_for('login'))
    
    return render_template('register.html', form=form)
````

````html name=templates/contact_wtf.html
{% extends "base.html" %}

{% block content %}
    <h1>Contact Us</h1>
    
    {% with messages = get_flashed_messages(with_categories=true) %}
        {% if messages %}
            {% for category, message in messages %}
                <div class="alert alert-{{ category }}">{{ message }}</div>
            {% endfor %}
        {% endif %}
    {% endwith %}
    
    <form method="POST" action="" novalidate>
        {{ form.hidden_tag() }}  {# CSRF token #}
        
        <div class="form-group">
            {{ form.name.label }}
            {{ form.name(class="form-control") }}
            {% if form.name.errors %}
                <ul class="errors">
                    {% for error in form.name.errors %}
                        <li>{{ error }}</li>
                    {% endfor %}
                </ul>
            {% endif %}
        </div>
        
        <div class="form-group">
            {{ form.email.label }}
            {{ form.email(class="form-control") }}
            {% if form.email.errors %}
                <ul class="errors">
                    {% for error in form.email.errors %}
                        <li>{{ error }}</li>
                    {% endfor %}
                </ul>
            {% endif %}
        </div>
        
        <div class="form-group">
            {{ form.subject.label }}
            {{ form.subject(class="form-control") }}
        </div>
        
        <div class="form-group">
            {{ form.message.label }}
            {{ form.message(class="form-control", rows="5") }}
            {% if form.message.errors %}
                <ul class="errors">
                    {% for error in form.message.errors %}
                        <li>{{ error }}</li>
                    {% endfor %}
                </ul>
            {% endif %}
        </div>
        
        {{ form.submit(class="btn btn-primary") }}
    </form>
{% endblock %}
````

---

## **PART 4: APPLICATION ARCHITECTURE**

### **9. Blueprints (Modular Architecture)**

Blueprints allow you to organize your Flask application into reusable modules.

#### **Project Structure with Blueprints**

```
myapp/
├── app/
│   ├── __init__.py
│   ├── models.py
│   ├── main/
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── auth/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   └── forms.py
│   ├── blog/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   └── forms.py
│   ├── api/
│   │   ├── __init__.py
│   │   └── routes.py
│   └── templates/
│       ├── base.html
│       ├── main/
│       ├── auth/
│       └── blog/
├── config.py
└── run.py
```

#### **Creating Blueprints**

````python name=app/main/__init__.py
from flask import Blueprint

main = Blueprint('main', __name__)

from app.main import routes
````

````python name=app/main/routes.py
from flask import render_template
from app.main import main

@main.route('/')
def index():
    return render_template('main/index.html')

@main.route('/about')
def about():
    return render_template('main/about.html')
````

````python name=app/auth/__init__.py
from flask import Blueprint

auth = Blueprint('auth', __name__, url_prefix='/auth')

from app.auth import routes
````

````python name=app/auth/routes.py
from flask import render_template, redirect, url_for, flash, request
from app.auth import auth

@auth.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        # Handle login
        flash('Logged in successfully!', 'success')
        return redirect(url_for('main.index'))
    return render_template('auth/login.html')

@auth.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        # Handle registration
        flash('Registration successful!', 'success')
        return redirect(url_for('auth.login'))
    return render_template('auth/register.html')

@auth.route('/logout')
def logout():
    # Handle logout
    flash('Logged out successfully!', 'info')
    return redirect(url_for('main.index'))
````

````python name=app/blog/__init__.py
from flask import Blueprint

blog = Blueprint('blog', __name__, url_prefix='/blog')

from app.blog import routes
````

````python name=app/blog/routes.py
from flask import render_template, redirect, url_for, request
from app.blog import blog

@blog.route('/')
def index():
    # Get all blog posts
    return render_template('blog/index.html')

@blog.route('/post/<int:post_id>')
def post(post_id):
    # Get specific post
    return render_template('blog/post.html', post_id=post_id)

@blog.route('/create', methods=['GET', 'POST'])
def create():
    if request.method == 'POST':
        # Create new post
        return redirect(url_for('blog.index'))
    return render_template('blog/create.html')
````

````python name=app/api/__init__.py
from flask import Blueprint

api = Blueprint('api', __name__, url_prefix='/api/v1')

from app.api import routes
````

````python name=app/api/routes.py
from flask import jsonify
from app.api import api

@api.route('/users')
def get_users():
    users = [
        {'id': 1, 'name': 'Alice'},
        {'id': 2, 'name': 'Bob'}
    ]
    return jsonify({'users': users})

@api.route('/users/<int:user_id>')
def get_user(user_id):
    return jsonify({'id': user_id, 'name': 'Alice'})
````

#### **Application Factory Pattern**

````python name=app/__init__.py
from flask import Flask
from config import config

def create_app(config_name='default'):
    app = Flask(__name__)
    app.config.from_object(config[config_name])
    
    # Register blueprints
    from app.main import main as main_blueprint
    app.register_blueprint(main_blueprint)
    
    from app.auth import auth as auth_blueprint
    app.register_blueprint(auth_blueprint)
    
    from app.blog import blog as blog_blueprint
    app.register_blueprint(blog_blueprint)
    
    from app.api import api as api_blueprint
    app.register_blueprint(api_blueprint)
    
    return app
````

````python name=run.py
from app import create_app
import os

app = create_app(os.getenv('FLASK_ENV') or 'default')

if __name__ == '__main__':
    app.run()
````

#### **URL Generation with Blueprints**

```python
# In templates or code:
url_for('main.index')           # /
url_for('auth.login')           # /auth/login
url_for('blog.post', post_id=5) # /blog/post/5
url_for('api.get_users')        # /api/v1/users
```

---

### **10. Error Handling**

````python name=error_handling.py
from flask import Flask, render_template, jsonify

app = Flask(__name__)

# 404 Error - Page Not Found
@app.errorhandler(404)
def not_found_error(error):
    # Check if API request
    if request.path.startswith('/api/'):
        return jsonify({'error': 'Resource not found'}), 404
    
    return render_template('errors/404.html'), 404

# 500 Error - Internal Server Error
@app.errorhandler(500)
def internal_error(error):
    # Log the error
    app.logger.error(f'Server Error: {error}')
    
    if request.path.startswith('/api/'):
        return jsonify({'error': 'Internal server error'}), 500
    
    return render_template('errors/500.html'), 500

# 403 Error - Forbidden
@app.errorhandler(403)
def forbidden_error(error):
    return render_template('errors/403.html'), 403

# Custom Exception
class InvalidAPIUsage(Exception):
    status_code = 400
    
    def __init__(self, message, status_code=None, payload=None):
        super().__init__()
        self.message = message
        if status_code is not None:
            self.status_code = status_code
        self.payload = payload
    
    def to_dict(self):
        rv = dict(self.payload or ())
        rv['message'] = self.message
        return rv

@app.errorhandler(InvalidAPIUsage)
def handle_invalid_usage(error):
    response = jsonify(error.to_dict())
    response.status_code = error.status_code
    return response

# Using custom exception
@app.route('/api/data')
def api_data():
    # Validate something
    raise InvalidAPIUsage('Invalid API key', status_code=401)

# Try-Except in Routes
@app.route('/divide/<int:a>/<int:b>')
def divide(a, b):
    try:
        result = a / b
        return jsonify({'result': result})
    except ZeroDivisionError:
        return jsonify({'error': 'Cannot divide by zero'}), 400
    except Exception as e:
        app.logger.error(f'Error: {e}')
        return jsonify({'error': 'An error occurred'}), 500
````

````html name=templates/errors/404.html
{% extends "base.html" %}

{% block title %}Page Not Found{% endblock %}

{% block content %}
    <div class="error-page">
        <h1>404</h1>
        <h2>Page Not Found</h2>
        <p>The page you are looking for doesn't exist or has been moved.</p>
        <a href="{{ url_for('main.index') }}" class="btn">Go Home</a>
    </div>
{% endblock %}
````

````html name=templates/errors/500.html
{% extends "base.html" %}

{% block title %}Server Error{% endblock %}

{% block content %}
    <div class="error-page">
        <h1>500</h1>
        <h2>Internal Server Error</h2>
        <p>Something went wrong on our end. We're working to fix it.</p>
        <a href="{{ url_for('main.index') }}" class="btn">Go Home</a>
    </div>
{% endblock %}
````

---

### **11. Middleware & Hooks**

````python name=middleware_hooks.py
from flask import Flask, g, request, session
import time

app = Flask(__name__)
app.secret_key = 'your-secret-key'

# before_request - Runs before each request
@app.before_request
def before_request():
    # Start timer
    g.start_time = time.time()
    
    # Log request
    app.logger.info(f'Request: {request.method} {request.path}')
    
    # Check authentication
    g.user = None
    if 'user_id' in session:
        # Load user from database
        g.user = {'id': session['user_id'], 'name': 'John'}
    
    # Block certain IPs
    # if request.remote_addr in BLOCKED_IPS:
    #     abort(403)

# after_request - Runs after each request (before response sent)
@app.after_request
def after_request(response):
    # Calculate request duration
    if hasattr(g, 'start_time'):
        duration = time.time() - g.start_time
        response.headers['X-Request-Duration'] = str(duration)
    
    # Add security headers
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'SAMEORIGIN'
    response.headers['X-XSS-Protection'] = '1; mode=block'
    
    # Log response
    app.logger.info(f'Response: {response.status_code}')
    
    return response

# teardown_request - Runs at the end (even if error occurs)
@app.teardown_request
def teardown_request(exception=None):
    # Close database connection
    # db = getattr(g, 'db', None)
    # if db is not None:
    #     db.close()
    
    if exception:
        app.logger.error(f'Request failed: {exception}')

# before_first_request - Runs once before first request (DEPRECATED in Flask 2.3+)
# Use app startup hooks instead
@app.before_first_request
def before_first_request():
    # Initialize something
    app.logger.info('First request - initializing...')

# Context processor - Makes variables available to all templates
@app.context_processor
def inject_user():
    return {'current_user': g.user}

# Template filter
@app.template_filter('format_currency')
def format_currency(value):
    return f'${value:,.2f}'

# Custom middleware class
class CustomMiddleware:
    def __init__(self, app):
        self.app = app
    
    def __call__(self, environ, start_response):
        # Do something before request
        print(f"Middleware: {environ['PATH_INFO']}")
        
        # Call the actual Flask app
        return self.app(environ, start_response)

# Apply middleware
# app.wsgi_app = CustomMiddleware(app.wsgi_app)

@app.route('/')
def home():
    # Access user from g
    if g.user:
        return f"Hello, {g.user['name']}!"
    return "Hello, Guest!"
````

---

### **12. Session Management**

````python name=session_management.py
from flask import Flask, session, redirect, url_for, request, render_template
from datetime import timedelta

app = Flask(__name__)
app.secret_key = 'your-very-secret-key-here'  # Must be secret!
app.permanent_session_lifetime = timedelta(days=7)

# Configure session
app.config['SESSION_COOKIE_NAME'] = 'my_session'
app.config['SESSION_COOKIE_HTTPONLY'] = True
app.config['SESSION_COOKIE_SECURE'] = True  # HTTPS only
app.config['SESSION_COOKIE_SAMESITE'] = 'Lax'

@app.route('/')
def index():
    # Check if user is logged in
    if 'user_id' in session:
        username = session.get('username')
        return f"Hello, {username}! <a href='/logout'>Logout</a>"
    return 'You are not logged in. <a href="/login">Login</a>'

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        
        # Validate credentials (dummy check)
        if username == 'admin' and password == 'password':
            # Make session permanent (lasts beyond browser close)
            session.permanent = True
            
            # Store data in session
            session['user_id'] = 1
            session['username'] = username
            session['role'] = 'admin'
            
            return redirect(url_for('dashboard'))
        else:
            return 'Invalid credentials', 401
    
    return '''
        <form method="post">
            Username: <input type="text" name="username"><br>
            Password: <input type="password" name="password"><br>
            <button type="submit">Login</button>
        </form>
    '''

@app.route('/dashboard')
def dashboard():
    # Check if logged in
    if 'user_id' not in session:
        return redirect(url_for('login'))
    
    username = session.get('username')
    role = session.get('role')
    
    # Access session data
    return f'''
        <h1>Dashboard</h1>
        <p>Username: {username}</p>
        <p>Role: {role}</p>
        <a href="/logout">Logout</a>
    '''

@app.route('/logout')
def logout():
    # Remove user from session
    session.pop('user_id', None)
    session.pop('username', None)
    session.pop('role', None)
    
    # Or clear entire session
    # session.clear()
    
    return redirect(url_for('index'))

# Shopping cart example
@app.route('/add-to-cart/<int:product_id>')
def add_to_cart(product_id):
    # Initialize cart if doesn't exist
    if 'cart' not in session:
        session['cart'] = []
    
    # Add product to cart
    session['cart'].append(product_id)
    session.modified = True  # Tell Flask session changed
    
    return redirect(url_for('view_cart'))

@app.route('/cart')
def view_cart():
    cart = session.get('cart', [])
    return f'Cart items: {cart}'

# Visit counter example
@app.route('/visit-counter')
def visit_counter():
    if 'visits' in session:
        session['visits'] += 1
    else:
        session['visits'] = 1
    
    return f"You have visited this page {session['visits']} times."
````

#### **Server-Side Sessions (Flask-Session)**

For production, use server-side sessions:

```bash
pip install Flask-Session redis
```

````python name=server_side_sessions.py
from flask import Flask, session
from flask_session import Session
import redis

app = Flask(__name__)

# Configure server-side sessions
app.config['SESSION_TYPE'] = 'redis'
app.config['SESSION_REDIS'] = redis.from_url('redis://localhost:6379')
app.config['SESSION_PERMANENT'] = False
app.config['SESSION_USE_SIGNER'] = True
app.config['SESSION_KEY_PREFIX'] = 'myapp:'

Session(app)

# Now sessions are stored in Redis, not cookies
````

---

## **PART 5: AUTHENTICATION & SECURITY**

### **13. Authentication & Authorization (JWT)**

#### **Basic Authentication with Flask-Login**

```bash
pip install Flask-Login werkzeug
```

````python name=models.py
from flask_login import UserMixin
from werkzeug.security import generate_password_hash, check_password_hash

class User(UserMixin):
    def __init__(self, id, username, email, password_hash):
        self.id = id
        self.username = username
        self.email = email
        self.password_hash = password_hash
    
    def set_password(self, password):
        self.password_hash = generate_password_hash(password)
    
    def check_password(self, password):
        return check_password_hash(self.password_hash, password)
    
    def __repr__(self):
        return f'<User {self.username}>'

# Dummy database
users_db = {}
````

````python name=auth_app.py
from flask import Flask, render_template, redirect, url_for, flash, request
from flask_login import LoginManager, login_user, logout_user, login_required, current_user
from werkzeug.security import generate_password_hash
from models import User, users_db

app = Flask(__name__)
app.secret_key = 'your-secret-key'

# Initialize Flask-Login
login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'
login_manager.login_message = 'Please log in to access this page.'

# User loader callback
@login_manager.user_loader
def load_user(user_id):
    return users_db.get(int(user_id))

@app.route('/')
def home():
    return render_template('home.html')

@app.route('/register', methods=['GET', 'POST'])
def register():
    if current_user.is_authenticated:
        return redirect(url_for('dashboard'))
    
    if request.method == 'POST':
        username = request.form.get('username')
        email = request.form.get('email')
        password = request.form.get('password')
        
        # Check if user exists
        if any(u.username == username for u in users_db.values()):
            flash('Username already exists.', 'error')
            return redirect(url_for('register'))
        
        # Create new user
        user_id = len(users_db) + 1
        password_hash = generate_password_hash(password)
        user = User(user_id, username, email, password_hash)
        users_db[user_id] = user
        
        flash('Registration successful! Please log in.', 'success')
        return redirect(url_for('login'))
    
    return render_template('register.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if current_user.is_authenticated:
        return redirect(url_for('dashboard'))
    
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        remember = request.form.get('remember', False)
        
        # Find user
        user = next((u for u in users_db.values() if u.username == username), None)
        
        if user and user.check_password(password):
            login_user(user, remember=remember)
            
            # Redirect to next page or dashboard
            next_page = request.args.get('next')
            return redirect(next_page or url_for('dashboard'))
        else:
            flash('Invalid username or password.', 'error')
    
    return render_template('login.html')

@app.route('/dashboard')
@login_required  # Decorator to protect route
def dashboard():
    return render_template('dashboard.html', user=current_user)

@app.route('/profile')
@login_required
def profile():
    return f'''
        <h1>Profile</h1>
        <p>Username: {current_user.username}</p>
        <p>Email: {current_user.email}</p>
        <a href="/dashboard">Dashboard</a> |
        <a href="/logout">Logout</a>
    '''

@app.route('/logout')
@login_required
def logout():
    logout_user()
    flash('You have been logged out.', 'info')
    return redirect(url_for('home'))

if __name__ == '__main__':
    app.run(debug=True)
````

#### **JWT Authentication (for APIs)**

```bash
pip install Flask-JWT-Extended
```

````python name=jwt_auth.py
from flask import Flask, jsonify, request
from flask_jwt_extended import (
    JWTManager, create_access_token, create_refresh_token,
    jwt_required, get_jwt_identity, get_jwt
)
from werkzeug.security import generate_password_hash, check_password_hash
from datetime import timedelta

app = Flask(__name__)

# JWT Configuration
app.config['JWT_SECRET_KEY'] = 'your-jwt-secret-key'  # Change this!
app.config['JWT_ACCESS_TOKEN_EXPIRES'] = timedelta(hours=1)
app.config['JWT_REFRESH_TOKEN_EXPIRES'] = timedelta(days=30)

jwt = JWTManager(app)

# Dummy user database
users = {
    'john': {
        'password': generate_password_hash('password123'),
        'role': 'user'
    },
    'admin': {
        'password': generate_password_hash('admin123'),
        'role': 'admin'
    }
}

# Register endpoint
@app.route('/api/register', methods=['POST'])
def register():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')
    
    if username in users:
        return jsonify({'error': 'User already exists'}), 400
    
    users[username] = {
        'password': generate_password_hash(password),
        'role': 'user'
    }
    
    return jsonify({'message': 'User registered successfully'}), 201

# Login endpoint
@app.route('/api/login', methods=['POST'])
def login():
    data = request.get_json()
    username = data.get('username')
    password = data.get('password')
    
    user = users.get(username)
    
    if not user or not check_password_hash(user['password'], password):
        return jsonify({'error': 'Invalid credentials'}), 401
    
    # Create tokens
    access_token = create_access_token(
        identity=username,
        additional_claims={'role': user['role']}
    )
    refresh_token = create_refresh_token(identity=username)
    
    return jsonify({
        'access_token': access_token,
        'refresh_token': refresh_token
    }), 200

# Refresh token endpoint
@app.route('/api/refresh', methods=['POST'])
@jwt_required(refresh=True)
def refresh():
    identity = get_jwt_identity()
    access_token = create_access_token(identity=identity)
    return jsonify({'access_token': access_token})

# Protected endpoint
@app.route('/api/protected', methods=['GET'])
@jwt_required()
def protected():
    current_user = get_jwt_identity()
    return jsonify({
        'message': f'Hello, {current_user}!',
        'user': current_user
    })

# Role-based access control
def admin_required():
    def wrapper(fn):
        @jwt_required()
        def decorator(*args, **kwargs):
            claims = get_jwt()
            if claims.get('role') != 'admin':
                return jsonify({'error': 'Admin access required'}), 403
            return fn(*args, **kwargs)
        decorator.__name__ = fn.__name__
        return decorator
    return wrapper

@app.route('/api/admin', methods=['GET'])
@admin_required()
def admin_only():
    return jsonify({'message': 'Admin area'})

# Logout (token blacklist - needs implementation)
@app.route('/api/logout', methods=['POST'])
@jwt_required()
def logout():
    # Implement token blacklist here
    return jsonify({'message': 'Logged out successfully'})

# Error handlers
@jwt.expired_token_loader
def expired_token_callback(jwt_header, jwt_payload):
    return jsonify({'error': 'Token has expired'}), 401

@jwt.invalid_token_loader
def invalid_token_callback(error):
    return jsonify({'error': 'Invalid token'}), 401

@jwt.unauthorized_loader
def missing_token_callback(error):
    return jsonify({'error': 'Missing authorization token'}), 401

if __name__ == '__main__':
    app.run(debug=True)
````

**Testing JWT API:**

```bash
# Register
curl -X POST http://localhost:5000/api/register \
  -H "Content-Type: application/json" \
  -d '{"username": "john", "password": "password123"}'

# Login
curl -X POST http://localhost:5000/api/login \
  -H "Content-Type: application/json" \
  -d '{"username": "john", "password": "password123"}'

# Use token
curl -X GET http://localhost:5000/api/protected \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

---

## **PART 6: DATABASE INTEGRATION**

### **14. Database Integration (SQLAlchemy)**

```bash
pip install Flask-SQLAlchemy Flask-Migrate
```

#### **Database Configuration**

````python name=config.py
import os

basedir = os.path.abspath(os.path.dirname(__file__))

class Config:
    # SQLite (development)
    SQLALCHEMY_DATABASE_URI = 'sqlite:///' + os.path.join(basedir, 'app.db')
    
    # PostgreSQL (production)
    # SQLALCHEMY_DATABASE_URI = 'postgresql://user:password@localhost/dbname'
    
    # MySQL
    # SQLALCHEMY_DATABASE_URI = 'mysql://user:password@localhost/dbname'
    
    SQLALCHEMY_TRACK_MODIFICATIONS = False
````

#### **Models**

````python name=models.py
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime

db = SQLAlchemy()

# One-to-Many Relationship
class User(db.Model):
    __tablename__ = 'users'
    
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False, index=True)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(200))
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    is_active = db.Column(db.Boolean, default=True)
    
    # Relationships
    posts = db.relationship('Post', backref='author', lazy='dynamic', cascade='all, delete-orphan')
    profile = db.relationship('Profile', backref='user', uselist=False, cascade='all, delete-orphan')
    
    def __repr__(self):
        return f'<User {self.username}>'
    
    def to_dict(self):
        return {
            'id': self.id,
            'username': self.username,
            'email': self.email,
            'created_at': self.created_at.isoformat()
        }

class Profile(db.Model):
    __tablename__ = 'profiles'
    
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    bio = db.Column(db.Text)
    website = db.Column(db.String(200))
    location = db.Column(db.String(100))
    
    def __repr__(self):
        return f'<Profile {self.user_id}>'

class Post(db.Model):
    __tablename__ = 'posts'
    
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), nullable=False)
    content = db.Column(db.Text, nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    updated_at = db.Column(db.DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
    published = db.Column(db.Boolean, default=False)
    
    # Foreign Key
    user_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    
    # Many-to-Many relationship with tags
    tags = db.relationship('Tag', secondary='post_tags', backref='posts')
    
    def __repr__(self):
        return f'<Post {self.title}>'

# Many-to-Many Relationship
post_tags = db.Table('post_tags',
    db.Column('post_id', db.Integer, db.ForeignKey('posts.id'), primary_key=True),
    db.Column('tag_id', db.Integer, db.ForeignKey('tags.id'), primary_key=True)
)

class Tag(db.Model):
    __tablename__ = 'tags'
    
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), unique=True, nullable=False)
    
    def __repr__(self):
        return f'<Tag {self.name}>'
````

#### **Application Setup with Database**

````python name=app/__init__.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from config import Config

db = SQLAlchemy()
migrate = Migrate()

def create_app(config_class=Config):
    app = Flask(__name__)
    app.config.from_object(config_class)
    
    # Initialize extensions
    db.init_app(app)
    migrate.init_app(app, db)
    
    # Import models
    from app import models
    
    # Register blueprints
    from app.main import main as main_bp
    app.register_blueprint(main_bp)
    
    return app
````

#### **Database Operations (CRUD)**

````python name=database_operations.py
from app import create_app, db
from app.models import User, Post, Tag, Profile

app = create_app()

with app.app_context():
    # CREATE
    
    # Create a user
    user = User(username='john_doe', email='john@example.com')
    user.password_hash = 'hashed_password'
    db.session.add(user)
    db.session.commit()
    
    # Create profile for user
    profile = Profile(user_id=user.id, bio='Software Developer', location='New York')
    db.session.add(profile)
    db.session.commit()
    
    # Create a post
    post = Post(
        title='My First Post',
        content='This is my first blog post!',
        user_id=user.id,
        published=True
    )
    db.session.add(post)
    db.session.commit()
    
    # Create tags and associate with post
    tag1 = Tag(name='python')
    tag2 = Tag(name='flask')
    post.tags.extend([tag1, tag2])
    db.session.commit()
    
    # READ
    
    # Get user by ID
    user = User.query.get(1)
    # or
    user = db.session.get(User, 1)
    
    # Get user by username
    user = User.query.filter_by(username='john_doe').first()
    
    # Get all users
    users = User.query.all()
    
    # Get with conditions
    active_users = User.query.filter_by(is_active=True).all()
    
    # Complex queries
    users = User.query.filter(User.email.endswith('@example.com')).all()
    
    # Order by
    users = User.query.order_by(User.created_at.desc()).all()
    
    # Limit
    recent_users = User.query.order_by(User.created_at.desc()).limit(10).all()
    
    # Pagination
    page = 1
    per_page = 10
    pagination = User.query.paginate(page=page, per_page=per_page)
    users = pagination.items
    
    # Joins
    posts_with_authors = db.session.query(Post, User).join(User).all()
    
    # Count
    user_count = User.query.count()
    
    # Exists
    exists = db.session.query(User.query.filter_by(username='john_doe').exists()).scalar()
    
    # Access relationships
    user = User.query.get(1)
    user_posts = user.posts.all()  # All posts by user
    user_profile = user.profile  # User's profile
    
    post = Post.query.get(1)
    post_author = post.author  # Post's author
    post_tags = post.tags  # Post's tags
    
    # UPDATE
    
    # Update single record
    user = User.query.get(1)
    user.email = 'newemail@example.com'
    db.session.commit()
    
    # Update multiple records
    User.query.filter_by(is_active=False).update({'is_active': True})
    db.session.commit()
    
    # DELETE
    
    # Delete single record
    user = User.query.get(1)
    db.session.delete(user)
    db.session.commit()
    
    # Delete multiple records
    Post.query.filter_by(published=False).delete()
    db.session.commit()
    
    # ADVANCED QUERIES
    
    # OR condition
    from sqlalchemy import or_
    users = User.query.filter(
        or_(User.username == 'john', User.username == 'jane')
    ).all()
    
    # AND condition
    from sqlalchemy import and_
    users = User.query.filter(
        and_(User.is_active == True, User.email.endswith('@example.com'))
    ).all()
    
    # IN clause
    usernames = ['john', 'jane', 'bob']
    users = User.query.filter(User.username.in_(usernames)).all()
    
    # LIKE
    users = User.query.filter(User.username.like('%john%')).all()
    
    # Between
    from datetime import datetime, timedelta
    week_ago = datetime.utcnow() - timedelta(days=7)
    recent_posts = Post.query.filter(Post.created_at >= week_ago).all()
    
    # Aggregate functions
    from sqlalchemy import func
    post_count = db.session.query(func.count(Post.id)).scalar()
    avg_posts_per_user = db.session.query(func.avg(func.count(Post.id))).group_by(Post.user_id).scalar()
````

#### **Database Migrations**

```bash
# Initialize migrations
flask db init

# Create a migration
flask db migrate -m "Initial migration"

# Apply migration
flask db upgrade

# Rollback migration
flask db downgrade

# Show migration history
flask db history

# Show current revision
flask db current
```

#### **Practical Example: Blog Application**

````python name=blog_routes.py
from flask import Blueprint, render_template, request, redirect, url_for, flash
from app import db
from app.models import User, Post, Tag
from flask_login import login_required, current_user

blog = Blueprint('blog', __name__, url_prefix='/blog')

@blog.route('/')
def index():
    page = request.args.get('page', 1, type=int)
    pagination = Post.query.filter_by(published=True)\
        .order_by(Post.created_at.desc())\
        .paginate(page=page, per_page=10, error_out=False)
    
    posts = pagination.items
    return render_template('blog/index.html', posts=posts, pagination=pagination)

@blog.route('/post/<int:post_id>')
def post(post_id):
    post = Post.query.get_or_404(post_id)
    return render_template('blog/post.html', post=post)

@blog.route('/create', methods=['GET', 'POST'])
@login_required
def create():
    if request.method == 'POST':
        title = request.form.get('title')
        content = request.form.get('content')
        tag_names = request.form.get('tags', '').split(',')
        
        # Create post
        post = Post(title=title, content=content, user_id=current_user.id)
        
        # Add tags
        for tag_name in tag_names:
            tag_name = tag_name.strip()
            if tag_name:
                tag = Tag.query.filter_by(name=tag_name).first()
                if not tag:
                    tag = Tag(name=tag_name)
                post.tags.append(tag)
        
        db.session.add(post)
        db.session.commit()
        
        flash('Post created successfully!', 'success')
        return redirect(url_for('blog.post', post_id=post.id))
    
    return render_template('blog/create.html')

@blog.route('/edit/<int:post_id>', methods=['GET', 'POST'])
@login_required
def edit(post_id):
    post = Post.query.get_or_404(post_id)
    
    # Check if user owns the post
    if post.user_id != current_user.id:
        flash('You cannot edit this post.', 'error')
        return redirect(url_for('blog.index'))
    
    if request.method == 'POST':
        post.title = request.form.get('title')
        post.content = request.form.get('content')
        
        db.session.commit()
        
        flash('Post updated successfully!', 'success')
        return redirect(url_for('blog.post', post_id=post.id))
    
    return render_template('blog/edit.html', post=post)

@blog.route('/delete/<int:post_id>', methods=['POST'])
@login_required
def delete(post_id):
    post = Post.query.get_or_404(post_id)
    
    if post.user_id != current_user.id:
        flash('You cannot delete this post.', 'error')
        return redirect(url_for('blog.index'))
    
    db.session.delete(post)
    db.session.commit()
    
    flash('Post deleted successfully!', 'success')
    return redirect(url_for('blog.index'))

@blog.route('/tag/<string:tag_name>')
def posts_by_tag(tag_name):
    tag = Tag.query.filter_by(name=tag_name).first_or_404()
    posts = tag.posts
    return render_template('blog/tag.html', tag=tag, posts=posts)
````

---

## **PART 7: REST API DEVELOPMENT**

### **15. Building RESTful APIs**

````python name=api_routes.py
from flask import Blueprint, jsonify, request
from app import db
from app.models import User, Post
from flask_jwt_extended import jwt_required, get_jwt_identity

api = Blueprint('api', __name__, url_prefix='/api/v1')

# Helper function for pagination metadata
def get_pagination_meta(pagination):
    return {
        'page': pagination.page,
        'per_page': pagination.per_page,
        'total': pagination.total,
        'pages': pagination.pages,
        'has_next': pagination.has_next,
        'has_prev': pagination.has_prev,
        'next_page': pagination.next_num if pagination.has_next else None,
        'prev_page': pagination.prev_num if pagination.has_prev else None
    }

# Users endpoints

@api.route('/users', methods=['GET'])
def get_users():
    """Get all users with pagination"""
    page = request.args.get('page', 1, type=int)
    per_page = request.args.get('per_page', 10, type=int)
    
    pagination = User.query.paginate(page=page, per_page=per_page, error_out=False)
    
    users = [user.to_dict() for user in pagination.items]
    
    return jsonify({
        'success': True,
        'data': users,
        'meta': get_pagination_meta(pagination)
    }), 200

@api.route('/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    """Get single user"""
    user = User.query.get_or_404(user_id)
    return jsonify({
        'success': True,
        'data': user.to_dict()
    }), 200

@api.route('/users', methods=['POST'])
def create_user():
    """Create new user"""
    data = request.get_json()
    
    # Validation
    if not data:
        return jsonify({'success': False, 'error': 'No data provided'}), 400
    
    required_fields = ['username', 'email', 'password']
    for field in required_fields:
        if field not in data:
            return jsonify({'success': False, 'error': f'{field} is required'}), 400
    
    # Check if user exists
    if User.query.filter_by(username=data['username']).first():
        return jsonify({'success': False, 'error': 'Username already exists'}), 409
    
    if User.query.filter_by(email=data['email']).first():
        return jsonify({'success': False, 'error': 'Email already exists'}), 409
    
    # Create user
    user = User(
        username=data['username'],
        email=data['email']
    )
    user.set_password(data['password'])
    
    db.session.add(user)
    db.session.commit()
    
    return jsonify({
        'success': True,
        'message': 'User created successfully',
        'data': user.to_dict()
    }), 201

@api.route('/users/<int:user_id>', methods=['PUT'])
@jwt_required()
def update_user(user_id):
    """Update user"""
    current_user_id = get_jwt_identity()
    
    # Authorization check
    if current_user_id != user_id:
        return jsonify({'success': False, 'error': 'Unauthorized'}), 403
    
    user = User.query.get_or_404(user_id)
    data = request.get_json()
    
    # Update fields
    if 'email' in data:
        user.email = data['email']
    if 'username' in data:
        user.username = data['username']
    
    db.session.commit()
    
    return jsonify({
        'success': True,
        'message': 'User updated successfully',
        'data': user.to_dict()
    }), 200

@api.route('/users/<int:user_id>', methods=['DELETE'])
@jwt_required()
def delete_user(user_id):
    """Delete user"""
    user = User.query.get_or_404(user_id)
    
    db.session.delete(user)
    db.session.commit()
    
    return jsonify({
        'success': True,
        'message': 'User deleted successfully'
    }), 200

# Posts endpoints

@api.route('/posts', methods=['GET'])
def get_posts():
    """Get all posts"""
    page = request.args.get('page', 1, type=int)
    per_page = request.args.get('per_page', 10, type=int)
    published = request.args.get('published', type=bool)
    
    query = Post.query
    
    if published is not None:
        query = query.filter_by(published=published)
    
    pagination = query.order_by(Post.created_at.desc())\
        .paginate(page=page, per_page=per_page, error_out=False)
    
    posts = [{
        'id': post.id,
        'title': post.title,
        'content': post.content,
        'published': post.published,
        'created_at': post.created_at.isoformat(),
        'author': {
            'id': post.author.id,
            'username': post.author.username
        },
        'tags': [tag.name for tag in post.tags]
    } for post in pagination.items]
    
    return jsonify({
        'success': True,
        'data': posts,
        'meta': get_pagination_meta(pagination)
    }), 200

@api.route('/posts/<int:post_id>', methods=['GET'])
def get_post(post_id):
    """Get single post"""
    post = Post.query.get_or_404(post_id)
    
    return jsonify({
        'success': True,
        'data': {
            'id': post.id,
            'title': post.title,
            'content': post.content,
            'published': post.published,
            'created_at': post.created_at.isoformat(),
            'updated_at': post.updated_at.isoformat(),
            'author': {
                'id': post.author.id,
                'username': post.author.username,
                'email': post.author.email
            },
            'tags': [{'id': tag.id, 'name': tag.name} for tag in post.tags]
        }
    }), 200

@api.route('/posts', methods=['POST'])
@jwt_required()
def create_post():
    """Create new post"""
    data = request.get_json()
    current_user_id = get_jwt_identity()
    
    if not data or 'title' not in data or 'content' not in data:
        return jsonify({'success': False, 'error': 'Title and content are required'}), 400
    
    post = Post(
        title=data['title'],
        content=data['content'],
        published=data.get('published', False),
        user_id=current_user_id
    )
    
    db.session.add(post)
    db.session.commit()
    
    return jsonify({
        'success': True,
        'message': 'Post created successfully',
        'data': {'id': post.id, 'title': post.title}
    }), 201

@api.route('/posts/<int:post_id>', methods=['PUT', 'PATCH'])
@jwt_required()
def update_post(post_id):
    """Update post"""
    post = Post.query.get_or_404(post_id)
    current_user_id = get_jwt_identity()
    
    if post.user_id != current_user_id:
        return jsonify({'success': False, 'error': 'Unauthorized'}), 403
    
    data = request.get_json()
    
    if 'title' in data:
        post.title = data['title']
    if 'content' in data:
        post.content = data['content']
    if 'published' in data:
        post.published = data['published']
    
    db.session.commit()
    
    return jsonify({
        'success': True,
        'message': 'Post updated successfully',
        'data': {'id': post.id, 'title': post.title}
    }), 200

@api.route('/posts/<int:post_id>', methods=['DELETE'])
@jwt_required()
def delete_post(post_id):
    """Delete post"""
    post = Post.query.get_or_404(post_id)
    current_user_id = get_jwt_identity()
    
    if post.user_id != current_user_id:
        return jsonify({'success': False, 'error': 'Unauthorized'}), 403
    
    db.session.delete(post)
    db.session.commit()
    
    return jsonify({
        'success': True,
        'message': 'Post deleted successfully'
    }), 200

# Search endpoint
@api.route('/search', methods=['GET'])
def search():
    """Search posts"""
    query = request.args.get('q', '')
    
    if not query:
        return jsonify({'success': False, 'error': 'Query parameter required'}), 400
    
    posts = Post.query.filter(
        (Post.title.contains(query)) | (Post.content.contains(query))
    ).all()
    
    results = [{
        'id': post.id,
        'title': post.title,
        'excerpt': post.content[:100] + '...'
    } for post in posts]
    
    return jsonify({
        'success': True,
        'query': query,
        'count': len(results),
        'data': results
    }), 200
````

#### **API Best Practices**

````python name=api_best_practices.py
from flask import Blueprint, jsonify, request
from functools import wraps

api = Blueprint('api', __name__)

# 1. Versioning
# Put version in URL: /api/v1/users

# 2. Consistent Response Format
def api_response(success, data=None, error=None, status=200):
    response = {'success': success}
    if data is not None:
        response['data'] = data
    if error:
        response['error'] = error
    return jsonify(response), status

# 3. Rate Limiting (using Flask-Limiter)
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)

@api.route('/api/resource')
@limiter.limit("10 per minute")
def limited_route():
    return api_response(True, {'message': 'Success'})

# 4. CORS (Cross-Origin Resource Sharing)
from flask_cors import CORS

# Enable CORS for all routes
CORS(api, resources={r"/api/*": {"origins": "*"}})

# 5. Input Validation with Marshmallow
from marshmallow import Schema, fields, validate, ValidationError

class UserSchema(Schema):
    username = fields.Str(required=True, validate=validate.Length(min=3, max=50))
    email = fields.Email(required=True)
    age = fields.Int(validate=validate.Range(min=18, max=120))

@api.route('/api/users', methods=['POST'])
def create_user_validated():
    schema = UserSchema()
    try:
        data = schema.load(request.get_json())
        # Process valid data
        return api_response(True, data, status=201)
    except ValidationError as err:
        return api_response(False, error=err.messages, status=400)

# 6. Error Handling
@api.errorhandler(404)
def not_found(error):
    return api_response(False, error='Resource not found', status=404)

@api.errorhandler(500)
def internal_error(error):
    return api_response(False, error='Internal server error', status=500)

# 7. Pagination Helper
def paginate(query, page, per_page):
    pagination = query.paginate(page=page, per_page=per_page, error_out=False)
    return {
        'items': [item.to_dict() for item in pagination.items],
        'meta': {
            'page': page,
            'per_page': per_page,
            'total': pagination.total,
            'pages': pagination.pages
        }
    }

# 8. HATEOAS (Hypermedia)
def add_links(resource_id, resource_type):
    return {
        'self': f'/api/{resource_type}/{resource_id}',
        'collection': f'/api/{resource_type}'
    }
````

---

## **PART 8: TESTING**

### **17. Testing Flask Applications**

```bash
pip install pytest pytest-flask pytest-cov
```

#### **Test Configuration**

````python name=tests/conftest.py
import pytest
from app import create_app, db
from app.models import User, Post
from config import Config

class TestConfig(Config):
    TESTING = True
    SQLALCHEMY_DATABASE_URI = 'sqlite://'  # In-memory database
    WTF_CSRF_ENABLED = False

@pytest.fixture
def app():
    app = create_app(TestConfig)
    
    with app.app_context():
        db.create_all()
        yield app
        db.session.remove()
        db.drop_all()

@pytest.fixture
def client(app):
    return app.test_client()

@pytest.fixture
def runner(app):
    return app.test_cli_runner()

@pytest.fixture
def sample_user(app):
    user = User(username='testuser', email='test@example.com')
    user.set_password('password123')
    db.session.add(user)
    db.session.commit()
    return user

@pytest.fixture
def sample_post(app, sample_user):
    post = Post(
        title='Test Post',
        content='This is a test post',
        user_id=sample_user.id
    )
    db.session.add(post)
    db.session.commit()
    return post
````

#### **Unit Tests**

````python name=tests/test_models.py
import pytest
from app.models import User, Post
from app import db

def test_user_creation(app):
    """Test creating a user"""
    with app.app_context():
        user = User(username='john', email='john@example.com')
        user.set_password('password123')
        
        db.session.add(user)
        db.session.commit()
        
        assert user.id is not None
        assert user.username == 'john'
        assert user.check_password('password123')
        assert not user.check_password('wrongpassword')

def test_user_password_hashing(app):
    """Test password hashing"""
    with app.app_context():
        user = User(username='john', email='john@example.com')
        user.set_password('mypassword')
        
        assert user.password_hash != 'mypassword'
        assert user.check_password('mypassword')

def test_post_creation(app, sample_user):
    """Test creating a post"""
    with app.app_context():
        post = Post(
            title='Test Post',
            content='Content here',
            user_id=sample_user.id
        )
        db.session.add(post)
        db.session.commit()
        
        assert post.id is not None
        assert post.author.username == 'testuser'

def test_user_posts_relationship(app, sample_user):
    """Test user-posts relationship"""
    with app.app_context():
        post1 = Post(title='Post 1', content='Content 1', user_id=sample_user.id)
        post2 = Post(title='Post 2', content='Content 2', user_id=sample_user.id)
        
        db.session.add_all([post1, post2])
        db.session.commit()
        
        user = User.query.get(sample_user.id)
        assert user.posts.count() == 2
````

#### **Integration Tests**

````python name=tests/test_routes.py
import pytest
from flask import url_for

def test_home_page(client):
    """Test home page loads"""
    response = client.get('/')
    assert response.status_code == 200
    assert b'Welcome' in response.data

def test_404_page(client):
    """Test 404 error handler"""
    response = client.get('/nonexistent')
    assert response.status_code == 404

def test_user_registration(client):
    """Test user registration"""
    response = client.post('/auth/register', data={
        'username': 'newuser',
        'email': 'newuser@example.com',
        'password': 'password123',
        'confirm_password': 'password123'
    }, follow_redirects=True)
    
    assert response.status_code == 200
    assert b'Registration successful' in response.data

def test_user_login(client, sample_user):
    """Test user login"""
    response = client.post('/auth/login', data={
        'username': 'testuser',
        'password': 'password123'
    }, follow_redirects=True)
    
    assert response.status_code == 200
    assert b'Dashboard' in response.data

def test_user_login_invalid(client):
    """Test login with invalid credentials"""
    response = client.post('/auth/login', data={
        'username': 'invalid',
        'password': 'wrong'
    }, follow_redirects=True)
    
    assert response.status_code == 200
    assert b'Invalid' in response.data

def test_protected_route_without_login(client):
    """Test accessing protected route without login"""
    response = client.get('/dashboard')
    assert response.status_code == 302  # Redirect to login

def test_create_post(client, sample_user):
    """Test creating a post"""
    # Login first
    client.post('/auth/login', data={
        'username': 'testuser',
        'password': 'password123'
    })
    
    response = client.post('/blog/create', data={
        'title': 'New Post',
        'content': 'This is my new post',
        'tags': 'python,flask'
    }, follow_redirects=True)
    
    assert response.status_code == 200
    assert b'Post created' in response.data
````

#### **API Tests**

````python name=tests/test_api.py
import pytest
import json

def test_api_get_users(client):
    """Test GET /api/users"""
    response = client.get('/api/v1/users')
    assert response.status_code == 200
    
    data = json.loads(response.data)
    assert data['success'] == True
    assert 'data' in data

def test_api_create_user(client):
    """Test POST /api/users"""
    response = client.post('/api/v1/users',
        data=json.dumps({
            'username': 'apiuser',
            'email': 'api@example.com',
            'password': 'password123'
        }),
        content_type='application/json'
    )
    
    assert response.status_code == 201
    data = json.loads(response.data)
    assert data['success'] == True
    assert data['data']['username'] == 'apiuser'

def test_api_get_posts(client, sample_post):
    """Test GET /api/posts"""
    response = client.get('/api/v1/posts')
    assert response.status_code == 200
    
    data = json.loads(response.data)
    assert len(data['data']) > 0

def test_api_authentication_required(client):
    """Test protected endpoint requires auth"""
    response = client.post('/api/v1/posts',
        data=json.dumps({'title': 'Test', 'content': 'Test'}),
        content_type='application/json'
    )
    assert response.status_code == 401

def test_api_with_jwt_token(client, sample_user):
    """Test API with JWT authentication"""
    # Get token
    response = client.post('/api/login',
        data=json.dumps({
            'username': 'testuser',
            'password': 'password123'
        }),
        content_type='application/json'
    )
    
    data = json.loads(response.data)
    token = data['access_token']
    
    # Use token
    response = client.post('/api/v1/posts',
        data=json.dumps({
            'title': 'Authenticated Post',
            'content': 'Created with JWT'
        }),
        headers={'Authorization': f'Bearer {token}'},
        content_type='application/json'
    )
    
    assert response.status_code == 201
````

#### **Running Tests**

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/test_models.py

# Run with coverage
pytest --cov=app tests/

# Generate coverage report
pytest --cov=app --cov-report=html tests/

# Run verbose
pytest -v

# Run and stop on first failure
pytest -x

# Run specific test
pytest tests/test_models.py::test_user_creation
```

---

## **PART 9: DEPLOYMENT**

### **18. Deployment**

#### **Preparing for Production**

````python name=config.py
import os

class ProductionConfig:
    DEBUG = False
    TESTING = False
    
    SECRET_KEY = os.environ.get('SECRET_KEY')
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL')
    
    # Security
    SESSION_COOKIE_SECURE = True
    SESSION_COOKIE_HTTPONLY = True
    SESSION_COOKIE_SAMESITE = 'Lax'
    
    # Performance
    SQLALCHEMY_POOL_SIZE = 10
    SQLALCHEMY_MAX_OVERFLOW = 20
````

#### **Using Gunicorn**

```bash
pip install gunicorn
```

````python name=wsgi.py
from app import create_app

app = create_app('production')

if __name__ == '__main__':
    app.run()
````

**Run with Gunicorn:**
```bash
gunicorn -w 4 -b 0.0.0.0:8000 wsgi:app

# With more options
gunicorn -w 4 \
  --bind 0.0.0.0:8000 \
  --timeout 120 \
  --access-logfile - \
  --error-logfile - \
  wsgi:app
```

#### **Environment Variables**

````bash name=.env.production
FLASK_APP=wsgi.py
FLASK_ENV=production
SECRET_KEY=your-production-secret-key
DATABASE_URL=postgresql://user:password@localhost/dbname
JWT_SECRET_KEY=your-jwt-secret
````

#### **Docker Deployment**

````dockerfile name=Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Create non-root user
RUN useradd -m flaskuser && chown -R flaskuser:flaskuser /app
USER flaskuser

EXPOSE 8000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:8000", "wsgi:app"]
````

````yaml name=docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - FLASK_ENV=production
      - SECRET_KEY=${SECRET_KEY}
      - DATABASE_URL=postgresql://postgres:password@db:5432/flaskapp
    depends_on:
      - db
    volumes:
      - ./app:/app
  
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=flaskapp
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
````

**Build and run:**
```bash
docker-compose up --build
```

#### **Nginx Configuration**

````nginx name=/etc/nginx/sites-available/flask-app
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static {
        alias /path/to/your/app/static;
        expires 30d;
    }
}
````

Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/flask-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

## **ADDITIONAL IMPORTANT TOPICS**

### **19. Flask Extensions Deep Dive**

#### **Flask-Mail (Email Sending)**

```bash
pip install Flask-Mail
```

````python name=email_config.py
from flask_mail import Mail, Message

app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = os.environ.get('MAIL_USERNAME')
app.config['MAIL_PASSWORD'] = os.environ.get('MAIL_PASSWORD')

mail = Mail(app)

def send_email(subject, recipient, template):
    msg = Message(
        subject,
        recipients=[recipient],
        html=template,
        sender=app.config['MAIL_USERNAME']
    )
    mail.send(msg)
````

#### **Flask-Caching**

```bash
pip install Flask-Caching
```

````python name=caching.py
from flask_caching import Cache

cache = Cache(config={'CACHE_TYPE': 'SimpleCache'})
cache.init_app(app)

@app.route('/expensive-operation')
@cache.cached(timeout=300)  # Cache for 5 minutes
def expensive():
    # Expensive operation
    return result
````

#### **Flask-Admin (Admin Panel)**

```bash
pip install Flask-Admin
```

````python name=admin_setup.py
from flask_admin import Admin
from flask_admin.contrib.sqla import ModelView

admin = Admin(app, name='MyApp', template_mode='bootstrap4')
admin.add_view(ModelView(User, db.session))
admin.add_view(ModelView(Post, db.session))
````

---

### **20. Advanced Patterns**

#### **Application Factory with Blueprints (Complete Example)**

````python name=app/__init__.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager
from flask_migrate import Migrate
from config import config

db = SQLAlchemy()
login_manager = LoginManager()
migrate = Migrate()

def create_app(config_name='default'):
    app = Flask(__name__)
    app.config.from_object(config[config_name])
    
    # Initialize extensions
    db.init_app(app)
    login_manager.init_app(app)
    migrate.init_app(app, db)
    
    # Configure login manager
    login_manager.login_view = 'auth.login'
    
    # Register blueprints
    from app.main import main as main_bp
    app.register_blueprint(main_bp)
    
    from app.auth import auth as auth_bp
    app.register_blueprint(auth_bp, url_prefix='/auth')
    
    from app.api import api as api_bp
    app.register_blueprint(api_bp, url_prefix='/api/v1')
    
    return app
````

---

## **LEARNING ROADMAP**

### **Week 1-2: Foundations**
- ✅ Setup environment
- ✅ Basic routing
- ✅ Templates
- ✅ Forms
- **Project**: Build a simple blog with posts and comments

### **Week 3-4: Intermediate**
- ✅ Database integration
- ✅ User authentication
- ✅ Blueprints
- ✅ Error handling
- **Project**: Multi-user blog platform

### **Week 5-6: Advanced**
- ✅ REST API development
- ✅ JWT authentication
- ✅ Testing
- **Project**: RESTful API with documentation

### **Week 7-8: Production**
- ✅ Deployment
- ✅ Docker
- ✅ Performance optimization
- **Project**: Deploy full application

---

## **PRACTICE PROJECTS**

1. **To-Do List Application**
   - CRUD operations
   - User accounts
   - Categories and tags

2. **Blog Platform**
   - Posts and comments
   - User authentication
   - Rich text editor
   - Search functionality

3. **E-commerce API**
   - Product catalog
   - Shopping cart
   - Order management
   - Payment integration

4. **Social Media Clone**
   - User profiles
   - Posts and likes
   - Follow system
   - Real-time notifications

---

## **RESOURCES**

**Official Documentation:**
- Flask: https://flask.palletsprojects.com/
- SQLAlchemy: https://www.sqlalchemy.org/
- Jinja2: https://jinja.palletsprojects.com/

**Books:**
- "Flask Web Development" by Miguel Grinberg
- "Flask Framework Cookbook"

**Video Courses:**
- Corey Schafer's Flask Tutorial (YouTube)
- Miguel Grinberg's Flask Mega-Tutorial

---

This guide covers Flask from absolute beginner to advanced level. Start with Part 1 and work your way through each section, building the practice projects as you go. Feel free to ask questions about any specific topic!
