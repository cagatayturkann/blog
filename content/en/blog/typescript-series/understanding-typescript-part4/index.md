---
title: 'Understanding TypeScript - Part 4: Interfaces'
summary: In this article, we will examine interfaces, one of the important features of TypeScript, in detail. We will understand their differences from Type Aliases and learn practical use cases.
date: 2025-02-12T16:51:00+03:00
cardimage: '/blog/en/typescript-series/understanding-typescript-part4/images/cover.svg'
featureimage: '/blog/en/typescript-series/understanding-typescript-part4/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'interfaces', 'type-system']
translationKey: 'typescript-interfaces'
authors:
  - Çağatay: /images/author.jpeg
---

Hello! In this part of our TypeScript series, we will examine interfaces in detail, one of the most powerful features of the type system. We will learn what interfaces are, why we should use them, and how they provide benefits in real-world applications.

## What is an Interface and Why Should We Use It?

Interfaces are a feature in TypeScript that allows us to define the structure of objects. To explain it in simple terms:

"Think of it like drawing a construction plan. In this plan, you have details like how many floors the building will have, room dimensions, window locations, etc. An interface is just like that - it draws a 'plan' for an object. We determine in advance what properties it will have and what types these properties will be."

Here's a simple example:

```typescript
// Simple interface definition
interface User {
  // Required properties
  id: number; // Unique number of the user
  name: string; // Name of the user
  email: string; // Email address

  // Optional properties (marked with ?)
  phone?: string; // Phone number (optional)
  age?: number; // Age information (optional)
}

// Using the interface
const newUser: User = {
  id: 1,
  name: 'John Smith',
  email: 'john@example.com',
  // phone and age are optional, so we don't have to write them
};

// TypeScript protects us here
const invalidUser: User = {
  id: '1', // Error! string value cannot be assigned to number type
  name: 'Michael',
  email: true, // Error! boolean value cannot be assigned to string type
};
```

### Advantages of Using Interfaces

1. **Code Safety:**

```typescript
// Function defined with interface
function updateUser(id: number, user: User) {
  // Thanks to TypeScript, we're sure about the structure of the user object
  console.log(`Updating ${user.name}...`);
  // ... update operations
}

// This works
updateUser(1, {
  id: 1,
  name: 'John',
  email: 'john@example.com',
});

// This gives an error - missing and incorrect properties
updateUser(1, {
  id: 1,
  firstName: 'John', // Error! should be 'name' instead of 'firstName'
});
```

2. **Code Completion:**
   Interfaces enhance your IDE's code completion feature. When accessing an object's properties, the IDE shows you all available properties.

3. **Documentation:**
   Interfaces also serve as documentation. Other developers reading your code can quickly understand the structure of an object.

## Interface vs Type: Understanding the Differences

Let's examine the differences between Interface and Type with real examples:

### 1. Declaration Merging

```typescript
// Declaration merging is possible with interfaces
interface Car {
  brand: string;
}

interface Car {
  // We can add new properties with the same name
  model: string;
}

const tesla: Car = {
  brand: 'Tesla', // Properties from both
  model: 'Model 3', // interfaces are required
};

// This is not possible with Type
type Bicycle = {
  brand: string;
};

// Error! Identifier 'Bicycle' has already been declared
type Bicycle = {
  model: string;
};
```

This feature is especially useful when developing libraries. Users can add new properties to existing interfaces.

### 2. Extends and Implements

Interfaces are very useful in object-oriented programming:

```typescript
// Base interface
interface Animal {
  name: string;
  species: string;
  makeSound(): void;
}

// Deriving from interface
interface Cat extends Animal {
  hasPaws: boolean;
  canClimb: boolean;
}

// Usage in classes
class TabbyCat implements Cat {
  // We must implement all properties and methods
  name: string;
  species: string = 'Cat';
  hasPaws: boolean = true;
  canClimb: boolean = true;

  constructor(name: string) {
    this.name = name;
  }

  makeSound() {
    console.log('Meow!');
  }
}
```

## Interface Methods and Properties

Interfaces can define not only data structure but also object behaviors. Let's proceed with a real example:

```typescript
// Interface for product management in an e-commerce system
interface ProductManagement {
  // Basic CRUD operations
  addProduct(product: Product): Promise<boolean>;
  updateProduct(id: string, product: Product): Promise<boolean>;
  deleteProduct(id: string): Promise<boolean>;
  getProduct(id: string): Promise<Product | null>;

  // Stock management methods
  updateStock(productId: string, quantity: number): Promise<void>;
  checkStock(productId: string): Promise<number>;

  // Statistics methods
  generateSalesReport(start: Date, end: Date): Promise<SalesReport>;
}

// A class using this interface
class PostgreSQLProductManagement implements ProductManagement {
  constructor(private db: Database) {}

  async addProduct(product: Product): Promise<boolean> {
    try {
      await this.db.query('INSERT INTO products (id, name, price, stock) VALUES ($1, $2, $3, $4)', [
        product.id,
        product.name,
        product.price,
        product.stock,
      ]);
      return true;
    } catch (error) {
      console.error('Error adding product:', error);
      return false;
    }
  }

  // Implementation of other methods...
}
```

### Readonly and Optional Properties

We can make some properties in interfaces readonly or optional. Here's a real scenario:

```typescript
// Interface for user profile
interface UserProfile {
  // Readonly properties (cannot be changed)
  readonly id: string; // User ID never changes
  readonly registrationDate: Date; // Registration date cannot be changed

  // Required properties
  email: string; // Email address
  name: string; // Username

  // Optional properties
  phone?: string; // Phone number
  address?: {
    // Address information
    city: string;
    district: string;
    postalCode?: string;
  };
  profilePicture?: string; // Profile picture URL

  // Social media information (all optional)
  socialMedia?: {
    twitter?: string;
    linkedin?: string;
    github?: string;
  };
}

// Usage example
const newProfile: UserProfile = {
  id: 'usr_123', // readonly, can only be assigned during initialization
  registrationDate: new Date(), // readonly, can only be assigned during initialization
  email: 'john@example.com',
  name: 'John Smith',
  // We don't have to add optional fields
};

// INCORRECT USAGE - cannot modify readonly properties
newProfile.id = 'usr_456'; // Error! readonly property cannot be modified
newProfile.registrationDate = new Date(); // Error! readonly property cannot be modified

// We can add optional properties later
newProfile.phone = '555-0123';
newProfile.address = {
  city: 'New York',
  district: 'Manhattan',
};
```

## Generic Interfaces

Generics make interfaces more flexible and reusable. For example, a generic interface for API responses:

```typescript
// Generic API response interface
interface APIResponse<T> {
  success: boolean; // Is the operation successful?
  data: T; // Generic data type
  timestamp: number; // Operation time
  statusCode: number; // HTTP status code
  message?: string; // Optional message
  errors?: string[]; // Error messages if any
}

// Usage with different data types
interface User {
  id: number;
  name: string;
  email: string;
}

interface Product {
  id: number;
  name: string;
  price: number;
}

// API response for user list
const usersResponse: APIResponse<User[]> = {
  success: true,
  data: [
    { id: 1, name: 'John', email: 'john@example.com' },
    { id: 2, name: 'Michael', email: 'michael@example.com' },
  ],
  timestamp: Date.now(),
  statusCode: 200,
};

// API response for a single product
const productResponse: APIResponse<Product> = {
  success: false,
  data: { id: 0, name: '', price: 0 }, // Empty product
  timestamp: Date.now(),
  statusCode: 404,
  message: 'Product not found',
  errors: ['Product with specified ID does not exist'],
};
```

## Interface Inheritance

Interfaces can inherit from each other. This feature prevents code repetition and helps us create a modular structure:

```typescript
// Basic entity properties
interface Entity {
  id: string; // Unique identifier
  createdAt: Date; // Creation date
  updatedAt: Date; // Last update date
  deleted: boolean; // Deletion status
}

// Basic person information
interface Person extends Entity {
  firstName: string;
  lastName: string;
  email: string;
  phone?: string;
}

// Student information
interface Student extends Person {
  studentId: string;
  department: string;
  year: number;
  courses: string[];
  gpa?: number;
}

// Teacher information
interface Teacher extends Person {
  employeeId: string;
  subject: string;
  coursesTaught: string[];
  salary: number;
}

// Usage example
const newStudent: Student = {
  // Properties from Entity
  id: 'std_123',
  createdAt: new Date(),
  updatedAt: new Date(),
  deleted: false,

  // Properties from Person
  firstName: 'John',
  lastName: 'Smith',
  email: 'john@school.edu',

  // Student-specific properties
  studentId: '2024001',
  department: 'Computer Engineering',
  year: 2,
  courses: ['Algorithms', 'Data Structures', 'TypeScript 101'],
};
```

## Common Use Cases for Interfaces

Interfaces appear in many areas of software development. Here are the most common use cases:

### 1. API Communication

We use interfaces to define incoming and outgoing data structures when working with APIs:

```typescript
// Structure of user data coming from API
interface UserResponse {
  id: number;
  name: string;
  email: string;
  role: 'admin' | 'user';
  lastLogin?: Date;
}

// Login information to be sent to API
interface LoginCredentials {
  email: string;
  password: string;
  remember?: boolean;
}
```

### 2. Database Operations

We use interfaces to model table structures and query results in database operations:

```typescript
interface Product {
  id: number; // Product unique number
  name: string; // Product name
  price: number; // Product price
  stock: number; // Stock quantity
  category: string; // Product category
}
```

### 3. Form Management

Interfaces help us when defining form data structure and validation rules:

```typescript
interface RegistrationForm {
  email: string; // User email
  password: string; // Password
  passwordConfirm: string; // Password confirmation
  username: string; // Username
}
```

### 4. Configuration Management

We use interfaces when defining application settings and configurations:

```typescript
interface AppSettings {
  apiUrl: string; // API server address
  maxAttempts: number; // Maximum number of attempts
  timeout: number; // Timeout duration
  debug: boolean; // Debug mode on/off
}
```

### 5. Data Models

We use interfaces when defining basic data structures in our application:

```typescript
interface Order {
  id: string; // Order number
  customerId: string; // Customer number
  products: string[]; // Products in order
  totalAmount: number; // Total amount
  status: 'pending' | 'confirmed' | 'cancelled'; // Order status
}
```

These use cases show how important interfaces are in terms of type safety and code organization. In each area, interfaces help make our code safer and easier to maintain.

## Best Practices and Tips

There are some important points to consider when using TypeScript interfaces. By applying these practices, we can write more readable and maintainable code.

### 1. Naming Conventions

Interface names should clearly indicate what they do and follow certain standards:

```typescript
// ✅ Good Naming Examples
interface UserService {
  getUser(id: string): Promise<User>;
}

interface ProductRepository {
  updateStock(productId: string, quantity: number): void;
}

// ❌ Naming Conventions to Avoid
interface IUser {
  // Don't use 'I' prefix
  // ...
}

interface dataManager {
  // Use PascalCase
  // ...
}

interface DATA_SERVICE {
  // Don't use UPPERCASE
  // ...
}
```

### 2. Single Responsibility Principle

Each interface should focus on a single task and do it well:

```typescript
// ✅ Well-Designed Interfaces
interface IdentityInformation {
  id: string;
  ssn: string;
  passportNo?: string;
}

interface ContactInformation {
  email: string;
  phone?: string;
  address?: {
    state: string;
    city: string;
  };
}

// Combine when needed
interface User extends IdentityInformation, ContactInformation {
  firstName: string;
  lastName: string;
}

// ❌ Situation to Avoid: Putting everything in one interface
interface HugeInterface {
  // Identity information
  id: string;
  ssn: string;

  // Contact information
  email: string;
  phone: string;

  // Address information
  state: string;
  city: string;

  // User information
  firstName: string;
  lastName: string;

  // Other information...
  // ... and many more properties
}
```

### 3. Adding Descriptive Comments

Add comments that clearly indicate how interfaces should be used:

```typescript
/**
 * Card information to be used during payment process.
 * This interface should only be used during payment processing and
 * sensitive information should be cleared from memory after the transaction.
 */
interface PaymentInformation {
  /** Amount to be paid (in USD, with cent precision) */
  amount: number;

  /** 16-digit card number */
  cardNumber: string;

  /** Expiration date in MM/YY format (example: 12/25) */
  expirationDate: string;

  /** 3-digit security code on the back of the card */
  securityCode: string;
}
```

### 4. Using Optional Properties Correctly

Be careful when using optional properties and document them:

```typescript
interface UserProfile {
  // Required fields
  id: string;
  name: string;
  email: string;

  // Optional fields - explain why they're optional
  /** User may choose not to provide a phone number */
  phone?: string;

  /** Default will be used if no profile photo is uploaded */
  profilePhotoUrl?: string;

  /** User may not have given location permission yet */
  location?: {
    lat: number;
    lng: number;
  };
}
```

## Conclusion

Interfaces are one of the most powerful features of TypeScript, and when used correctly, they:

- Make your code more readable
- Provide type safety
- Make maintenance easier
- Improve team collaboration
- Serve as documentation

The examples and best practices we've seen in this article will guide you in your daily TypeScript development. See you in the next article.

Feel free to leave comments if you have any questions. Happy coding! 🚀
