---
title: "Understanding TypeScript - Part 5: The TypeScript Compiler"
summary: In this article, we will examine the TypeScript compiler in detail. We will learn how to manage compiler settings, configuration options, and project configuration.
date: 2025-02-12T17:05:00+03:00
cardimage: '/en/blog/typescript-series/understanding-typescript-part5/images/cover.svg'
featureimage: '/en/blog/typescript-series/understanding-typescript-part5/images/cover.svg'
featured: true
categories: ['TypeScript']
tags: ['typescript', 'compiler', 'configuration']
translationKey: 'typescript-compiler-guide'
authors:
  - Çağatay: /images/author.jpeg
---

Hello! In this part of our TypeScript series, we will examine how our code is transformed into JavaScript and how we can manage this process. The TypeScript compiler and configuration options are among the important topics that form the foundation of our projects.

## What is the TypeScript Compiler?

The TypeScript compiler (tsc) is a tool that converts our TypeScript code into JavaScript code that browsers can understand. It transforms the code we write using modern TypeScript features into our targeted JavaScript version.

### Starting the Project: tsc --init

When starting our TypeScript project, our first step is to create a configuration file. We can do this with the `tsc --init` command:

```bash
npx tsc --init
```

This command creates a `tsconfig.json` file in our project's root directory. This file contains the basic settings that determine the compiler's behavior:

```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## Development with Watch Mode

We can use Watch Mode to instantly compile changes we make to our code during development:

```bash
tsc --watch
# or
tsc -w
```

Advantages of Watch Mode:

1. Automatically detects file changes
2. Performs instant compilation
3. Shows errors in real-time
4. Speeds up the development process

```typescript
// example.ts
let message = 'Hello TypeScript!';
console.log(message);

// When Watch Mode is active, the JavaScript file
// is automatically updated when you save the file
```

## Multiple File Compilation

The TypeScript compiler can compile all TypeScript files in our project with a single command. When we run the `tsc` command in the project directory, all `.ts` files are automatically compiled:

```typescript
// user.ts
export interface User {
  id: number;
  name: string;
}

// service.ts
import { User } from './user';
export class UserService {
  // service code
}

// Both files are compiled with a single command
```

## tsconfig.json and Basic Settings

### Files Option

The `files` option in the `tsconfig.json` file allows us to explicitly specify which files to compile:

```json
{
  "compilerOptions": {
    // other options
  },
  "files": ["src/main.ts", "src/utils/helpers.ts", "src/types/index.d.ts"]
}
```

### Include and Exclude Options

We use `include` and `exclude` options to determine which files should be compiled and which should be excluded in our project:

```json
{
  "compilerOptions": {
    // other options
  },
  "include": [
    "src/**/*" // all files under src directory
  ],
  "exclude": [
    "node_modules", // exclude node_modules directory
    "**/*.test.ts", // exclude test files
    "src/temp/*" // exclude temporary files
  ]
}
```

This configuration is particularly useful for:

- Keeping the `node_modules` directory out of compilation
- Separating test files from production code
- Excluding specific directories or file types

### OutDir: Determining Output Directory

We specify where the compiled JavaScript files will be saved using the `outDir` option:

```json
{
  "compilerOptions": {
    "outDir": "./dist"
  }
}
```

With this configuration:

- TypeScript files stay in the `src` folder
- Compiled JavaScript files are saved to the `dist` folder
- Project structure becomes more organized

Example project structure:

```
project/
├── src/
│   ├── index.ts
│   └── utils/
│       └── helpers.ts
├── dist/
│   ├── index.js
│   └── utils/
│       └── helpers.js
└── tsconfig.json
```

### Target: JavaScript Version Target

The `target` option determines which JavaScript version our TypeScript code will be compiled to:

```json
{
  "compilerOptions": {
    "target": "es2020"
    // Other options: "es3", "es5", "es6", "es2016", "es2017", "esnext"
  }
}
```

An example showing the effect of different targets:

```typescript
// TypeScript code
class Animal {
  constructor(public name: string) {}
}

// output for target: "es5"
var Animal = /** @class */ (function () {
  function Animal(name) {
    this.name = name;
  }
  return Animal;
})();

// output for target: "es2020"
class Animal {
  constructor(name) {
    this.name = name;
  }
}
```

## Strict Mode and Null Checks

We can enable `strict` mode to use TypeScript's type safety features at the highest level:

```json
{
  "compilerOptions": {
    "strict": true,
    "strictNullChecks": true
  }
}
```

Effect of these settings:

```typescript
// strict: false
let name: string;
name = null; // No problem

// strict: true and strictNullChecks: true
let name: string;
name = null; // Error! Type 'null' is not assignable to type 'string'

// Correct usage
let name: string | null;
name = null; // Now it works
```

## Including JavaScript Files

In some projects, we may need to use JavaScript and TypeScript files together. The `allowJs` option makes this possible:

```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true // Performs type checking in JavaScript files
  }
}
```

This feature is particularly useful when:

- Converting existing JavaScript projects to TypeScript
- Working with third-party JavaScript libraries
- Doing gradual TypeScript adaptation

Example usage:

```typescript
// utils.js (JavaScript file)
export function add(a, b) {
  return a + b;
}

// index.ts (TypeScript file)
import { add } from './utils.js';
const result = add(5, 3); // TypeScript type checking works
```

## Best Practices

1. **Adjust Configuration According to Project**

   ```json
   {
     "compilerOptions": {
       "target": "es2020", // For modern browsers
       "module": "esnext", // Modern module system
       "strict": true, // Strict type checking
       "outDir": "./dist", // Output directory
       "rootDir": "./src" // Source directory
     }
   }
   ```

2. **Use Watch Mode Effectively**

   ```bash
   # package.json
   {
     "scripts": {
       "dev": "tsc --watch",
       "build": "tsc"
     }
   }
   ```

3. **Keep Project Structure Organized**

   ```
   project/
   ├── src/           # TypeScript source files
   ├── dist/          # Compiled JavaScript files
   ├── tests/         # Test files
   ├── tsconfig.json  # TypeScript configuration
   └── package.json
   ```

4. **Appropriate Include/Exclude Configuration**
   ```json
   {
     "include": ["src/**/*"],
     "exclude": ["node_modules", "**/*.test.ts", "**/*.spec.ts"]
   }
   ```

## Conclusion

The TypeScript compiler and configuration options are an important part of modern web development processes. With proper configuration, you can:

- Write safer code
- Speed up the development process
- Better organize project structure
- Make team collaboration easier

In our next article, we'll examine more advanced features of TypeScript. See you then!
