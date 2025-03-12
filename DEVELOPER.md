# Developer Documentation

This document provides technical details about the Job Portal application for developers who want to understand, maintain, or extend the codebase.

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Technology Stack Details](#technology-stack-details)
- [Frontend Structure](#frontend-structure)
- [Backend Structure](#backend-structure)
- [Key Components](#key-components)
- [Authentication Flow](#authentication-flow)
- [API Documentation](#api-documentation)
- [Database Schema](#database-schema)
- [Deployment](#deployment)
- [Contributing Guidelines](#contributing-guidelines)

## Architecture Overview

The Job Portal follows a classic MERN (MongoDB, Express.js, React.js, Node.js) stack architecture with a clear separation between frontend and backend:

1. **Frontend**: React.js application with Redux for state management
2. **Backend**: Express.js REST API with MongoDB database
3. **Communication**: HTTP requests via Axios from frontend to backend API
4. **Authentication**: JWT (JSON Web Tokens) for secure user authentication

The application employs a layered architecture:
- Presentation Layer (React components)
- State Management Layer (Redux)
- Data Access Layer (API services)
- Business Logic Layer (Express controllers)
- Data Storage Layer (MongoDB models)

## Technology Stack Details

### Frontend
- **React (18.x)**: UI library
- **Redux Toolkit**: State management
- **React Router (6.x)**: Client-side routing
- **Axios**: HTTP client
- **Tailwind CSS**: Utility-first CSS framework
- **React Toastify**: Toast notifications
- **Vite**: Build tool and development server

### Backend
- **Node.js**: JavaScript runtime
- **Express.js**: Web framework
- **MongoDB**: NoSQL database
- **Mongoose**: ODM (Object Document Mapper)
- **JWT**: Authentication mechanism
- **bcrypt.js**: Password hashing
- **Multer**: File upload handling
- **Validator**: Input validation

## Frontend Structure

### Directory Structure

```
client/
├── public/                # Static assets
├── src/
│   ├── components/        # Reusable UI components
│   │   ├── Navbar.jsx     # Application navigation
│   │   ├── JobCard.jsx    # Job listing card
│   │   ├── ProtectedRoute.jsx  # Auth guard for user routes
│   │   └── AdminRoute.jsx      # Auth guard for admin routes
│   ├── layouts/
│   │   └── MainLayout.jsx # Main application layout wrapper
│   ├── pages/
│   │   ├── Home.jsx       # Landing page with job listings
│   │   ├── Login.jsx      # User authentication
│   │   ├── Register.jsx   # User registration
│   │   ├── JobDetails.jsx # Detailed job view with application form
│   │   ├── AdminDashboard.jsx # Admin job management
│   │   ├── CreateJob.jsx  # Job creation form
│   │   └── Applications.jsx # Application tracking
│   ├── redux/
│   │   ├── store.js       # Redux store configuration
│   │   └── slices/
│   │       └── authSlice.js # Authentication state management
│   ├── utils/
│   │   └── api.js         # Axios instance and interceptors
│   ├── App.jsx            # Main component with routes
│   ├── main.jsx           # Entry point
│   └── index.css          # Global styles (Tailwind imports)
└── vite.config.js         # Vite configuration
```

### Key Frontend Concepts

1. **Component Structure**: The application follows a component-based architecture with:
   - Layouts: Wrapper components that provide consistent structure
   - Pages: Full-page components rendered by routes
   - Components: Reusable UI elements

2. **Routing**: Uses React Router v6 with route protection through custom route components:
   - `ProtectedRoute`: Ensures user authentication
   - `AdminRoute`: Ensures admin-level access

3. **State Management**: Uses Redux with Redux Toolkit:
   - Authentication state stored in Redux
   - Local component state for form handling and UI state

4. **API Communication**: Centralized through the `api.js` utility:
   - Axios instance with base URL configuration
   - Request/response interceptors for authentication
   - Error handling for 401 unauthorized responses

## Backend Structure

### Directory Structure

```
server/
├── src/
│   ├── controllers/           # Request handlers
│   │   ├── auth.controller.js # Authentication logic
│   │   ├── job.controller.js  # Job CRUD operations
│   │   └── application.controller.js # Application management
│   ├── middleware/
│   │   ├── auth.js            # JWT authentication middleware
│   │   └── upload.js          # File upload middleware (Multer)
│   ├── models/
│   │   ├── User.js            # User schema and model
│   │   ├── Job.js             # Job schema and model
│   │   └── Application.js     # Application schema and model
│   ├── routes/
│   │   ├── auth.routes.js     # Authentication endpoints
│   │   ├── job.routes.js      # Job management endpoints
│   │   └── application.routes.js # Application endpoints
│   └── index.js               # Entry point, Express configuration
├── uploads/                   # File upload storage
└── .env                       # Environment variables
```

### Key Backend Concepts

1. **RESTful API Design**: The API follows RESTful principles with resource-based URLs and appropriate HTTP methods:
   - GET: Retrieve resources
   - POST: Create resources
   - PATCH: Update resources
   - DELETE: Remove resources

2. **Middleware Stack**:
   - CORS handling
   - Body parsing
   - Authentication (JWT)
   - Role-based access control
   - File upload processing (Multer)

3. **MongoDB/Mongoose**: 
   - Schemas define the structure of documents
   - Middleware (pre/post hooks) for password hashing
   - Custom methods for model-specific logic
   - Indexes for query optimization

4. **Authentication Flow**:
   - JWT tokens for stateless authentication
   - Token verification middleware
   - Role-based access control

## Key Components

### Authentication Components
- **Login/Register**: Handle user registration and authentication
- **ProtectedRoute**: Route wrapper that redirects unauthenticated users
- **AdminRoute**: Route wrapper that restricts access to admin users only

### Job Components
- **JobCard**: Displays job summary information
- **JobDetails**: Shows complete job information with application functionality
- **CreateJob**: Form for admins to create new job listings

### Application Components
- **Applications**: Displays user's applications or all applications for admins
- **ApplicationForm**: Form for submitting job applications with resume upload

## Authentication Flow

1. **Registration**:
   - User submits registration form with name, email, and password
   - Server validates input
   - Password is hashed using bcrypt
   - User document is created in the database
   - JWT token is generated and returned to client
   - Client stores token in localStorage and Redux state

2. **Login**:
   - User submits email and password
   - Server validates credentials
   - JWT token is generated and returned to client
   - Client stores token in localStorage and Redux state

3. **Authentication**:
   - Token is sent with each request in the Authorization header
   - Server middleware verifies token validity
   - User information is attached to the request object
   - Role-based middleware controls access to protected routes

4. **Logout**:
   - Client removes token from localStorage and Redux state

## API Documentation

### Authentication

#### Register User
- **URL**: `/api/auth/register`
- **Method**: `POST`
- **Body**:
  ```json
  {
    "name": "User Name",
    "email": "user@example.com",
    "password": "password123"
  }
  ```
- **Response**:
  ```json
  {
    "status": "success",
    "token": "jwt-token",
    "data": {
      "user": {
        "_id": "user-id",
        "name": "User Name",
        "email": "user@example.com",
        "role": "user"
      }
    }
  }
  ```

#### Login User
- **URL**: `/api/auth/login`
- **Method**: `POST`
- **Body**:
  ```json
  {
    "email": "user@example.com",
    "password": "password123"
  }
  ```
- **Response**: Same as register response

### Jobs

#### Get All Jobs
- **URL**: `/api/jobs`
- **Method**: `GET`
- **Query Parameters**:
  - `category`: Filter by job category
  - `page`: Pagination page number
  - `limit`: Results per page
- **Response**:
  ```json
  {
    "status": "success",
    "results": 10,
    "total": 24,
    "data": {
      "jobs": [
        {
          "_id": "job-id",
          "title": "Job Title",
          "company": "Company Name",
          "category": "MERN",
          "description": "Job description",
          "experience": "2-3 years",
          "location": "Remote",
          "salary": "$60,000 - $80,000",
          "createdBy": {
            "_id": "user-id",
            "name": "Admin Name"
          }
        }
      ]
    }
  }
  ```

#### Get Single Job
- **URL**: `/api/jobs/:id`
- **Method**: `GET`
- **Response**:
  ```json
  {
    "status": "success",
    "data": {
      "job": {
        "_id": "job-id",
        "title": "Job Title",
        "company": "Company Name",
        "category": "MERN",
        "description": "Job description",
        "experience": "2-3 years",
        "location": "Remote",
        "salary": "$60,000 - $80,000",
        "createdBy": {
          "_id": "user-id",
          "name": "Admin Name"
        }
      }
    }
  }
  ```

#### Create Job
- **URL**: `/api/jobs`
- **Method**: `POST`
- **Authentication**: Required (Admin only)
- **Body**:
  ```json
  {
    "title": "Job Title",
    "company": "Company Name",
    "category": "MERN",
    "description": "Job description",
    "experience": "2-3 years",
    "location": "Remote",
    "salary": "$60,000 - $80,000"
  }
  ```
- **Response**:
  ```json
  {
    "status": "success",
    "data": {
      "job": {
        "_id": "job-id",
        "title": "Job Title",
        "company": "Company Name",
        "category": "MERN",
        "description": "Job description",
        "experience": "2-3 years",
        "location": "Remote",
        "salary": "$60,000 - $80,000",
        "createdBy": "user-id"
      }
    }
  }
  ```

### Applications

#### Submit Application
- **URL**: `/api/applications/:jobId`
- **Method**: `POST`
- **Authentication**: Required
- **Content-Type**: `multipart/form-data`
- **Body**:
  - `description`: Text description
  - `resume`: File upload (PDF, DOC, DOCX)
- **Response**:
  ```json
  {
    "status": "success",
    "data": {
      "application": {
        "_id": "application-id",
        "job": "job-id",
        "applicant": "user-id",
        "description": "Application description",
        "resume": "filename.pdf",
        "status": "pending"
      }
    }
  }
  ```

## Database Schema

### User Model
```javascript
{
  name: String,          // User's full name
  email: String,         // Unique email address
  password: String,      // Hashed password
  role: String,          // "user" or "admin"
  createdAt: Date        // Account creation timestamp
}
```

### Job Model
```javascript
{
  title: String,         // Job title
  category: String,      // Job category
  description: String,   // Full job description
  experience: String,    // Required experience level
  company: String,       // Company name
  location: String,      // Job location
  salary: String,        // Salary range
  isActive: Boolean,     // Whether job is active
  createdBy: ObjectId,   // Reference to User model
  createdAt: Date        // Job posting timestamp
}
```

### Application Model
```javascript
{
  job: ObjectId,         // Reference to Job model
  applicant: ObjectId,   // Reference to User model
  description: String,   // Applicant's description
  resume: String,        // Resume filename
  status: String,        // "pending", "reviewed", "accepted", or "rejected"
  createdAt: Date        // Application submission timestamp
}
```

## Deployment

### Production Build

#### Frontend
1. Navigate to the client directory
2. Run `npm run build` to create a production build
3. The build output will be in the `dist` directory

#### Backend
1. Ensure all production environment variables are set
2. Start the server with `npm start`

### Environment Variables

#### Backend (.env)
```
PORT=5000
MONGODB_URI=mongodb+srv://...
JWT_SECRET=your-jwt-secret
JWT_EXPIRES_IN=7d
UPLOAD_PATH=uploads
```

#### Frontend (.env)
```
VITE_API_URL=http://localhost:5000/api
```

## Contributing Guidelines

### Code Style
- Follow the existing code style
- Use meaningful variable and function names
- Add comments for complex logic
- Keep components focused on a single responsibility

### Git Workflow
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Testing
- Write tests for new features
- Ensure all tests pass before submitting PR
- Test across different browsers and screen sizes 