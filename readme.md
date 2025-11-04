### [Read Documentation on the project](https://www.notion.so/Full-Stack-Django-React-2a06217989ea80658896d2e40e0eceab)

# NOTES App Using Django, React, and SQLite

This repository contains a simple notes application that demonstrates how a **Django** backend and a **React** frontend can work together using **SQLite** as the database.  Users can register, authenticate with JSON Web Tokens (JWT) and create personal notes through a RESTful API.

## Backend

The backend is built with **Django** and **Django Rest Framework (DRF)**.  It exposes a small API with the following features:

- **User registration and authentication** – users sign up by providing a username and password.  Upon successful authentication the API issues JWT access and refresh tokens.  These tokens are used in the `Authorization` header to authenticate subsequent requests.  JWTs allow the server to verify a user and prevent unauthorized access to resources because they carry a cryptographic signature【106748125677202†L109-L114】.

- **Note model** – each note has a `title`, `content`, and `created_at` timestamp.  Notes are linked to the authenticated user via a foreign key.  Users can only see and modify their own notes.

- **CRUD endpoints** – authenticated users can list their notes, create new notes, or delete existing notes through the API.  Unauthenticated requests are rejected because DRF’s default permissions and authentication classes are set to require authentication【131740627892243†L139-L142】.

### Key API endpoints

- `POST /api/user/register/` – create a new user by sending a JSON payload with `username` and `password`.
- `POST /api/token/` – obtain an access and refresh token pair by sending valid credentials.
- `POST /api/token/refresh/` – refresh an expired access token by sending the refresh token.
- `GET /api/notes/` – list the notes belonging to the authenticated user.
- `POST /api/notes/` – create a new note for the authenticated user.
- `DELETE /api/notes/delete/<id>/` – delete a specific note (only the owner can delete).

For endpoints that require authentication, include the JWT access token in the `Authorization` header: `Authorization: Bearer <your_access_token>`.

## Setup Instructions

Follow these steps in a terminal on Windows (similar commands apply for macOS/Linux):

1. **Clone the repository**

   ```bash
   git clone <repo-url>
   ```

2. **Create and activate a virtual environment**

   ```bash
   python -m venv venv
   .\venv\Scripts\activate
   ```

   On macOS/Linux use `python3 -m venv venv && source venv/bin/activate` instead.

3. **Install dependencies**

   Install the backend requirements using pip:

   ```bash
   pip install -r requirements.txt
   ```

4. **Navigate to the backend folder**

   ```bash
   cd backend
   ```

5. **Apply migrations** – create the database tables based on the models:

   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

   Running migrations ensures that your database schema matches the current models before you start the server【131740627892243†L149-L163】.

6. **Run the development server**

   ```bash
   python manage.py runserver
   ```

   By default, the backend will be available at `http://127.0.0.1:8000/`.

Once the server is running you can test the API with a REST client such as Postman, Insomnia or using the browser for GET requests.  Remember to include your JWT access token in the `Authorization` header when accessing protected endpoints.

## Next Steps

This README focuses on the backend portion of the project.  The next stage is to implement the React frontend, connect it to these endpoints and manage user authentication and note management in the user interface.  Refer to the project documentation for instructions on setting up the frontend and deploying the full stack application.
