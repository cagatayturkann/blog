---
title: 'Understanding TypeScript - Part 1: The Basics'
summary: TypeScript is a powerful superset of JavaScript that adds static typing to the language. In this article series, we'll explore TypeScript from basics to advanced concepts, starting with fundamental types and type inference.
date: 2025-02-08T23:19:00+03:00
cardimage: '/en/blog/typescript-series/understanding-typescript-part1/images/cover.svg'
featureimage: 'en/blog/typescript-series/understanding-typescript-part1/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'javascript']
translationKey: "understanding-typescript-part1"
authors:
  - Çağatay: /images/author.jpeg
---

Hello everyone! As a developer who has recently dived deep into TypeScript, I want to share my learning journey with you. In this series, we'll explore TypeScript from the ground up, understanding why it exists and how it can make our JavaScript development experience better.

## What is TypeScript?

TypeScript is JavaScript with types. That's the simplest way to describe it, but there's much more to it. It's a superset of JavaScript developed by Microsoft that adds static typing to the language. This means that all valid JavaScript code is also valid TypeScript code, but TypeScript adds additional features that help us write more robust and maintainable code.

Let's look at a simple example of how TypeScript differs from JavaScript:

```typescript
// JavaScript
let greeting = "Hello";
greeting = 42; // This works in JavaScript, but might cause problems

// TypeScript
let greeting: string = "Hello";
greeting = 42; // Error: Type 'number' is not assignable to type 'string'
```

## Why Types?

TypeScript's type system offers several key benefits:

1. **Helps us find errors:** The type system can catch bugs before our code runs
2. **Analyzes our code as we type:** Provides real-time feedback in our editor
3. **Only exists in development:** Types are removed when code is compiled to JavaScript

These features make TypeScript particularly valuable for large codebases and team collaborations.

## Basic Types in TypeScript

Let's explore the fundamental types in TypeScript:

### 1. Strings

Strings represent text values in TypeScript. Here's how we work with them:

```typescript
// Declaring a string variable
let myString: string = "Words!!!";

// CAN'T reassign to a different type
myString = 100; // Error

// CAN reassign to a value of same type
myString = "New words!!!"; // Works fine
```

In this example, once we declare `myString` as a string, TypeScript ensures we can only assign string values to it.

### 2. Numbers

TypeScript simplifies number handling compared to other languages:

```typescript
// Declaring a number variable
let myNumber: number = 42;

// CAN'T reassign to a different type
myNumber = "I'm a string!"; // Error

// CAN reassign to a value of same type
myNumber = 60; // Works fine
```

Unlike some programming languages that have multiple number types (float, int, etc.), TypeScript (like JavaScript) just uses the `number` type for all numeric values.

### 3. Booleans

Boolean values represent true/false conditions:

```typescript
// Declaring a boolean variable
const myBoolean: boolean = true;

// CAN'T reassign to a different type
myBoolean = 87; // Error

// CAN reassign to a value of same type
myBoolean = false; // Works fine
```

## Type Inference

One of TypeScript's most powerful features is its ability to infer types automatically. This means you don't always have to explicitly declare types:

```typescript
// Creating a variable with a value,
// but without a type annotation
let x = 27;

x = 'Twenty-seven'; // Error - Type 'string' is not assignable to type 'number'
```

In this example, TypeScript automatically infers that `x` is a number based on its initial value. This feature helps us write more concise code while maintaining type safety.

## The 'any' Type

Sometimes we need more flexibility than strict typing allows. That's where the `any` type comes in:

```typescript
// Declaring a variable with type 'any'
const myComplicatedData: any = "I'm going to be complicated!";

// CAN reassign to any type - type checks are off!
myComplicatedData = 87;        // Works
myComplicatedData = 'abc...';  // Works
myComplicatedData = true;      // Works
```

### When Should We Use 'any'?

While it's generally recommended to avoid `any`, there are legitimate use cases for it. Here's a real-world scenario:

```typescript
// Working with external API data
function handleAPIResponse(response: any) {
    // We might not know the exact structure of the API response
    // especially when working with third-party APIs
    console.log(response.data);        // Works
    console.log(response.status);      // Works
    console.log(response.someField);   // Works
}

// Working with legacy JavaScript code
declare const oldJavaScriptLibrary: any;
// We can use the library without TypeScript errors
oldJavaScriptLibrary.someOldMethod();
```

Common scenarios where `any` might be necessary:
1. When integrating with external APIs where the response structure is unknown or dynamic
2. During migration from JavaScript to TypeScript (temporary usage)
3. When working with third-party libraries that don't have TypeScript type definitions
4. When dealing with truly dynamic content where the type cannot be predicted

However, remember that using `any` removes all the benefits of TypeScript's type checking. It should be used as a last resort, and you should always try to define proper types when possible.

## Best Practices

1. **Let TypeScript Infer When Possible**
   - Don't add type annotations when TypeScript can infer the type correctly
   - This makes your code cleaner and more maintainable

2. **Avoid 'any'**
   - Using `any` removes all the benefits of TypeScript
   - Only use it when you have a very specific reason to do so

3. **Be Explicit When Necessary**
   - Add type annotations when TypeScript's inference isn't sufficient
   - This improves code readability and helps catch errors

## Practical Example

Let's look at a practical example combining what we've learned:

```typescript
// Creating variables with different types
let username: string = "John Doe";
let age: number = 30;
let isLoggedIn: boolean = true;

// Using type inference
let lastLoginDate = new Date();  // TypeScript infers Date type
let loginCount = 5;              // TypeScript infers number type

// Working with these variables
function displayUserInfo() {
    console.log(`User: ${username}`);
    console.log(`Age: ${age}`);
    console.log(`Logged In: ${isLoggedIn}`);
    console.log(`Last Login: ${lastLoginDate}`);
    console.log(`Login Count: ${loginCount}`);
}

// TypeScript will catch these errors:
username = 123;          // Error: Type 'number' is not assignable to type 'string'
age = "thirty";         // Error: Type 'string' is not assignable to type 'number'
isLoggedIn = "yes";     // Error: Type 'string' is not assignable to type 'boolean'
```

This example shows how TypeScript helps us maintain type safety in a real application scenario, preventing common type-related bugs before they happen.

## Conclusion

This introduction to TypeScript covers the basics of types and type inference. TypeScript adds a powerful type system to JavaScript that can help us write more reliable code. In the next part of this series, we'll dive deeper into interfaces, functions, and more advanced TypeScript features.

Remember that TypeScript is designed to help us catch errors early and make our code more maintainable. While it might seem like extra work at first, the benefits become clear as your projects grow in size and complexity.

Stay tuned for Part 2 where we'll explore more advanced TypeScript concepts!