---
title: "Understanding TypeScript - Part 9: Sample Project"
summary: In this article, we will explore the process of developing a Todo application using TypeScript and Node.js with JWT and Google OAuth authentication. We will discuss the experience of designing a modern REST API by implementing TypeScript's type safety, MongoDB integration, and clean architecture principles.
date: 2025-02-25T09:00:00+03:00
cardimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum9/images/cover.svg'
featureimage: '/tr/blog/typescript-serisi/typescripti-anlamak-bolum9/images/cover.svg'
featured: true
categories: ['TypeScript', 'Node.js', 'Backend']
tags: ['typescript', 'nodejs', 'microservices']
translationKey: 'typescript-file-handler-service'
authors:
  - Çağatay: /images/author.jpeg
---

# Developing a Node.js Authentication API with TypeScript: Comprehensive Guide

## Introduction

Type safety and code quality are becoming increasingly important when developing modern web applications. In this tutorial, we will learn TypeScript's powerful features through a real project. While developing a Todo application with JWT and Google OAuth authentication, we will implement TypeScript's core concepts and best practices.

This project will provide you with:

- Practice in writing secure code with TypeScript
- Experience in designing a modern REST API
- Authentication and Authorization implementation
- Using TypeScript with MongoDB
- Applying Clean Architecture principles

## TypeScript Features and Project Structure

We'll develop a secure and scalable API using TypeScript's core features. Let's explain each feature with examples from our actual project code:

### 1. TypeScript Basics (Basics)

TypeScript's basic building blocks are used in our project like this:

```typescript
// src/config/env.ts'de Tip Tanımlamaları
const PORT: number = Number(process.env.PORT) || 3000;
const JWT_EXPIRES_IN: string = '1d';

// src/middleware/auth.middleware.ts'de Type Assertion
const decoded = jwt.verify(token, JWT_SECRET) as IJwtPayload;
// Burada JWT'den gelen veriyi IJwtPayload tipine dönüştürüyoruz

// src/interfaces/user.interface.ts'de Literal Types
type UserRole = 'user' | 'admin';
// User modelinde kullanıcı rollerini sadece bu iki değerle sınırlıyoruz
```

**Projedeki Kullanım Örnekleri:**

- `PORT` tanımı `src/index.ts`'de server başlatırken kullanılıyor
- Type assertion `auth.middleware.ts`'de JWT doğrulamasında kullanılıyor
- UserRole tipi `IUser` interface'inde kullanıcı rolünü kısıtlamak için kullanılıyor

### 2. Functions

TypeScript'te functions are typed like this:

```typescript
// src/services/auth.service.ts'de Method Signatures
interface IAuthService {
  login(credentials: IUserLogin): Promise<{ user: IUser; token: string }>;
  register(userData: IUserRegistration): Promise<IUser>;
}

// src/controllers/auth.controller.ts'de Implementation
public async login(req: Request, res: Response): Promise<void> {
  const { email, password } = req.body;
  const result = await this.authService.login({ email, password });
  // ...
}
```

**Projedeki Kullanım Örnekleri:**

- `IAuthService` interface'i `auth.service.ts`'de servis implementasyonunu tanımlıyor
- Controller'lardaki tüm handler functions use Request and Response types
- All async functions are typed with Promise return type

### 3. Object Types

We model complex data structures with object types in our project:

```typescript
// src/config/database.ts'de Configuration Types
type DatabaseConfig = {
  uri: string;
  options: {
    useNewUrlParser: boolean;
    useUnifiedTopology: boolean;
  };
};

// src/controllers/todo.controller.ts'de Request Types
interface ITodoCreate {
  title: string;
  description?: string; // Optional property example
}
```

**Projedeki Kullanım Örnekleri:**

- `DatabaseConfig` type defines MongoDB connection settings
- `ITodoCreate` interface is used for request body validation in todo creation endpoint
- Optional properties allow partial updates in todo updates

### 4. Interfaces

Interfaces are used in our project both for type definition and for contracts:

```typescript
// src/interfaces/base.interface.ts'de Base Interface
interface IBaseEntity {
  _id: string;
  createdAt: Date;
  updatedAt: Date;
}

// src/interfaces/user.interface.ts'de Interface Extension
interface IUser extends IBaseEntity {
  email: string;
  password?: string;
  name: string;
  role: UserRole;
}
```

**Projedeki Kullanım Örnekleri:**

- `IBaseEntity` defines common fields for all MongoDB models
- `IUser` interface defines User model schema and methods
- Interfaces ensure type safety in mongoose model definitions

### 5. TypeScript Compiler

We configure TypeScript compiler specifically for our project:

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

**Compiler Settings's Importance:**

- `strict`: Enables strict type checking
- `target`: Allows us to use modern JavaScript features
- `module`: Uses Node.js compatible module system

### 6. Classes

We implement OOP principles with TypeScript classes:

```typescript
// src/services/base.service.ts'de Abstract Base Class
abstract class BaseService<T extends IBaseEntity> {
  constructor(protected model: Model<T>) {}

  abstract create(data: Partial<T>): Promise<T>;

  async findById(id: string): Promise<T | null> {
    return this.model.findById(id);
  }
}

// src/services/todo.service.ts'de Class Implementation
class TodoService extends BaseService<ITodo> {
  async create(data: ICreateTodo): Promise<ITodo> {
    return this.model.create(data);
  }

  async markAsCompleted(id: string): Promise<ITodo | null> {
    return this.model.findByIdAndUpdate(id, { completed: true });
  }
}
```

**Why This Feature?**

- Abstract classes enforce common behaviors
- Inheritance reduces code repetition
- Organizes service layer

**Projedeki Kullanım Örnekleri:**

- `BaseService` defines basic CRUD operations for all services
- `TodoService` and `AuthService` extend this base class to add their own specific methods
- Abstract methods ensure each service must implement its own create method

### 7. Generics

We use generics in our project like this:

```typescript
// src/services/base.service.ts'de Generic Service
class CrudService<T extends IBaseEntity> {
  async findOne(filter: FilterQuery<T>): Promise<T | null> {
    return this.model.findOne(filter);
  }
}

// src/utils/response.ts'de Generic Response Handler
function createResponse<T>(success: boolean, message: string, data?: T): IApiResponse<T> {
  return { success, message, data };
}

// src/utils/error.ts'de Generic Error Handler
class ApiError<T = unknown> extends Error {
  constructor(public statusCode: number, message: string, public data?: T) {
    super(message);
  }
}
```

**Why This Feature?**

- Keeps type safety while writing reusable code
- Creates functions that work with different data types
- Creates flexible structures with type parameters

**Projedeki Kullanım Örnekleri:**

- `CrudService` works with different model types (User, Todo, etc.)
- `createResponse` creates consistent API responses for all data structures
- `ApiError` provides customizable error handling for different error types

### 8. Type Narrowing

We safely perform runtime type checking and narrowing in TypeScript:

```typescript
// src/utils/error.ts'de Type Guards
function isError(error: unknown): error is Error {
  return error instanceof Error;
}

// src/middleware/error.middleware.ts'de Error Handling
function handleError(error: unknown): IApiResponse<null> {
  if (isError(error)) {
    return createResponse(false, error.message);
  }

  if (typeof error === 'string') {
    return createResponse(false, error);
  }

  return createResponse(false, 'Unknown error occurred');
}

// src/types/error.types.ts'de Discriminated Unions
type ValidationError = {
  type: 'validation';
  fields: { [key: string]: string };
};

type AuthError = {
  type: 'auth';
  message: string;
};

type AppError = ValidationError | AuthError;

// src/utils/error-handler.ts'de Error Type Handling
function handleAppError(error: AppError) {
  switch (error.type) {
    case 'validation':
      return error.fields;
    case 'auth':
      return error.message;
  }
}
```

**Why This Feature?**

- Keeps runtime type safety
- Improves error handling
- Works with Union types correctly

**Projedeki Kullanım Örnekleri:**

- `isError` type guard in middleware detects error type correctly
- Error handling middleware distinguishes different error types
- Discriminated unions allow handling validation and auth errors separately

## Project Summary

Our API will include the following features:

1. **User Management**

   - Registration and Login
   - JWT Authentication
   - Google OAuth Integration
   - Role-based authorization

2. **Todo Operations**

   - Create, read, update, delete todos
   - User-specific todos
   - Todo status changes

3. **Security and Validation**
   - Input validation
   - Route protection
   - Error handling

## Project Structure

Our project is organized as follows:

```
src/
├── config/         # Configuration files
├── controllers/    # HTTP request handlers
├── interfaces/     # TypeScript interfaces
├── middleware/     # Express middleware
├── models/        # Mongoose models
├── routes/        # API routes
├── services/      # Business logic
├── utils/         # Helper functions
└── index.ts       # Application entry point
```

## Developing the Project with TypeScript

### 1. Project Setup and TypeScript Configuration

First step is to integrate TypeScript into our project:

```bash
mkdir nodejs-typescript-auth
cd nodejs-typescript-auth
npm init -y
npm install typescript ts-node @types/node --save-dev
```

#### Dependencies

Let's install necessary packages for our project:

```bash
# Main dependencies
npm install express mongoose dotenv jsonwebtoken bcrypt passport passport-google-oauth20 passport-jwt cors

# Type definitions
npm install @types/express @types/mongoose @types/jsonwebtoken @types/bcrypt @types/passport @types/passport-google-oauth20 @types/passport-jwt @types/cors --save-dev
```

#### TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

### 2. Defining Data Models

#### User Model

Let's define our User model with TypeScript interfaces:

```typescript
// src/interfaces/user.interface.ts

// Base interface - basic user properties
interface IBaseUser {
  email: string;
  name: string;
}

// Main user interface - includes all properties
interface IUser extends IBaseUser {
  password?: string; // Optional: Google OAuth users may not have a password
  googleId?: string; // Optional: Only for users who sign in with Google
  role: 'user' | 'admin'; // Union type to limit roles
  comparePassword(candidatePassword: string): Promise<boolean>;
}

// Required fields for registration
interface IUserRegistration {
  email: string;
  password: string;
  name: string;
}

// Required fields for login
interface IUserLogin {
  email: string;
  password: string;
}
```

#### Todo Model

Let's define the interfaces needed for todo operations:

```typescript
// src/interfaces/todo.interface.ts

interface ITodo {
  title: string;
  description?: string;
  completed: boolean;
  user: string; // Reference: User ID
  createdAt: Date;
  updatedAt: Date;
}

// Required fields for todo creation
interface ICreateTodo {
  title: string;
  description?: string;
}

// Optional fields for todo updates
interface IUpdateTodo {
  title?: string;
  description?: string;
  completed?: boolean;
}
```

### 3. Service Layer Implementation

#### Base Service

Let's create a generic base service to reduce code repetition:

```typescript
// src/services/base.service.ts

abstract class BaseService<T> {
  constructor(protected model: Model<T>) {}

  async findById(id: string): Promise<T | null> {
    return this.model.findById(id);
  }

  async findOne(filter: FilterQuery<T>): Promise<T | null> {
    return this.model.findOne(filter);
  }

  async find(filter: FilterQuery<T>): Promise<T[]> {
    return this.model.find(filter);
  }
}
```

#### Auth Service

Service to handle authentication operations:

```typescript
// src/services/auth.service.ts

class AuthService extends BaseService<IUser> {
  public async register(userData: IUserRegistration): Promise<IUser> {
    const existingUser = await this.findOne({ email: userData.email });
    if (existingUser) {
      throw new Error('This email is already in use');
    }

    const user = await this.model.create(userData);
    return user;
  }

  public async login(loginData: IUserLogin): Promise<{ user: IUser; token: string }> {
    const user = await this.findOne({ email: loginData.email });
    if (!user || !(await user.comparePassword(loginData.password))) {
      throw new Error('Invalid credentials');
    }

    return {
      user,
      token: this.generateToken(user),
    };
  }

  private generateToken(user: IUser): string {
    return jwt.sign({ id: user._id, email: user.email, role: user.role }, process.env.JWT_SECRET!, { expiresIn: '1d' });
  }
}
```

#### Todo Service

Service to handle todo operations:

```typescript
// src/services/todo.service.ts

class TodoService extends BaseService<ITodo> {
  public async getAllTodos(userId: string): Promise<ITodo[]> {
    return this.find({ user: userId });
  }

  public async createTodo(todoData: ICreateTodo, userId: string): Promise<ITodo> {
    return this.model.create({
      ...todoData,
      user: userId,
      completed: false,
    });
  }

  public async updateTodo(todoId: string, todoData: Partial<ITodo>, userId: string): Promise<ITodo | null> {
    return this.model.findOneAndUpdate({ _id: todoId, user: userId }, todoData, { new: true });
  }
}
```

### 4. Middleware Implementation

TypeScript's safe middleware writing:

```typescript
// src/middleware/auth.middleware.ts

// Request type extension
declare global {
  namespace Express {
    interface Request {
      user?: IUser;
    }
  }
}

export const isAuthenticated = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) {
      throw new Error('Token not found');
    }

    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as IJwtPayload;
    const user = await UserModel.findById(decoded.id);

    if (!user) {
      throw new Error('User not found');
    }

    req.user = user;
    next();
  } catch (error) {
    res.status(401).json({
      success: false,
      message: 'Authorization error',
      error: error instanceof Error ? error.message : 'Unknown error',
    });
  }
};
```

### 5. Controller Layer

TypeScript's safe controller:

```typescript
// src/controllers/todo.controller.ts

class TodoController {
  constructor(private todoService: TodoService) {}

  public async getAllTodos(req: Request, res: Response): Promise<void> {
    try {
      const todos = await this.todoService.getAllTodos(req.user!._id);

      res.status(200).json({
        success: true,
        message: 'Todos fetched successfully',
        data: todos,
      });
    } catch (error) {
      res.status(500).json({
        success: false,
        message: 'Error fetching todos',
        error: error instanceof Error ? error.message : 'Unknown error',
      });
    }
  }
}
```

## API Endpoints

### Auth Endpoints

```typescript
POST /api/auth/register
- Request Body: { email: string, password: string, name: string }
- Response: { success: boolean, message: string, data: { user: IUser, token: string } }

POST /api/auth/login
- Request Body: { email: string, password: string }
- Response: { success: boolean, message: string, data: { user: IUser, token: string } }

GET /api/auth/google
- Google OAuth initiation endpoint

GET /api/auth/google/callback
- Google OAuth callback endpoint
```

### Todo Endpoints

```typescript
GET /api/todos
- Headers: { Authorization: "Bearer ${token}" }
- Response: { success: boolean, message: string, data: ITodo[] }

POST /api/todos
- Headers: { Authorization: "Bearer ${token}" }
- Request Body: { title: string, description?: string }
- Response: { success: boolean, message: string, data: ITodo }

PUT /api/todos/:id
- Headers: { Authorization: "Bearer ${token}" }
- Request Body: { title?: string, description?: string, completed?: boolean }
- Response: { success: boolean, message: string, data: ITodo }

DELETE /api/todos/:id
- Headers: { Authorization: "Bearer ${token}" }
- Response: { success: boolean, message: string }
```

## Best Practices

1. **Type Safety**

   - Always use specific types
   - Avoid the `any` type
   - Limit value sets with Union types
   - Write reusable code with Generic types

2. **Code Organization**

   - Use separate folders for each layer
   - Keep interfaces in relevant domain folders
   - Generalize service layer with abstract classes

3. **Error Handling**

   - Create custom error classes
   - Use global error handler
   - Standardize error messages

4. **Security**
   - Store sensitive information in environment variables
   - Implement input validation
   - Apply rate limiting
   - Configure CORS policies correctly

## Conclusion

In this project, we learned:

- Writing secure code with TypeScript's type system
- Implementing OOP principles with TypeScript
- Designing a modern API architecture
- Implementing authentication and authorization

TypeScript provided our project with important advantages such as:

- Compile-time error detection
- Better IDE support
- Self-documenting code
- Maintainability
