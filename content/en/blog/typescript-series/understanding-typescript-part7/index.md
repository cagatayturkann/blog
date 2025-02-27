---
title: "Understanding TypeScript - Part 7: Generics"
summary: In this article, we'll explore generics, one of TypeScript's most powerful features. We'll learn how to write reusable code while maintaining type safety.
date: 2025-02-12T19:30:00+03:00
cardimage: '/en/blog/typescript-series/understanding-typescript-part7/images/cover.svg'
featureimage: '/en/blog/typescript-series/understanding-typescript-part7/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'generics', 'type-safety']
translationKey: 'typescript-generics'
authors:
  - Cagatay: /images/author.jpeg
---

Hello! In this part of our TypeScript series, we'll explore generic structures that make your code more flexible and reusable. Generics allow us to write functions and classes that can work with different data types while maintaining type safety.

## What are Generic Structures?

Generics is a feature that allows a function or class to work with different types. The key point here is maintaining type safety while providing this flexibility. Let's start with a simple example:

```typescript
// Non-generic approach - Separate function for each type
function getFirstNumber(arr: number[]): number {
  return arr[0];
}

function getFirstString(arr: string[]): string {
  return arr[0];
}

// Generic approach - Single function, all types
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

// Usage
const firstNumber = getFirst<number>([1, 2, 3]); // type number
const firstText = getFirst<string>(['a', 'b', 'c']); // type string
```

Here, `<T>` is a type parameter that specifies which types the function will work with. Since TypeScript can infer types in most cases, we don't need to explicitly specify the type parameter:

```typescript
const firstNumber = getFirst([1, 2, 3]); // automatically infers number type
const firstText = getFirst(['a', 'b', 'c']); // automatically infers string type
```

## Built-in Generic Types

TypeScript has several commonly used built-in generic types:

### Array<T>

```typescript
let numbers: Array<number> = [1, 2, 3]; // number[]
let texts: Array<string> = ['a', 'b']; // string[]
```

### Promise<T>

```typescript
async function fetchData(): Promise<User> {
  const response = await fetch('/api/user');
  return response.json();
}
```

### Record<K,V>

```typescript
type UserRoles = Record<string, string[]>;

const roles: UserRoles = {
  admin: ['read', 'write', 'delete'],
  editor: ['read', 'write'],
  user: ['read'],
};
```

## Writing Generic Functions

Here are some key points to consider when writing generic functions:

```typescript
// Simple generic function
function reverse<T>(items: T[]): T[] {
  return items.reverse();
}

// Generic function - Arrow function syntax
const filter = <T>(arr: T[], fn: (item: T) => boolean): T[] => {
  return arr.filter(fn);
};

// Usage examples
const numbers = reverse<number>([1, 2, 3]);
const texts = reverse(['a', 'b', 'c']); // Type inference

const filteredArray = filter([1, 2, 3, 4], (n) => n % 2 === 0);
```

### Multiple Type Parameters

Sometimes we need multiple type parameters:

```typescript
function pair<T, U>(x: T, y: U): [T, U] {
  return [x, y];
}

const result = pair<string, number>('hello', 42);
const automatic = pair('hello', 42); // Type inference works
```

## Type Constraints

We can use type constraints to specify what properties generic types should have:

```typescript
interface Length {
  length: number;
}

function showLength<T extends Length>(arg: T): number {
  return arg.length;
}

// Works - string has length property
showLength('Hello');

// Works - array has length property
showLength([1, 2, 3]);

// Error - number doesn't have length property
// showLength(123);
```

## Generic Classes

We can also use generic structures in classes:

```typescript
class DataContainer<T> {
  private data: T[];

  constructor(initialData: T[]) {
    this.data = initialData;
  }

  add(item: T): void {
    this.data.push(item);
  }

  get(index: number): T {
    return this.data[index];
  }

  getAll(): T[] {
    return this.data;
  }
}

// Usage
const numberContainer = new DataContainer<number>([1, 2, 3]);
numberContainer.add(4);
console.log(numberContainer.getAll()); // [1, 2, 3, 4]

const textContainer = new DataContainer<string>(['a', 'b', 'c']);
textContainer.add('d');
console.log(textContainer.get(0)); // 'a'
```

## Generic Interface's

Interfaces can also use generic structures:

```typescript
interface APIResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface User {
  id: number;
  name: string;
}

// Usage
function fetchUser(): Promise<APIResponse<User>> {
  return fetch('/api/user').then((res) => res.json());
}

// async/await usage
async function getUser(): Promise<APIResponse<User>> {
  const response = await fetch('/api/user');
  return response.json();
}
```

## Generic Type Inference (Type Inference)

TypeScript's type inference system is very powerful. In most cases, we don't need to explicitly specify generic types:

```typescript
// Explicit type parameter
const x = getFirst<number>([1, 2, 3]);

// Allow type inference
const y = getFirst([1, 2, 3]); // automatically infers number type

// Type inference for generic class
const container = new DataContainer(['a', 'b', 'c']); // automatically infers string[] type
```

## Best Practices

1. **Generic Naming Rules**

```typescript
// Common generic type names:
// T: Type (General type parameter)
// K: Key (Primary type)
// V: Value (Value type)
// E: Element (Element type)
// P: Properties (Property type)
```

2. **Using Type Constraints Correctly**

```typescript
// Good usage
interface HasId {
  id: number;
}

function getById<T extends HasId>(items: T[], id: number): T | undefined {
  return items.find((item) => item.id === id);
}
```

3. **Generic Constraint vs Union Types**

```typescript
// Generic constraint usage
function process<T extends string | number>(value: T): T {
  return value;
}

// Union type usage
function process2(value: string | number): string | number {
  return value;
}

// Generic constraint is safer and maintains type information
```

4. **Keep Generics Simple**

```typescript
// Complex
function processData<T, U, V, W>(
  data: T[],
  transformer: (item: T) => U,
  validator: (transformed: U) => V,
  formatter: (validated: V) => W
): W[] {
  // ...
}

// Better - Use separate interfaces for intermediate types
interface DataProcessor<T, R> {
  transform(item: T): R;
}

function processData<T, R>(data: T[], processor: DataProcessor<T, R>): R[] {
  return data.map((item) => processor.transform(item));
}
```

## Generic's with Arrow Functions

When using generic arrow functions in TypeScript, there are some syntax features to consider:

```typescript
// Standard generic arrow function
const identity = (arg: T): T => arg;

// Prevent TSX conflict when using with React
const identity = (arg: T): T => arg;

// Arrow function with multiple type parameters
const pair = (first: T, second: U): [T, U] => [first, second];
```

## Generic's with Asynchronous Operations

Asynchronous operations are common in modern web applications. Generics can also help in this area:

```typescript
// Generic async function
async function fetchData(url: string): Promise {
  const response = await fetch(url);
  return response.json();
}

// Usage
interface User {
  id: number;
  name: string;
  email: string;
}

// Type-safe API call
const user = await fetchData('/api/user/1');
console.log(user.name); // Type-safe access

// Generic error handling
interface APIError {
  code: number;
  message: string;
}

async function fetchWithError(): Promise {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw (await response.json()) as APIError;
    }
    return response.json();
  } catch (error) {
    throw error as APIError;
  }
}
```

## Generics with Utility Types

TypeScript's built-in utility types also use generic structures. Here are some common examples:

```typescript
// Partial - Makes all properties optional
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial;
// { id?: number; name?: string; email?: string; }

// Pick - Selects specific properties
type UserBasicInfo = Pick;
// { name: string; email: string; }

// Omit - Removes specific properties
type UserWithoutId = Omit;
// { name: string; email: string; }

// Record - Creates key-value structure
type UserRoles = Record;
// { [key: string]: string[] }
```

## Advanced Type Inference Topics

TypeScript's type inference system is very advanced. Here are some advanced examples:

```typescript
// Return type inference
function createPair(first: T) {
  return {
    first,
    second: first,
  };
}
// TypeScript automatically infers { first: T, second: T } type

// Generic constraints with type inference
interface HasLength {
  length: number;
}

function longest(a: T, b: T): T {
  return a.length >= b.length ? a : b;
}

// TypeScript makes separate inferences for string[] and string
const longerArray = longest([1, 2], [1, 2, 3]); // type: number[]
const longerString = longest('123', '12345'); // type: string
```

## Generic Type Alias vs Interface

When using generic structures with type alias and interface, there are some differences:

```typescript
// Generic type alias
type Container = {
  value: T;
  tag: string;
};

// Generic interface
interface Box {
  value: T;
  tag: string;
}

// They are used similarly
const numberContainer: Container = { value: 42, tag: 'number' };
const stringBox: Box = { value: 'test', tag: 'text' };

// Interfaces can be extended
interface LabeledBox extends Box {
  label: string;
}

// Type aliases can be extended with intersection types
type LabeledContainer = Container & {
  label: string;
};
```

## Important Points and Tips

1. **Carefully Use Generic Constraints**

   - Very broad constraints reduce type safety
   - Very narrow constraints reduce reusability

2. **Trust Type Inference**

   - TypeScript can infer the correct type in most cases
   - Don't explicitly specify generic types unnecessarily

3. **Maintain Readability**
   - Generic type names should be meaningful
   - Avoid too many type parameters
   - Break down complex generic structures into smaller parts

## Conclusion

Generics are one of the most powerful features of TypeScript. When used correctly:

- Reduce code repetition
- Increase type safety
- Allow reusable and flexible code
- Provide better IDE support

Especially when developing large projects or libraries, using generics can maximize the power of generics. Understanding and using generics is a must when developing with TypeScript.

Our next article will explore other advanced features of TypeScript. See you soon!

## Default Type Parameters

We can assign default values to generic types:

```typescript
class Queue<T = number> {
  private data: T[] = [];

  push(item: T) {
    this.data.push(item);
  }

  pop(): T | undefined {
    return this.data.shift();
  }
}

// Uses number type by default
const numberQueue = new Queue();

// Customized for string type
const textQueue = new Queue<string>();
```

## Generic Interfaces

We can also use generic structures in interfaces:

```typescript
interface APIResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface User {
  id: number;
  name: string;
}

// Usage
function fetchUser(): Promise<APIResponse<User>> {
  return fetch('/api/user').then((res) => res.json());
}

// Usage with async/await
async function getUser(): Promise<APIResponse<User>> {
  const response = await fetch('/api/user');
  return response.json();
}
```
