# Typescript

1. Install Typescript
   ```bash
    npm install typescript
   ```
2. Compile Typescript file.

   ```bash
   touch with-typescript.ts

   // command to transpile ts into js
   npx tsc with-typescript.ts
   ```

## Topics

- What is Typescript?
- What is the difference between Typescript and Javascript
- Why do we need typescript?
- Typescript primitives, array and object types
- Type Inference, Union Type, Type Alias in Typescript
- type, typeof, instanceof keyword, interface keyword
- Functions and Types in Typescript
- Generics in Typescript with example
- Spread operator in typescript
- classes and interfaces in Typescript
- Configuring the Typescript Compiler

# TypeScript Basics for Angular Development

A guide answering the trainer's questions, with explanations and working examples. Since you already know Java, several TypeScript concepts (interfaces, generics, access modifiers, static typing) will feel familiar — those parallels are called out where useful.

---

## 1. What is TypeScript?

TypeScript is a **strongly-typed superset of JavaScript**, developed and maintained by Microsoft. "Superset" means every valid JavaScript file is also valid TypeScript — TS just adds optional static types, interfaces, generics, enums, and access modifiers on top.

TypeScript code doesn't run directly in the browser or Node.js. It gets **compiled (transpiled)** into plain JavaScript first, using the TypeScript compiler (`tsc`).

```typescript
// hello.ts
let message: string = "Hello, TypeScript!";
console.log(message);
```

Running `tsc hello.ts` produces `hello.js`:

```javascript
var message = "Hello, TypeScript!";
console.log(message);
```

Angular itself is written in TypeScript, and the Angular CLI generates `.ts` files by default — components, services, modules — which is why this foundation matters before you start Angular.

---

## 2. Difference Between TypeScript and JavaScript

| Aspect          | TypeScript                                                                     | JavaScript                                                            |
| --------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------- |
| Typing          | Static (optional), checked at compile time                                     | Dynamic, checked only at runtime                                      |
| Compilation     | Must be compiled to JS before running                                          | Runs directly in browser/Node                                         |
| Error detection | Many errors caught while typing/compiling                                      | Errors often only surface when code runs                              |
| OOP features    | Interfaces, generics, enums, access modifiers (`public`/`private`/`protected`) | Classes exist (ES6+) but no interfaces, generics, or access modifiers |
| Tooling         | Rich autocomplete, refactoring, inline docs                                    | Weaker autocomplete since types aren't known ahead of time            |
| File extension  | `.ts` / `.tsx`                                                                 | `.js` / `.jsx`                                                        |
| Learning curve  | Slightly higher (need to learn the type system)                                | Lower to start, but harder to maintain in large codebases             |

A concrete example of why this matters:

```typescript
// TypeScript — caught immediately, before you even run the code
function add(a: number, b: number): number {
  return a + b;
}
add(5, "10"); // ❌ Compile-time error: Argument of type 'string' is not assignable to parameter of type 'number'
```

```javascript
// JavaScript — same mistake, no warning at all
function add(a, b) {
  return a + b;
}
add(5, "10"); // "510" — silently wrong, discovered only when output looks weird
```

---

## 3. Why Do We Need TypeScript?

- **Compile-time error checking** — the same safety net Java's compiler already gives you, but for JS. Typos, wrong argument types, and null-handling mistakes get caught before the app runs.
- **Better IDE support** — autocomplete, inline documentation, "go to definition," and safe refactoring across large codebases (IntelliJ IDEA and VS Code both use this heavily).
- **Self-documenting code** — a function signature like `getUser(id: number): User` tells you exactly what's expected and returned, without reading the implementation.
- **Scales better for large apps** — enterprise Angular projects (multiple modules, services, RabbitMQ/REST integrations) stay maintainable because types make cross-file contracts explicit.
- **Familiar OOP patterns** — interfaces, generics, access modifiers, and enums map closely to concepts you already use in Java/Spring.
- **Required by Angular** — Angular's decorators (`@Component`, `@Injectable`), dependency injection, and strict template type-checking all depend on TypeScript's type system.

---

## 4. TypeScript Primitives, Array and Object Types

**Primitive types:** `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`.

```typescript
let username: string = "Revanna";
let age: number = 29;
let isActive: boolean = true;
let nothing: null = null;
let notAssigned: undefined = undefined;
```

**Array types** — two equivalent syntaxes:

```typescript
let scores: number[] = [10, 20, 30];
let names: Array<string> = ["Anu", "Ravi"];
```

**Object types** — describing the shape of an object inline, or via `type`/`interface` (covered in section 6):

```typescript
let user: { name: string; age: number } = {
  name: "Revanna",
  age: 29,
};
```

---

## 5. Type Inference, Union Type, Type Alias

**Type inference** — TypeScript figures out the type automatically from the assigned value, so you don't always need explicit annotations.

```typescript
let city = "Bengaluru"; // inferred as string
// city = 5;  ❌ Error: Type 'number' is not assignable to type 'string'
```

**Union type** — a variable can hold one of several specified types, using `|`.

```typescript
let id: string | number;
id = "EMP123"; // ✅
id = 456; // ✅
// id = true;   ❌ Error
```

**Type alias** — a custom name for any type, using the `type` keyword. Useful for unions, object shapes, or anything you'll reuse.

```typescript
type ID = string | number;

type User = {
  id: ID;
  name: string;
};

const emp: User = { id: 101, name: "Revanna" };
```

---

## 6. `type`, `typeof`, `instanceof`, `interface` Keywords

**`type`** — declares a type alias. Can represent unions, tuples, primitives, functions, or object shapes. Cannot be reopened later to add more members.

**`typeof`** — has two different uses depending on context:

1. **JavaScript runtime operator** — returns a string describing a value's type.
2. **TypeScript type-query operator** — extracts the _type_ of an existing variable, used in a type position.

```typescript
// typeof — JS runtime check
let x = 10;
console.log(typeof x); // "number"

// typeof — TS type query (compile-time)
const config = { url: "api.example.com", timeout: 3000 };
type ConfigType = typeof config;
// ConfigType is now { url: string; timeout: number }
```

**`instanceof`** — a runtime check confirming whether an object is an instance of a specific class. Commonly used to narrow a union type.

```typescript
class Dog {
  bark() {
    console.log("Woof");
  }
}
class Cat {
  meow() {
    console.log("Meow");
  }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark();
  } else {
    animal.meow();
  }
}
```

**`interface`** — defines a contract/shape for an object or class, similar to a Java interface. Unlike `type`, interfaces with the same name automatically merge, and they're extendable with `extends`.

```typescript
interface Employee {
  id: number;
  name: string;
  department?: string; // optional property
}

const emp1: Employee = { id: 1, name: "Revanna" };
```

**`type` vs `interface`, in short:** use `interface` for object/class shapes you may extend (closest to Java interfaces); use `type` when you need unions, tuples, or primitive aliases that `interface` can't express.

---

## 7. Functions and Types in TypeScript

```typescript
// Basic typed function — parameter types + return type
function greet(name: string): string {
  return `Hello, ${name}`;
}

// Optional (?) and default parameters
function createUser(name: string, age?: number, role: string = "Developer") {
  return { name, age, role };
}

// Rest parameters — collects remaining args into an array
function sum(...nums: number[]): number {
  return nums.reduce((acc, n) => acc + n, 0);
}

// Function type expression — like a Java functional interface signature
let calculator: (a: number, b: number) => number;
calculator = (a, b) => a + b;

// Arrow function with types
const multiply = (a: number, b: number): number => a * b;

// void return type — same idea as Java's void
function logMessage(msg: string): void {
  console.log(msg);
}
```

---

## 8. Generics in TypeScript (with Example)

Generics let you write reusable, type-safe code without committing to a specific type upfront — conceptually the same as Java's `List<T>` or `Map<K, V>`.

```typescript
// Generic function
function identity<T>(value: T): T {
  return value;
}

identity<string>("Hello");
identity<number>(42);

// Generic interface
interface ApiResponse<T> {
  data: T;
  status: number;
}

const userResponse: ApiResponse<{ name: string }> = {
  data: { name: "Revanna" },
  status: 200,
};

// Generic class
class Box<T> {
  private content: T;
  constructor(value: T) {
    this.content = value;
  }
  getContent(): T {
    return this.content;
  }
}

const numberBox = new Box<number>(100);

// Generic constraint — T must have a 'length' property
function printLength<T extends { length: number }>(item: T): void {
  console.log(item.length);
}
printLength("hello"); // ✅ strings have length
printLength([1, 2, 3]); // ✅ arrays have length
```

You'll see this constantly in Angular — `HttpClient.get<User[]>(url)` uses generics to tell you exactly what shape of data comes back from an API call.

---

## 9. Spread Operator in TypeScript

The `...` spread operator expands an array, object, or iterable into its individual elements.

```typescript
// Array spread — combine or clone arrays
const arr1: number[] = [1, 2, 3];
const arr2: number[] = [4, 5, 6];
const combined: number[] = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// Object spread — clone/merge objects (common for immutable state updates in Angular)
const user = { name: "Revanna", role: "Developer" };
const updatedUser = { ...user, role: "Senior Developer" };
// { name: "Revanna", role: "Senior Developer" }

// Spread in function calls
function add3(a: number, b: number, c: number): number {
  return a + b + c;
}
const nums: [number, number, number] = [1, 2, 3];
add3(...nums); // 6
```

Note: spread _expands_ values out; rest parameters (`...nums: number[]` in a function signature) _collect_ values in — opposite directions of the same syntax.

---

## 10. Classes and Interfaces in TypeScript

TypeScript classes support access modifiers almost identically to Java: `public`, `private`, `protected`, and `readonly` (≈ Java's `final` for fields).

```typescript
interface Vehicle {
  brand: string;
  start(): void;
}

class Car implements Vehicle {
  brand: string;
  private mileage: number = 0;
  readonly registrationNumber: string;

  constructor(brand: string, registrationNumber: string) {
    this.brand = brand;
    this.registrationNumber = registrationNumber;
  }

  start(): void {
    console.log(`${this.brand} is starting...`);
  }

  protected addMileage(km: number): void {
    this.mileage += km;
  }
}

// Constructor parameter shorthand — very common in Angular services/components
class Employee {
  constructor(
    public name: string,
    private salary: number,
    readonly employeeId: string,
  ) {}
}

// Inheritance
class ElectricCar extends Car {
  batteryCapacity: number;
  constructor(brand: string, reg: string, battery: number) {
    super(brand, reg);
    this.batteryCapacity = battery;
  }
}
```

Just like Java: `implements` for interfaces (a class can implement multiple), `extends` for inheriting from a single parent class. In Angular, every component and service you write is a TypeScript class, usually decorated with `@Component` or `@Injectable`.

---

## 11. Configuring the TypeScript Compiler

TypeScript is configured via a `tsconfig.json` file at the project root. You can generate one with `tsc --init`, though Angular CLI creates and manages this automatically when you scaffold a project (`tsconfig.json`, plus `tsconfig.app.json` and `tsconfig.spec.json`).

```jsonc
{
  "compilerOptions": {
    "target": "ES2022", // JS version the code compiles down to
    "module": "ES2022", // module system used in output
    "lib": ["ES2022", "dom"], // built-in type definitions available
    "strict": true, // enables all strict type-checking rules
    "outDir": "./dist", // where compiled JS files go
    "rootDir": "./src", // where source .ts files live
    "esModuleInterop": true, // smoother interop with CommonJS modules
    "sourceMap": true, // generates .map files for debugging
    "declaration": true, // generates .d.ts type declaration files
    "skipLibCheck": true, // skips type-checking .d.ts files (faster builds)
    "noImplicitAny": true, // errors if a variable's type can't be inferred
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"],
}
```

Compile with `tsc` (uses `tsconfig.json` automatically), or `tsc --watch` to recompile on every save. The `strict` flag is worth understanding well before Angular — it also governs how strictly your component templates get type-checked.
