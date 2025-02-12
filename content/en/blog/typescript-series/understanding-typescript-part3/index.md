---
title: 'Understanding TypeScript - Part 3: Object Types'
summary: In this article, we will explore object types in TypeScript in detail. We will learn about object definitions, type aliases, and working with nested objects.
date: 2025-02-09T01:56:00+03:00
cardimage: '/en/blog/typescript-series/understanding-typescript-part3/images/cover.svg'
featureimage: '/en/blog/typescript-series/understanding-typescript-part3/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'javascript', 'object-types']
translationKey: 'typescript-object-types'
authors:
  - Çağatay: /images/author.jpeg
---

Hello! In this part of our TypeScript series, we will examine object types in detail. We'll particularly focus on how object types are used in functions and why type aliases are important.

## Working with Object Annotations

In TypeScript, we can use two different approaches when defining object parameters in functions. First, we can define the object type directly within the function parameter:

```typescript
// Defining object type directly in parameter
const printName = (name: { first: string; last: string }) => {
  return `Name: ${name.first} ${name.last}`;
};

// Usage
printName({ first: 'Will', last: 'Ferrell' });
```

While this approach is useful for simple objects, it can reduce readability and lead to code duplication in complex object structures. Especially if you're going to use the same object type in multiple places, it's recommended to use type aliases instead of this approach.

### Using Curly Braces

The use of curly braces when defining object types in function parameters can sometimes be confusing:

```typescript
// Syntax that might be confusing
const printPerson = (person: { name: string; age: number }): { info: string } => {
  return { info: `${person.name} is ${person.age} years old` };
};

// More readable version - Using Type Alias
type Person = {
  name: string;
  age: number;
};

type PersonInfo = {
  info: string;
};

const printPerson2 = (person: Person): PersonInfo => {
  return { info: `${person.name} is ${person.age} years old` };
};
```

The second approach is more readable because:

1. Type definitions are separated from function definition
2. Types are reusable
3. Code is more organized and easier to maintain

## Using Type Aliases

Type aliases allow us to define object types separately and reuse these types throughout our code:

```typescript
// Defining type alias
type Person = {
  name: string;
  age: number;
};

// Using in function
const sayHappyBirthday = (person: Person) => {
  return `Hey ${person.name}, congrats on turning ${person.age}!`;
};

// Using in variable
const jerry: Person = {
  name: 'Jerry',
  age: 42,
};

sayHappyBirthday(jerry);
```

Advantages of using type aliases:

1. Prevents code duplication
2. Keeps type definitions in a central location
3. Allows managing changes from a single point
4. Improves code readability

## Nested Objects

Defining nested object structures is quite common in TypeScript. Here's an example:

```typescript
const describePerson = (person: {
  name: string;
  age: number;
  parentNames: {
    mom: string;
    dad: string;
  };
}) => {
  return `Person: ${name}, Age: ${age}, Parents: ${parentNames.mom}, ${parentNames.dad}`;
};

// Usage
describePerson({
  name: 'Jimmy',
  age: 10,
  parentNames: {
    mom: 'Kim',
    dad: 'Steve',
  },
});
```

We can make this structure more organized using type aliases:

```typescript
type ParentNames = {
  mom: string;
  dad: string;
};

type PersonWithParents = {
  name: string;
  age: number;
  parentNames: ParentNames;
};

const describePerson2 = (person: PersonWithParents) => {
  const { name, age, parentNames } = person;
  return `Person: ${name}, Age: ${age}, Parents: ${parentNames.mom}, ${parentNames.dad}`;
};
```

## Excess Properties

TypeScript warns you when you try to use properties that aren't defined in an object type:

```typescript
type BasicPerson = {
  name: string;
  age: number;
};

// Will cause error
const person: BasicPerson = {
  name: 'John',
  age: 30,
  location: 'New York', // Excess property error
};

// Correct usage
const personData = {
  name: 'John',
  age: 30,
  location: 'New York',
};
const person2: BasicPerson = personData; // This works
```

## Optional Properties

Sometimes we might want some properties in an object type to be optional:

```typescript
type OptionalPerson = {
  name: string;
  age: number;
  phone?: string; // Optional property
  email?: string; // Optional property
};

// Both usages are valid
const person1: OptionalPerson = {
  name: 'Alice',
  age: 25,
};

const person2: OptionalPerson = {
  name: 'Bob',
  age: 30,
  phone: '555-0123',
  email: 'bob@email.com',
};
```

## Readonly Modifier

In TypeScript, the `readonly` modifier is used to prevent properties of an object from being modified. This is very useful for maintaining data integrity and preventing unwanted changes:

```typescript
type Person = {
  readonly name: string;
  readonly age: number;
};

const john: Person = {
  name: 'John',
  age: 30,
};

// The following lines will cause compilation errors
// john.name = "Johnny";  // Error: Cannot assign to 'name' because it is a read-only property
// john.age = 31;  // Error: Cannot assign to 'age' because it is a read-only property
```

The `readonly` modifier allows value assignment during object creation but prevents these properties from being modified later.

### Readonly Array

The `readonly` modifier can also be used for arrays. This prevents the array's contents from being modified:

```typescript
const numbers: readonly number[] = [1, 2, 3, 4, 5];

// The following methods can no longer be used
// numbers.push(6);  // Error
// numbers.pop();    // Error
// numbers[2] = 10;  // Error
```

## Intersection Types

Intersection types allow us to combine multiple types to create a new type:

```typescript
type Employee = {
  employeeId: number;
  department: string;
};

type Person = {
  name: string;
  age: number;
};

// A new type combining two types
type EmployeePerson = Employee & Person;

const worker: EmployeePerson = {
  employeeId: 1234,
  department: 'Engineering',
  name: 'Alice',
  age: 30,
};
```

Intersection types are very useful when creating complex object structures. You can combine properties from multiple types into a single type.

### Intersection Type Example

Let's look at a simple example showing how intersection types can be used:

```typescript
// Type containing address information
type Address = {
  street: string;
  city: string;
  country: string;
};

// Type containing contact information
type Contact = {
  email: string;
  phone: string;
};

// Combining types to create a complete user profile
type UserProfile = Person & Address & Contact;

// Usage example:
const user: UserProfile = {
  name: 'John',
  age: 30,
  street: 'Main Street',
  city: 'New York',
  country: 'USA',
  email: 'john@email.com',
  phone: '555-0123'
};

// Example usage in a function
function displayUserInfo(user: UserProfile) {
  console.log(`
    User: ${user.name}
    Age: ${user.age}
    Address: ${user.street}, ${user.city}, ${user.country}
    Contact: ${user.email}, ${user.phone}
  `);
}

displayUserInfo(user);
```

This example demonstrates how intersection types can be used to combine different properties:

1. **Type Composition**: We define separate types for different purposes (`Person`, `Address`, `Contact`)
2. **Intersection Types**: We combine these types using the `&` operator to create a more comprehensive type
3. **Modularity**: Each type has its own responsibility and can be managed separately
4. **Reusability**: These types can be used elsewhere in our code

This pattern is particularly useful when you want to:
- Logically separate different groups of data
- Make your code more modular
- Reuse type definitions
- Organize complex data structures

## Array Types

Array types in TypeScript can be defined in several ways:

```typescript
// First method: Using square brackets
const numbers: number[] = [1, 2, 3, 4, 5];

// Second method: Using Generic Array type
const strings: Array<string> = ['hello', 'world'];

// Mixed type array
const mixed: (number | string)[] = [1, 'two', 3, 'four'];

// Tuple (Fixed-length array with different types)
const employee: [number, string] = [1, 'John Doe'];

// Readonly array
const readonlyNumbers: readonly number[] = [1, 2, 3];
```

### Array Methods and Type Inference

TypeScript performs smart type inference with array methods:

```typescript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((x) => x * 2); // doubled type is number[]

const names = ['Alice', 'Bob', 'Charlie'];
const upperNames = names.map((name) => name.toUpperCase()); // upperNames type is string[]
```

### Multi-Dimensional Arrays

In TypeScript, multi-dimensional arrays are used to create nested arrays or matrix-like data structures:

```typescript
// 2D number array
const matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9],
];

// 3D array example
const threeDimensionalArray: number[][][] = [
  [
    [1, 2],
    [3, 4],
  ],
  [
    [5, 6],
    [7, 8],
  ],
  [
    [9, 10],
    [11, 12],
  ],
];

// Mixed type 2D array
const mixedMatrix: (number | string)[][] = [
  [1, 'two', 3],
  ['four', 5, 'six'],
];

// Operations on multi-dimensional array
const sumMatrix = (matrix: number[][]): number => {
  return matrix.flat().reduce((sum, num) => sum + num, 0);
};

console.log(sumMatrix(matrix)); // Returns sum of all elements

// Checking array dimensions
const printMatrixInfo = (matrix: number[][]) => {
  console.log(`Matrix size: ${matrix.length} x ${matrix[0].length}`);
};

printMatrixInfo(matrix); // Outputs "Matrix size: 3 x 3"
```

Multi-dimensional arrays are frequently used in areas such as image processing, game development, and scientific calculations. TypeScript provides strong type checking in such complex array structures.

## Conclusion

TypeScript's object types features increase your code's type safety and help you write clearer, easier-to-debug code. The `readonly` modifier, intersection types, flexible array types, and multi-dimensional arrays are important parts of TypeScript's powerful type system.

See you in our next article!
