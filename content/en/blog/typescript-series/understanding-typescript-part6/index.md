---
title: "Understanding TypeScript - Part 6: Classes"
summary: In this article, we will examine TypeScript's class structure in detail. We will cover topics such as class definitions, access modifiers, getter/setter methods, and abstract classes.
date: 2025-02-12T18:15:00+03:00
cardimage: '/en/blog/typescript-series/understanding-typescript-part6/images/cover.svg'
featureimage: '/en/blog/typescript-series/understanding-typescript-part6/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'classes', 'oop']
translationKey: "typescript-classes"
authors:
  - Çağatay: /images/author.jpeg
---

Hello! In this part of our TypeScript series, we will examine classes, one of the fundamental building blocks of object-oriented programming. We'll learn about the advantages of TypeScript's class structure over JavaScript, how type safety is ensured, and the features you can use to make your code safer.

## The Concept of Classes in TypeScript

TypeScript classes support all features of JavaScript classes while providing additional type safety and powerful tools for object-oriented programming. Let's start with a simple example:

```typescript
// A class in JavaScript
class JsUser {
  constructor(name) {image.png
    this.name = name;
  }
}

// The same class in TypeScript
class TsUser {
  name: string;  // Type definition
  
  constructor(name: string) {  // Parameter type
    this.name = name;
  }
}
```

In the TypeScript version:
- Class property types are explicitly specified
- Constructor parameters have type safety
- IDEs provide better code completion support

## Basic Class Structure

Here are the basic structures we can use when creating a TypeScript class:

```typescript
class User {
  // Class properties (fields)
  id: number;
  name: string;
  private email: string;
  readonly registrationDate: Date;

  // Constructor method
  constructor(id: number, name: string, email: string) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.registrationDate = new Date();
  }

  // Class method
  displayInfo(): string {
    return `${this.name} (ID: ${this.id})`;
  }
}

// Using the class
const user = new User(1, "John Smith", "john@example.com");
console.log(user.displayInfo());  // "John Smith (ID: 1)"
```

In this example:
1. We defined types for class properties
2. We specified parameter types in the constructor
3. We defined an immutable property with readonly
4. We restricted access with private
5. We specified the method return type

## Class Fields and Access Modifiers

TypeScript has three types of access modifiers: public, private, and protected. Let's examine their usage and differences from JavaScript:

### Public Access Modifier

This is the default access level and doesn't require any modifier:

```typescript
class Car {
  brand: string;      // public by default
  public model: string; // explicitly marked as public

  constructor(brand: string, model: string) {
    this.brand = brand;
    this.model = model;
  }
}

const car = new Car("Toyota", "Corolla");
console.log(car.brand);   // Accessible
console.log(car.model);   // Accessible
```

### Private Access Modifier

Private properties can only be accessed from within the class. In TypeScript, we can define private properties in two different ways:

```typescript
class Account {
  private _balance: number;       // TypeScript private
  #transactions: number[];        // JavaScript private field (#)

  constructor(initialBalance: number) {
    this._balance = initialBalance;
    this.#transactions = [];
  }

  deposit(amount: number): void {
    this._balance += amount;
  }

  addTransaction(amount: number): void {
    this.#transactions.push(amount);
    this._balance -= amount;
  }
}

const account = new Account(1000);
// account._balance;     // Error: Cannot access private property
// account.#transactions; // Error: Cannot access private field
```

Differences between JavaScript and TypeScript private implementations:
- TypeScript's private modifier works at compile time
- JavaScript's # private fields feature works at runtime
- Using # provides real access restriction
- The private keyword only provides protection on the TypeScript side

### Protected Access Modifier

Protected properties can be accessed by the class itself and its subclasses:

```typescript
class Animal {
  protected species: string;

  constructor(species: string) {
    this.species = species;
  }
}

class Cat extends Animal {
  meow(): string {
    return `I'm a ${this.species}, meow!`; // Can access species property
  }
}

const cat = new Cat("cat");
// cat.species; // Error: Cannot access protected property from outside
```

## Readonly and Parameter Properties

### Readonly Properties

The readonly modifier indicates that a property can only be assigned a value during initialization and cannot be changed afterward:

```typescript
class Document {
  readonly id: string;
  readonly creationDate: Date;
  title: string;

  constructor(id: string, title: string) {
    this.id = id;  // OK
    this.creationDate = new Date();  // OK
    this.title = title;
  }

  changeId(newId: string) {
    this.id = newId;  // Error: Cannot assign to readonly property
  }
}
```

### Parameter Properties

Parameter properties, a special shorthand provided by TypeScript, automatically converts constructor parameters into class properties:

```typescript
// Long way
class Product {
  readonly id: string;
  private _price: number;
  public stock: number;

  constructor(id: string, price: number, stock: number) {
    this.id = id;
    this._price = price;
    this.stock = stock;
  }
}

// Using parameter properties
class ProductShort {
  constructor(
    readonly id: string,
    private _price: number,
    public stock: number
  ) {}
}
```

Thanks to this feature:
- You write less code
- Property definitions and assignments are done automatically
- Code becomes more readable

## Getter and Setter Methods

In TypeScript, you can define special access methods using get and set keywords:

```typescript
class Product {
  private _price: number;

  constructor(price: number) {
    this._price = price;
  }

  // Getter method
  get price(): number {
    return this._price;
  }

  // Setter method
  set price(newPrice: number) {
    if (newPrice < 0) {
      throw new Error("Price cannot be negative!");
    }
    this._price = newPrice;
  }
}

const product = new Product(100);
console.log(product.price);    // Getter is called: 100
product.price = 150;          // Setter is called
// product.price = -50;       // Throws error
```

Getter and setter methods allow you to:
- Control access to a property
- Add value validation logic
- Monitor property changes
- Return computed values

## Classes and Interfaces

In TypeScript, classes can implement one or more interfaces:

```typescript
interface LivingBeing {
  name: string;
  isAlive: boolean;
  move(): void;
}

interface FoodConsumer {
  eat(food: string): void;
}

class Human implements LivingBeing, FoodConsumer {
  constructor(public name: string) {
    this.isAlive = true;
  }

  isAlive: boolean;

  move(): void {
    console.log("Walking on two feet");
  }

  eat(food: string): void {
    console.log(`Eating ${food}`);
  }
}
```

This structure:
- Provides type safety
- Makes code maintenance easier
- Ensures all interface requirements are met

## Abstract Classes

Abstract classes are classes that cannot be instantiated directly and serve as templates for subclasses:

```typescript
abstract class Shape {
  abstract calculateArea(): number;
  abstract calculatePerimeter(): number;

  displayInfo(): string {
    return `Area: ${this.calculateArea()}, Perimeter: ${this.calculatePerimeter()}`;
  }
}

class Rectangle extends Shape {
  constructor(private width: number, private height: number) {
    super();
  }

  calculateArea(): number {
    return this.width * this.height;
  }

  calculatePerimeter(): number {
    return 2 * (this.width + this.height);
  }
}

// const shape = new Shape(); // Error: Cannot instantiate abstract class
const rectangle = new Rectangle(5, 3);
console.log(rectangle.displayInfo()); // "Area: 15, Perimeter: 16"
```

Advantages of abstract classes:
- You can define common behaviors in one place
- You can force subclasses to implement certain methods
- Increases code reusability

Important features of abstract classes:

1. Can contain both abstract and concrete (normal) methods
2. Can be used with generic types
3. Can provide helper functions to subclasses with protected methods
4. Prevent code duplication by gathering common behaviors in one place

Abstract Classes vs Interfaces:

- Abstract classes can contain implementation while interfaces only define structure
- A class can implement multiple interfaces but can only extend one abstract class
- Abstract classes can contain constructors, interfaces cannot
- Abstract classes can use access modifiers (private, protected, public)

## Best Practices

1. **Choose Private Properties Correctly**
   ```typescript
   class UserService {
     // TypeScript private: Compile-time check only
     private _apiUrl: string;
     
     // JavaScript private field: Real access restriction
     #apiKey: string;
   }
   ```

2. **Use Parameter Properties Effectively**
   ```typescript
   // Parameter properties for concise code
   class Configuration {
     constructor(
       private readonly apiUrl: string,
       private readonly timeout: number,
       public readonly versionNumber: string
     ) {}
   }
   ```

3. **Use Interfaces Effectively**
   ```typescript
   interface DataStore {
     save(data: any): Promise<void>;
     retrieve(id: string): Promise<any>;
   }

   class PostgreSQLStore implements DataStore {
     // Implement methods required by interface
   }

   class MongoDBStore implements DataStore {
     // Same interface, different implementation
   }
   ```

4. **Choose Access Modifiers Consciously**
   ```typescript
   class BankAccount {
     private _balance: number;           // No external access
     protected _accountNumber: string;   // Accessible by subclasses
     public readonly accountType: string; // Everyone can read but not modify
   }
   ```

## Conclusion

TypeScript classes combine all the features of JavaScript classes with type safety to provide a powerful object-oriented programming experience. Features like access modifiers, readonly properties, getter/setter methods, and abstract classes make your code safer and easier to maintain.
When using classes, you can:
- Increase code security by choosing the right access modifiers
- Write less code with parameter properties
- Ensure type safety with interfaces
- Reduce code duplication with abstract classes

In our next article, we'll examine more advanced features of TypeScript. See you then!