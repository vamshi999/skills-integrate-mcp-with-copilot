# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Teachers can register and unregister students after logging in
- Students can view activities and participant lists without logging in

## Configure teacher accounts

Copy the example file to `src/teachers.json` and add each teacher account to
its `teachers` list. This local credentials file is ignored by Git. Passwords
must be stored as salted PBKDF2 hashes rather than plaintext. Generate a hash
from the repository root:

```
cp src/teachers.example.json src/teachers.json
python -c "from src.app import hash_teacher_password; print(hash_teacher_password('replace-with-a-strong-password'))"
```

Add the username and generated hash to `src/teachers.json`, then restart the
application. The example starts with no accounts configured. Keep teacher
credentials private and use unique, strong passwords; do not commit
`src/teachers.json`.

Successful logins use an HTTP-only, same-site session cookie that expires
after eight hours. Sessions are held in memory and end when the server
restarts.

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   python app.py
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| GET    | `/auth/me`                                                        | Check whether the current browser is logged in as a teacher          |
| POST   | `/auth/login`                                                     | Log in with a configured teacher username and password               |
| POST   | `/auth/logout`                                                    | Log out the current teacher                                          |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Register a student (teacher login required)                          |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu` | Unregister a student (teacher login required)                    |

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
