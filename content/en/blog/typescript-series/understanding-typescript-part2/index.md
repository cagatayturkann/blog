---
title: 'Understanding TypeScript - Part 2: Functions'
summary: In this article, we'll explore TypeScript functions in detail, covering parameter types, return types, and special function types that make TypeScript unique and powerful.
date: 2025-02-08T23:21:00+03:00
cardimage: '/blog/en/typescript-series/understanding-typescript-part2/images/cover.svg'
featureimage: 'en/blog/typescript-series/understanding-typescript-part2/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'javascript', 'functions']
translationKey: "understanding-typescript-part2"
authors:
  - Çağatay: /images/author.jpeg
---

Hello everyone! In our previous article, we covered the basic types in TypeScript. Today, we'll dive into functions and how TypeScript makes them more powerful and safer to use. We'll start with the basics and gradually move to more advanced concepts.

## Function Parameter Types

In TypeScript, we can specify the type of function parameters, which helps prevent errors by catching them before runtime. Let's look at a simple example:

```typescript
// Creating a function with typed arguments
const encourageStudent = (name: string) => {
  return `Hey, ${name}, you're doing GREAT!`;
};

// This works fine
encourageStudent('you'); // Output: "Hey, you, you're doing GREAT!"

// This will cause a TypeScript error
encourageStudent(85); // Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

The type annotation after each parameter (`: string` in the example) tells TypeScript what type of values the function expects. This helps us catch errors during development rather than at runtime.

## Multiple Parameters

Functions can have multiple parameters, and each parameter can have its own type:

```typescript
function createUser(name: string, age: number, isActive: boolean) {
  return {
    name,
    age,
    isActive,
  };
}

// Correct usage
createUser('John', 25, true);

// TypeScript will catch these errors
createUser('John', '25', true); // Error: age should be a number
createUser('John'); // Error: missing parameters
createUser('John', 25, 'yes'); // Error: isActive should be boolean
```

In this example, TypeScript ensures that:

1. All required parameters are provided
2. Each parameter is of the correct type
3. Parameters are passed in the correct order

## Function Return Types

TypeScript can also specify what type of value a function returns. While TypeScript can often infer the return type (called type inference), explicitly declaring it can make your code more maintainable and self-documenting:

```typescript
const addNums = (x: number, y: number): number => {
  return x + y;
};

const concatenateStrings = (a: string, b: string): string => {
  return a + ' ' + b;
};

addNums(5, 5); // Returns: 10
concatenateStrings('Hello', 'World'); // Returns: "Hello World"
```

The `: number` and `: string` after the parameter lists indicate what type the functions must return. This helps in:

1. Providing documentation about what the function returns
2. Catching errors if you try to return the wrong type
3. Letting other developers know what to expect from the function

## The void Return Type

Sometimes functions don't return any value, they just perform an action. In TypeScript, we use the `void` type to indicate this:

```typescript
const warnUser = (message: string): void => {
  alert(message);
  // No return statement needed
};

const logData = (data: any): void => {
  console.log(data);
  // We didn't even write 'return'
};

// TypeScript will error if you try to use the return value
const result = logData('test'); // Error: Type 'void' is not assignable...
```

Using `void` is important because:

1. It tells other developers not to expect a return value
2. TypeScript will error if you try to return a value
3. It makes your APIs clearer and more predictable

## Optional Parameters and Default Values

TypeScript provides two ways to make parameters flexible: optional parameters and default values.

### Optional Parameters

Add a `?` after the parameter name to make it optional:

```typescript
function greetPerson(name: string, title?: string) {
  if (title) {
    return `Hello ${title} ${name}`;
  }
  return `Hello ${name}`;
}

greetPerson('John'); // Output: "Hello John"
greetPerson('John', 'Dr.'); // Output: "Hello Dr. John"
```

### Default Values

Assign a value in the parameter declaration to set a default:

```typescript
function orderCoffee(type: string = 'Americano', size: string = 'medium', milk: boolean = false) {
  let order = `${size} ${type}`;
  if (milk) order += ' with milk';
  return order;
}

orderCoffee(); // "medium Americano"
orderCoffee('Latte'); // "medium Latte"
orderCoffee('Espresso', 'small'); // "small Espresso"
orderCoffee('Mocha', 'large', true); // "large Mocha with milk"
```

Key differences between optional parameters and default values:

1. **Optional Parameters (`?`)**

   - Parameter becomes `undefined` if not provided
   - Requires checks in the function body
   - More flexible but requires more handling

2. **Default Values (`= value`)**
   - Uses specified value if parameter is omitted
   - No extra checks needed
   - Less flexible but easier to use

### Important: Parameter Order

When using both required and optional parameters, required parameters must come first:

```typescript
// CORRECT
function correct(required: string, optional?: string) {}

// WRONG - TypeScript will error
function wrong(optional?: string, required: string) {} // Error!
```

## Anonymous Functions and Type Inference

TypeScript is particularly good at inferring types in anonymous functions, especially in callbacks:

```typescript
const numbers = [1, 2, 3, 4, 5];

// TypeScript automatically infers 'number' type for 'num'
numbers.forEach((num) => {
  console.log(num.toFixed(2)); // Works because TypeScript knows num is a number
});

// Type inference in array methods
const squares = numbers.map((num) => num * num);
// squares is inferred as number[]
```

## The never Type

The `never` type is special in TypeScript and represents values that never occur. It has two main use cases:

1. **Functions that never complete:**

```typescript
function infiniteLoop(): never {
  while (true) {
    console.log("I'm still going!");
  }
}

function infiniteRecursion(): never {
  return infiniteRecursion();
}
```

2. **Functions that always throw errors:**

```typescript
function throwError(message: string): never {
  throw new Error(message);
}

function validateUser(user: never): never {
  throw new Error('Should never be called with a value');
}
```

Don't confuse `never` with `void`:

- `void` returns undefined or null (technically still a value)
- `never` means the function never completes execution

## Function Overloads

TypeScript allows you to define multiple function signatures for different parameter types:

```typescript
// Overload signatures
function combine(a: string, b: string): string;
function combine(a: number, b: number): number;

// Implementation
function combine(a: string | number, b: string | number): string | number {
  if (typeof a === 'string' && typeof b === 'string') {
    return a.concat(b);
  }
  if (typeof a === 'number' && typeof b === 'number') {
    return a + b;
  }
  throw new Error('Parameters must be of the same type!');
}

console.log(combine('Hello, ', 'World')); // "Hello, World"
console.log(combine(5, 10)); // 15
// combine("5", 10);  // Error! This combination isn't defined
```

## Best Practices

1. **Always Type Parameters**

   ```typescript
   // BAD
   function bad(name) {
     return `Hello ${name}`;
   }

   // GOOD
   function good(name: string): string {
     return `Hello ${name}`;
   }
   ```

2. **Consider Return Types**

   ```typescript
   // Type inference is sometimes enough
   const add = (a: number, b: number) => a + b;

   // But explicit return types are better for complex functions
   function processData(data: any[]): ProcessedData {
     // Complex operations...
     return processedResult;
   }
   ```

3. **Use Optional Parameters Wisely**

   - Put required parameters before optional ones
   - Consider using default values instead of optional parameters when appropriate
   - Document the behavior of optional parameters

4. **Avoid any Type**

   ```typescript
   // BAD
   function processAny(data: any) {
     return data.someMethod(); // Dangerous!
   }

   // GOOD
   function processTyped<T>(data: T) {
     // Type-safe operations
   }
   ```

## Quick Reference

Here's a quick reference of function types in TypeScript:

```typescript
// Basic function with parameter and return types
function basic(param: string): number {}

// Arrow function with type annotations
const arrow = (x: number): string => {};

// Optional parameter
function optional(name: string, age?: number) {}

// Default value
function defaultValue(name: string = 'Anonymous') {}

// Void return type
function noReturn(): void {}

// Never return type
function neverReturns(): never {}

// Function overloads
function overloaded(x: string): string;
function overloaded(x: number): number;
```

## Conclusion

TypeScript's function features provide powerful tools for writing safer and more maintainable code. Through type checking, we can catch errors early and make our code more self-documenting. While it might seem like extra work at first, the benefits become clear as your projects grow in size and complexity.