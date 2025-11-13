# NOTES App Using Django, React, and SQLite

> A full-stack web application demonstrating user authentication, JWT-based authorization, and CRUD operations for personal notes.

[![Python](https://img.shields.io/badge/Python-3.8+-green.svg)](https://www.python.org/)
[![Django](https://img.shields.io/badge/Django-5.2-darkgreen.svg)](https://www.djangoproject.com/)
[![React](https://img.shields.io/badge/React-19.1-blue.svg)](https://react.dev/)
[![Project Documentation](https://www.svgrepo.com/svg/522308/text-document)](https://www.notion.so/Full-Stack-Django-React-2a06217989ea80658896d2e40e0eceab/)

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Backend](#backend)
- [Frontend](#frontend)
- [Getting Started](#getting-started)
- [API Documentation](#api-documentation)
- [Configuration](#configuration)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)

## Overview

This repository contains a full-stack notes application that demonstrates how a **Django** backend and a **React** frontend work together using **SQLite** as the database. Users can register, authenticate with JSON Web Tokens (JWT), and manage personal notes through a RESTful API.

The application showcases modern web development practices including:
- JWT-based authentication and authorization
- RESTful API design with DRF (Django Rest Framework)
- React component architecture with routing
- Cross-Origin Resource Sharing (CORS) configuration
- Environment-based configuration management
- Responsive user interface

## Features

### User Management
- User registration with username and password
- Secure JWT-based authentication
- Token refresh mechanism for extended sessions
- Logout functionality with localStorage cleanup

### Notes Management
- Create new notes with title and content
- View all personal notes with timestamps
- Delete notes (owner-only access)
- Notes are automatically linked to authenticated user

### Security
- Protected routes requiring authentication
- User-specific note access (users can only view/modify their own notes)
- JWT token expiration (30 minutes access, 1 day refresh)
- CORS configuration for frontend-backend communication

## Tech Stack

### Backend
- **Django 5.2** - Web framework
- **Django Rest Framework (DRF) 3.16** - API development
- **Django Simple JWT 5.5** - JWT authentication
- **django-cors-headers 4.9** - CORS support
- **SQLite** - Database
- **Gunicorn** - WSGI HTTP server
- **Python 3.11+**

### Frontend
- **React 19.1** - UI library
- **React Router DOM 7.9** - Client-side routing
- **Axios 1.13** - HTTP client
- **Vite 7.1** - Build tool and development server
- **jwt-decode 4.0** - JWT token decoding
- **CSS3** - Styling

## Project Structure

```
NOTES_App_using_Django_React_SQLite/
├── backend/                          # Django backend
│   ├── manage.py                     # Django management script
│   ├── db.sqlite3                    # SQLite database
│   ├── requirements.txt              # Python dependencies
│   ├── build.sh                      # Deployment script
│   │
│   ├── api/                          # Main API app
│   │   ├── models.py                 # Note model definition
│   │   ├── views.py                  # API views (ListCreate, Delete)
│   │   ├── serializers.py            # DRF serializers
│   │   ├── urls.py                   # API URL routing
│   │   ├── admin.py                  # Admin configuration
│   │   ├── tests.py                  # Unit tests
│   │   └── migrations/               # Database migrations
│   │
│   └── backend/                      # Django project settings
│       ├── settings.py               # Main settings (DEBUG, INSTALLED_APPS, etc.)
│       ├── urls.py                   # Root URL configuration
│       ├── wsgi.py                   # WSGI entry point
│       ├── asgi.py                   # ASGI entry point
│       └── deployment_settings.py    # Production settings
│
├── frontend/                         # React frontend
│   ├── package.json                  # Node dependencies
│   ├── vite.config.js                # Vite configuration
│   ├── index.html                    # Entry HTML file
│   │
│   ├── src/
│   │   ├── main.jsx                  # React app entry point
│   │   ├── App.jsx                   # Main app component with routing
│   │   ├── api.js                    # Axios API client with interceptors
│   │   ├── constants.js              # App constants (ACCESS_TOKEN, etc.)
│   │   │
│   │   ├── components/               # Reusable components
│   │   │   ├── ProtectedRoute.jsx    # Route guard for authenticated pages
│   │   │   ├── Form.jsx              # Reusable form component
│   │   │   ├── Note.jsx              # Individual note component
│   │   │   └── LoadingIndicator.jsx  # Loading state indicator
│   │   │
│   │   ├── pages/                    # Page components
│   │   │   ├── Login.jsx             # Login page
│   │   │   ├── Register.jsx          # Registration page
│   │   │   ├── Home.jsx              # Notes dashboard
│   │   │   └── NotFound.jsx          # 404 error page
│   │   │
│   │   ├── styles/                   # CSS stylesheets
│   │   │   ├── Form.css
│   │   │   ├── Home.css
│   │   │   ├── Note.css
│   │   │   └── LoadingIndicator.css
│   │   │
│   │   ├── assets/                   # Static assets
│   │   ├── App.css                   # Main app styles
│   │   └── index.css                 # Global styles
│   │
│   ├── public/                       # Public static files
│   └── eslint.config.js              # ESLint configuration
│
└── readme.md                         # This file
```

## Backend

### Architecture

The backend is built with **Django** and **Django Rest Framework (DRF)** following REST API best practices.

### Core Models

#### User Model
- Uses Django's built-in `User` model
- Fields: `username`, `password` (hashed), authentication metadata

#### Note Model
```python
class Note(models.Model):
    title = CharField(max_length=100)          # Note title
    content = TextField()                      # Note content
    created_at = DateTimeField(auto_now_add)  # Auto-set timestamp
    author = ForeignKey(User)                 # Foreign key to User
```

### API Endpoints

#### Authentication Endpoints
| Method | Endpoint | Description | Authentication |
|--------|----------|-------------|-----------------|
| POST | `/api/user/register/` | Register a new user | No |
| POST | `/api/token/` | Obtain JWT access & refresh tokens | No |
| POST | `/api/token/refresh/` | Refresh expired access token | No |

#### Notes Endpoints
| Method | Endpoint | Description | Authentication |
|--------|----------|-------------|-----------------|
| GET | `/api/notes/` | List all notes of authenticated user | Required |
| POST | `/api/notes/` | Create a new note | Required |
| DELETE | `/api/notes/delete/<id>/` | Delete a specific note | Required |

### Request/Response Examples

**Register User:**
```bash
POST /api/user/register/
Content-Type: application/json

{
  "username": "john_doe",
  "password": "secure_password123"
}

Response: 201 Created
{
  "id": 1,
  "username": "john_doe"
}
```

**Obtain Token:**
```bash
POST /api/token/
Content-Type: application/json

{
  "username": "john_doe",
  "password": "secure_password123"
}

Response: 200 OK
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGc..."
}
```

**Create Note:**
```bash
POST /api/notes/
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "title": "My First Note",
  "content": "This is the content of my first note."
}

Response: 201 Created
{
  "id": 1,
  "title": "My First Note",
  "content": "This is the content of my first note.",
  "created_at": "2025-11-13T10:30:00Z",
  "author": 1
}
```

**List Notes:**
```bash
GET /api/notes/
Authorization: Bearer <access_token>

Response: 200 OK
[
  {
    "id": 1,
    "title": "My First Note",
    "content": "This is the content of my first note.",
    "created_at": "2025-11-13T10:30:00Z",
    "author": 1
  }
]
```

**Delete Note:**
```bash
DELETE /api/notes/delete/1/
Authorization: Bearer <access_token>

Response: 204 No Content
```

### Key Features

- **JWT Authentication**: 30-minute access token lifetime, 1-day refresh token lifetime
- **Permission Classes**: All note endpoints require authentication; registration endpoint is public
- **Querysets Filtering**: Each user can only view/modify their own notes
- **CORS Support**: Configured to accept requests from frontend
- **Error Handling**: Returns meaningful HTTP status codes and error messages

## Frontend

### Architecture

The frontend is built with **React 19** using modern hooks, **Vite** for fast development, and **React Router** for client-side navigation.

### Component Structure

#### Pages
- **Login.jsx** - User login form and authentication
- **Register.jsx** - User registration form
- **Home.jsx** - Main dashboard displaying notes with create/delete functionality
- **NotFound.jsx** - 404 error page for invalid routes

#### Components
- **ProtectedRoute.jsx** - Route guard that redirects unauthenticated users to login
- **Form.jsx** - Reusable form component for login/register/create note forms
- **Note.jsx** - Individual note card component with delete button
- **LoadingIndicator.jsx** - Loading spinner for async operations

### Key Features

#### Authentication Flow
1. User registers or logs in
2. Backend returns JWT tokens (access + refresh)
3. Tokens stored in browser's localStorage
4. Axios interceptor adds access token to all API requests
5. Protected routes check for token and redirect if missing

#### State Management
- Uses React hooks (`useState`, `useEffect`) for local component state
- localStorage for persisting JWT tokens across sessions
- Custom hook patterns for reusable logic

#### API Integration
- Centralized API client (`api.js`) using Axios
- Automatic token injection via request interceptor
- Environment-based API URLs (local development vs. production)
- Error handling and loading states

### Environment Configuration

```env
# Frontend/.env
VITE_API_BASE_URL_LOCAL="http://localhost:8000/"        # Development
```

### Routing

```javascript
/ → Home (Protected)
/login → Login page
/register → Register page
/logout → Clears tokens and redirects to login
/* → 404 Not Found
```

## Getting Started

### Prerequisites

- **Python 3.8+**
- **Node.js 18+** and **npm** (or yarn)
- **Git**

### Backend Setup

1. **Clone the repository**

   ```bash
   git clone <repo-url>
   cd NOTES_App_using_Django_React_SQLite
   ```

2. **Create and activate a virtual environment**

   **Windows:**
   ```bash
   python -m venv venv
   .\venv\Scripts\activate
   ```

   **macOS/Linux:**
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Install backend dependencies**

   ```bash
   cd backend
   pip install -r requirements.txt
   ```

4. **Apply database migrations**

   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

5. **Create a superuser (optional, for admin panel)**

   ```bash
   python manage.py createsuperuser
   ```

6. **Run the backend server**

   ```bash
   python manage.py runserver
   ```

   Backend available at: `http://127.0.0.1:8000/`

### Frontend Setup

1. **Navigate to frontend folder**

   ```bash
   cd ../frontend
   ```

2. **Install frontend dependencies**

   ```bash
   npm install
   ```

3. **Create or update `.env` file**

   ```env
   VITE_API_BASE_URL_LOCAL="http://localhost:8000/"
   ```

4. **Run development server**

   ```bash
   npm run dev
   ```

   Frontend available at: `http://localhost:5173/`

5. **Build for production**

   ```bash
   npm run build
   ```

### Testing the Application

1. **Start both servers** (backend on 8000, frontend on 5173)
2. **Navigate to** `http://localhost:5173/`
3. **Register** a new account
4. **Login** with your credentials
5. **Create, view, and delete** notes
6. **Test token refresh** by waiting 30+ minutes or manually refreshing the token

## API Documentation

### Authentication

All protected endpoints require JWT token in the `Authorization` header:

```
Authorization: Bearer <your_access_token>
```

### Status Codes

| Code | Meaning |
|------|---------|
| 200 | OK - Request successful |
| 201 | Created - Resource created |
| 204 | No Content - Successful deletion |
| 400 | Bad Request - Invalid input |
| 401 | Unauthorized - Missing or invalid token |
| 403 | Forbidden - No permission to access resource |
| 404 | Not Found - Resource not found |
| 500 | Server Error - Backend error |

### Token Management

**Access Token Lifetime**: 30 minutes
**Refresh Token Lifetime**: 1 day

To refresh an expired access token:
```bash
POST /api/token/refresh/
Content-Type: application/json

{
  "refresh": "<refresh_token>"
}

Response: 200 OK
{
  "access": "<new_access_token>"
}
```

## Configuration

### Backend Settings

Edit `backend/backend/settings.py`:

```python
# Enable/Disable debug mode
DEBUG = True  # Set to False in production

# Allowed hosts
ALLOWED_HOSTS = ["*"]  # Specify domains in production

# JWT Token Lifetime
SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=30),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
}

# CORS Configuration (if needed for different origin)
CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",
    "https://your-frontend-domain.com",
]
```

### Frontend Configuration

Edit `frontend/.env`:

```env
# Development
VITE_API_BASE_URL_LOCAL="http://localhost:8000/"

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/AmazingFeature`)
3. **Commit** your changes (`git commit -m 'Add some AmazingFeature'`)
4. **Push** to the branch (`git push origin feature/AmazingFeature`)
5. **Open** a Pull Request

## Support

For issues, questions, or suggestions:
- **Open an issue** on GitHub
- **Check existing documentation** and discussions
- **Review API endpoints** in the API Documentation section
