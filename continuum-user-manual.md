# Continuum User Manual for Augment Integration

## 1. System Overview

Continuum is an AI-powered project management web application designed to streamline project tracking, documentation, and collaboration. It integrates advanced AI capabilities to assist with documentation generation, task management, and project insights.

### 1.1 Core Purpose of Continuum

The primary purpose of Continuum is to maintain context and project awareness across different interaction sessions, thread windows, and users. Continuum serves as a central knowledge repository that:

- **Preserves Context Between Sessions**: Ensures AI assistants like Augment can maintain awareness of project status, history, and requirements across multiple conversations
- **Standardizes Communication**: Provides a consistent framework for interacting with AI assistants about project details
- **Centralizes Project Knowledge**: Creates a single source of truth for project information that persists beyond individual chat sessions
- **Facilitates Collaboration**: Allows multiple users to work with AI assistants on the same project with consistent context

By referencing this manual at the beginning of each session, you establish the necessary context for the AI to understand the current state of your project and continue work seamlessly from previous sessions.

## 2. Connection Information

### 2.1 Base URLs

- **API Base URL**: `http://localhost/AgmentW/api/v1`
- **Frontend URL**: `http://localhost:5173`

### 2.2 Authentication Endpoints

- **Login**: `POST /auth/login`
- **Register**: `POST /auth/register`

## 3. Authentication Process

### 3.1 Standard Authentication Format

```json
{
  "username": "admin",
  "password": "Continuum2023!"
}
```

### 3.2 Authentication Response

```json
{
  "message": "Login successful",
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "user": {
    "uid": "USR_ac2c96a98145100c",
    "username": "admin",
    "email": "admin@continuum.dev",
    "role": "admin"
  }
}
```

### 3.3 Using the Authentication Token

All authenticated requests must include the JWT token in the Authorization header:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...
```

## 4. Project Operations

### 4.1 Project Structure

Projects in Continuum have the following structure:
- **Project**: The top-level container (e.g., "Continuum")
- **Epic**: A large body of work that can be broken down into tasks
- **Task**: A specific unit of work
- **Subtask**: A smaller component of a task

### 4.2 Project Identifiers

- **Main Project UID**: `PRJ_579824edc2d7c2d5`
- **Project Name**: Continuum

### 4.3 Common Project Operations

#### 4.3.1 Get All Projects

```
GET /projects
```

#### 4.3.2 Get Project by ID

```
GET /projects/{uid}
```

#### 4.3.3 Create Project

```
POST /projects
```

Payload:
```json
{
  "name": "Project Name",
  "description": "Project Description",
  "status": "active"
}
```

#### 4.3.4 Update Project

```
PUT /projects/{uid}
```

Payload:
```json
{
  "name": "Updated Project Name",
  "description": "Updated Project Description",
  "status": "active"
}
```

## 5. Task Operations

### 5.1 Task Types

- **epic**: Large body of work
- **story**: User story
- **task**: Standard task
- **subtask**: Component of a task
- **bug**: Issue to be fixed

### 5.2 Task Status Options

- **to_do**: Not started
- **in_progress**: Currently being worked on
- **blocked**: Cannot proceed
- **review**: Ready for review
- **done**: Completed

### 5.3 Task Priority Options

- **low**: Low priority
- **medium**: Medium priority
- **high**: High priority
- **critical**: Critical priority

### 5.4 Common Task Operations

#### 5.4.1 Get Project Tasks

```
GET /projects/{uid}/tasks
```

#### 5.4.2 Create Task

```
POST /projects/{uid}/tasks
```

Payload:
```json
{
  "title": "Task Title",
  "description": "Task Description",
  "status": "to_do",
  "priority": "high",
  "task_type": "task",
  "parent_task_id": "1" (optional)
}
```

#### 5.4.3 Update Task

```
PUT /tasks/{uid}
```

Payload:
```json
{
  "title": "Updated Task Title",
  "description": "Updated Task Description",
  "status": "in_progress",
  "priority": "high",
  "task_type": "task",
  "parent_task_id": "1" (optional)
}
```

#### 5.4.4 Delete Task

```
DELETE /tasks/{uid}
```

## 6. Documentation Operations

### 6.1 Document Types

- **requirements**: Requirements documentation
- **design**: Design documentation
- **api**: API documentation
- **user_guide**: User guide
- **code_comments**: Code comments
- **meeting_notes**: Meeting notes
- **other**: Other documentation

### 6.2 Common Documentation Operations

#### 6.2.1 Get Project Documentation

```
GET /projects/{uid}/docs
```

#### 6.2.2 Create Documentation

```
POST /projects/{uid}/docs
```

Payload:
```json
{
  "title": "Document Title",
  "content": "Document Content",
  "doc_type": "design",
  "version": "1.0"
}
```

#### 6.2.3 Update Documentation

```
PUT /docs/{uid}
```

Payload:
```json
{
  "title": "Updated Document Title",
  "content": "Updated Document Content",
  "doc_type": "design",
  "version": "1.1"
}
```

#### 6.2.4 Delete Documentation

```
DELETE /docs/{uid}
```

## 7. Key Project Tasks

### 7.1 Main Epics

1. **Frontend Development** (TSK_3aafaa69ad03ea67) - Status: In Progress
2. **Backend API Development** (TSK_c5a3304a58e66c80) - Status: Done
3. **Authentication System** (TSK_d3af8033771efe92) - Status: Done
4. **Documentation Features** (TSK_89a90a215b037388) - Status: In Progress
5. **AI Integration** (TSK_9d5fc1f3e2f2f57e) - Status: To Do
6. **Enhance Document Formatting Support** (TSK_64d0b1f1261bcef9) - Status: To Do

### 7.2 Document Formatting Enhancement Tasks

1. **Update Database Schema for Rich Text** (TSK_8fac5a8c2622e7bb)
2. **Implement Rich Text Editor** (TSK_f5cee13adaf18922)
3. **Enhance API for Rich Text Handling** (TSK_be38af84ae84cea6)
4. **Implement Markdown Rendering Component** (TSK_555b88735224a0cb)
5. **Add Live Preview Feature** (TSK_b48cb7702bfb1512)

## 8. Best Practices for Augment Integration

### 8.1 Session Initialization

At the beginning of each session, use the phrase:

> "Follow the Continuum User Manual"

Then provide:
1. Project name (if different from "Continuum")
2. Authentication credentials (if different from default admin)

This initialization is crucial as it tells the AI assistant to load the context from Continuum, ensuring continuity between sessions and preventing the need to re-explain project details in each new conversation.

### 8.2 Authentication Process

Always authenticate first before performing any operations:

```
Please authenticate with Continuum using the admin credentials.
```

### 8.3 Task References

When referring to tasks, use either:
- The full task title in quotes: "Implement Rich Text Editor"
- The task UID: TSK_f5cee13adaf18922

### 8.4 Documentation References

When referring to documentation, use either:
- The full document title in quotes: "System Architecture"
- The document UID: DOC_758b51b44efb013e

### 8.5 Command Patterns

Use consistent command patterns:

- **View**: "Show me [resource]"
- **Create**: "Create a new [resource] with [details]"
- **Update**: "Update [resource] to [new state]"
- **Delete**: "Delete [resource]"

### 8.6 Context Maintenance

To maintain context between sessions:

1. Reference the project documentation
2. Mention previous work completed
3. Specify the current focus area

Consistent context maintenance is the core value proposition of Continuum. By following these practices, you ensure that AI assistants can pick up exactly where previous sessions left off, regardless of whether you're working with a new chat thread, a different AI assistant, or collaborating with team members.

## 9. Example Workflows

### 9.1 Starting a New Session

```
Follow the Continuum User Manual.

Please authenticate with Continuum using the admin credentials and show me the current status of the "Enhance Document Formatting Support" epic.
```

### 9.2 Working on a Specific Task

```
Follow the Continuum User Manual.

Please authenticate with Continuum using the admin credentials. I want to work on the "Implement Rich Text Editor" task. First, update its status to "in_progress", then show me the relevant documentation about frontend components.
```

### 9.3 Creating New Documentation

```
Follow the Continuum User Manual.

Please authenticate with Continuum using the admin credentials. Create a new document for the Continuum project with:
- Title: "Rich Text Editor Implementation Guide"
- Content: [Content here]
- Document Type: design
```

## 10. Troubleshooting

### 10.1 Authentication Issues

If authentication fails:
1. Verify the credentials
2. Check the API base URL
3. Ensure the server is running

### 10.2 Resource Not Found

If a resource is not found:
1. Verify the UID
2. Check if the resource exists
3. Ensure you have permission to access it

### 10.3 Invalid Requests

If a request is invalid:
1. Check the request format
2. Verify the required fields
3. Ensure the values are valid

### 10.4 Context Loss Between Sessions

If the AI assistant seems to have lost context between sessions:
1. Ensure you started with "Follow the Continuum User Manual"
2. Verify that authentication was successful
3. Check that you're referencing the correct project and resources
4. Provide the specific UIDs of resources you're working with

## 11. Conclusion

This user manual provides a comprehensive guide for interacting with the Continuum project management system through Augment. By following these guidelines, you can efficiently manage projects, tasks, and documentation while maintaining context between sessions.

Continuum's primary value is in preserving context across different interaction sessions, thread windows, and users. It serves as the central nervous system for your project, ensuring that all AI interactions have access to the same up-to-date information, regardless of when or where those interactions take place.