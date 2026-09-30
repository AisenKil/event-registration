# School Event Registration

A school event registration system built with React, TypeScript, Express, MySQL, and Jenkins CI.

## Technology Stack

* Frontend: React + Vite + TypeScript
* Backend: Node.js + Express
* Database: MySQL
* Web Server / Load Balancer: nginx
* CI: Jenkins
* Source Control: Git + GitHub

## Project Structure

```text
event-registration/
├── backend/
├── frontend/
├── nginx/
├── Jenkinsfile
├── .gitignore
└── README.md
```

## Run Locally

### 1. Start MySQL

Make sure MySQL is running through XAMPP.

Create the database:

```sql
CREATE DATABASE event_registration;
```

The backend will initialize the required tables and sample data when it starts.

### 2. Start the Backend

Open PowerShell:

```powershell
cd backend
npm install
npm start
```

The backend runs on:

```text
http://localhost:3000
```

Backend environment variables are configured using `.env.example`.

### 3. Start the Frontend

Open another PowerShell window:

```powershell
cd frontend
npm install
npm run dev
```

The frontend development server will provide the application URL shown by Vite.

## Default Admin Account

The default administrator is configured through the backend environment variables.

```text
Email: admin@school.edu
Password: admin123
```

For production use, change these credentials and the JWT secret.

## Jenkins CI Pipeline

This project uses Jenkins to automatically verify changes pushed to the GitHub repository.

GitHub repository:

```text
https://github.com/AisenKil/event-registration.git
```

The Jenkins pipeline is defined in:

```text
Jenkinsfile
```

### Pipeline Process

When Jenkins runs the pipeline, it performs the following steps:

```text
GitHub Repository
        ↓
Checkout
        ↓
Install Backend Dependencies
        ↓
Install Frontend Dependencies
        ↓
Prepare MySQL Database
        ↓
Run Backend Tests
        ↓
Build Frontend
        ↓
Pipeline SUCCESS
```

### Jenkins Stages

#### 1. Checkout

Jenkins retrieves the latest code from the `main` branch of the GitHub repository.

#### 2. Install Backend

Jenkins enters the `backend` directory and runs:

```powershell
npm ci
```

This installs the exact dependencies defined by the backend lockfile.

#### 3. Install Frontend

Jenkins enters the `frontend` directory and runs:

```powershell
npm ci
```

This installs the frontend dependencies.

#### 4. Prepare Database

Jenkins checks that the MySQL database exists:

```text
event_registration
```

The current Jenkins configuration uses the XAMPP MySQL executable:

```text
C:\xampp\mysql\bin\mysql.exe
```

#### 5. Backend Test

Jenkins runs:

```powershell
npm test
```

The backend uses Node.js' built-in test runner.

The current tests verify:

* `/api/health` returns a successful response.
* Protected API routes reject unauthenticated requests.

#### 6. Frontend Build

Jenkins runs:

```powershell
npm run build
```

This type-checks the TypeScript code and creates the production frontend files in:

```text
frontend/dist
```

### Automatic Jenkins Builds

The Jenkinsfile contains an SCM polling trigger:

```groovy
triggers {
    pollSCM('H/5 * * * *')
}
```

This allows Jenkins to check the GitHub repository approximately every five minutes for changes.

When a new commit is detected on `main`, Jenkins can automatically start another build.

## Jenkins Build Result

A successful pipeline ends with:

```text
Finished: SUCCESS
```

A failed stage causes the Jenkins build to fail and the Console Output can be used to identify the problem.

## CI/CD Notes

The current Jenkins configuration provides continuous integration:

* Retrieves the latest GitHub code.
* Installs project dependencies.
* Prepares the MySQL database.
* Runs backend tests.
* Builds the frontend.
* Reports whether the build succeeded or failed.

The current pipeline does not automatically deploy the application to a production server. Deployment can be added later if required.

## Nginx

The `nginx/nginx.conf` configuration is intended to:

* Serve the built frontend.
* Proxy application requests.
* Load-balance requests between two backend containers.

The backend container names `backend1` and `backend2` should be changed if different container names are used.

## Application Rules

The system implements the following rules:

* One registration per user per event.
* Registrations exceeding the maximum participant limit are automatically rejected.
* Users receive notifications when their registration is approved or rejected.
* Administrators receive notifications when an event reaches its maximum participant capacity.

## Development Workflow

The recommended workflow is:

```text
Make Code Changes
        ↓
Test Locally
        ↓
git add
        ↓
git commit
        ↓
git push origin main
        ↓
Jenkins Detects Changes
        ↓
Jenkins Runs Pipeline
        ↓
Tests + Frontend Build
        ↓
SUCCESS / FAILURE
```

## Current Jenkins Verification

The Jenkins pipeline has been successfully tested.

The verified pipeline stages are:

```text
✓ GitHub Checkout
✓ Backend npm ci
✓ Frontend npm ci
✓ MySQL Database Preparation
✓ Backend Tests
✓ Frontend Production Build
✓ Jenkins Post Actions
```

The successful Jenkins build ends with:

```text
Finished: SUCCESS
```

