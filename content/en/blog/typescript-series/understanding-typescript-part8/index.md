---
title: "Understanding TypeScript - Part 8: Type Narrowing"
summary: In this article, we'll explore type narrowing techniques in TypeScript. We'll cover type checking methods like typeof, instanceof, type predicates, and discriminated unions.
date: 2025-02-21T10:30:00+03:00
cardimage: '/en/blog/typescript-series/understanding-typescript-part8/images/cover.svg'
featureimage: '/en/blog/typescript-series/understanding-typescript-part8/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'type-narrowing', 'type-guards']
translationKey: 'typescript-type-narrowing'
authors:
  - Cagatay: /images/author.jpeg
---

Hello! In this part of our TypeScript series, we'll explore type narrowing, one of the most powerful features of the type system. Type narrowing refers to the techniques that allow us to narrow down a variable's type to a more specific type. These techniques help us work more safely with union types and complex type structures.

## What is Type Narrowing?

Type narrowing is TypeScript's ability to narrow down a variable's type to a more specific type within a context. This feature is particularly useful when working with union types. For example, if a variable can be either a string or a number, but we know it's definitely a string in a specific code block, TypeScript uses this information to enhance type safety.

## typeof Type Guards

The typeof operator is one of the most basic type narrowing methods in TypeScript:

```typescript
const isTeenager = (age: number | string) => {
  if (typeof age === 'string') {
    // Here age is definitely a string
    return age.charAt(0) === '1';
  } else {
    // Here age is definitely a number
    return age > 12 && age < 20;
  }
};

isTeenager('20'); // false
isTeenager(13); // true
```

Advantages of using typeof type guards:

- Provides type safety
- Improves IDE support and code completion features
- Prevents runtime errors
- Increases code readability

## Truthiness Type Guards

We can also perform type narrowing using JavaScript's truthiness feature:

```typescript
const printLetters = (word: string | null) => {
  if (!word) {
    console.log('No word was provided.');
    return;
  }

  // Here word is definitely a string
  word.split('').forEach((letter) => console.log(letter));
};

printLetters('Hello'); // H, e, l, l, o
printLetters(null); // No word was provided.
```

Truthiness check evaluates these values as false:

- false
- 0
- ""
- null
- undefined
- NaN

## Equality Type Narrowing

Equality comparisons are also used for type narrowing in TypeScript:

```typescript
const someFunc = (x: string | boolean, y: string | number) => {
  if (x === y) {
    // Here both x and y are definitely strings
    console.log(x.toUpperCase());
    console.log(y.toLowerCase());
  } else {
    // x: string | boolean
    // y: string | number
    console.log(x);
    console.log(y);
  }
};
```

## in Operator Type Guards

JavaScript's in operator checks if a property exists in an object. TypeScript uses this check for type narrowing:

```typescript
type Cat = { meow: () => void };
type Dog = { bark: () => void };

const talk = (creature: Cat | Dog) => {
  if ('meow' in creature) {
    // Here creature is definitely a Cat
    creature.meow();
  } else {
    // Here creature is definitely a Dog
    creature.bark();
  }
};

const kitty: Cat = { meow: () => console.log('MEOWWW') };
talk(kitty); // MEOWWW
```

## instanceof Narrowing

The instanceof operator checks if a variable is an instance of a specific class:

```typescript
const printFullDate = (date: Date | string) => {
  if (date instanceof Date) {
    // Here date is definitely a Date
    return date.toUTCString();
  } else {
    // Here date is definitely a string
    return new Date(date).toUTCString();
  }
};

console.log(printFullDate(new Date()));
console.log(printFullDate('2025-02-21'));
```

## Type Predicates

In TypeScript, you can write custom type guard functions. These functions have a return type in the format parameterName is Type:

```typescript
interface Cat {
  meow: () => void;
}
interface Dog {
  bark: () => void;
}

// Type predicate function
function isCat(pet: Cat | Dog): pet is Cat {
  return (pet as Cat).meow !== undefined;
}

let pet = getAnimal();
if (isCat(pet)) {
  // Here pet is definitely a Cat
  pet.meow();
} else {
  // Here pet is definitely a Dog
  pet.bark();
}
```

Advantages of type predicates:

- You can write custom type guard logic
- Prevents code duplication
- Centralizes type checks
- Improves readability

## Discriminated Unions

Discriminated unions is a technique for distinguishing between related types using a common literal property:

```typescript
interface Circle {
  kind: 'circle';
  radius: number;
}

interface Square {
  kind: 'square';
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape) {
  switch (shape.kind) {
    case 'circle':
      // Here shape is definitely a Circle
      return Math.PI * shape.radius ** 2;
    case 'square':
      // Here shape is definitely a Square
      return shape.sideLength ** 2;
  }
}
```

Advantages of discriminated unions:

- Provides type safety
- Easy to use with switch cases
- Excellent IDE support
- Easy to add new types
- Catches missing cases at compile time

## Best Practices

1. **Choosing the Right Type Guard**

   ```typescript
   // typeof for simple types
   function processValue(value: string | number) {
     if (typeof value === 'string') {
       return value.toUpperCase();
     }
     return value.toFixed(2);
   }

   // instanceof for classes
   function processDate(date: Date | string) {
     if (date instanceof Date) {
       return date.toISOString();
     }
     return new Date(date).toISOString();
   }
   ```

2. **Effective Use of Type Predicates**

   ```typescript
   interface User {
     id: number;
     name: string;
   }

   interface Admin extends User {
     role: 'admin';
     permissions: string[];
   }

   function isAdmin(user: User): user is Admin {
     return 'role' in user && user.role === 'admin';
   }
   ```

3. **Properly Structuring Discriminated Unions**

   ```typescript
   interface ApiSuccess {
     status: 'success';
     data: any;
   }

   interface ApiError {
     status: 'error';
     error: string;
   }

   type ApiResponse = ApiSuccess | ApiError;

   function handleResponse(response: ApiResponse) {
     if (response.status === 'success') {
       processData(response.data);
     } else {
       handleError(response.error);
     }
   }
   ```

## Conclusion

Type narrowing is one of TypeScript's most powerful features. With these techniques, you can:

- Write safer code
- Reduce runtime errors
- Get maximum benefit from IDE support
- Manage complex type structures more easily

In our next article, we'll continue exploring other advanced features of TypeScript. See you soon!
