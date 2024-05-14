

## Typescript's typelevel building blocks

Type-level programming in TypeScript unlocks a realm of possibilities, leveraging the language's powerful type system to perform complex computations, validations, and transformations directly within the type system itself. By harnessing features such as generics, conditional types, mapped types, and more, developers can create sophisticated type-level abstractions that enforce constraints, ensure type safety, and enhance code correctness. What's truly remarkable is how TypeScript's type system approaches "Turing completeness," a concept from computability theory suggesting that a system can perform any computation that a Turing machine can. While TypeScript's type system isn't fully Turing complete, its expressive capabilities come remarkably close, allowing for the implementation of advanced type-level patterns and techniques. From modeling domain-specific constraints to building type-safe libraries and frameworks, type-level programming in TypeScript empowers developers to write more robust, reliable, and maintainable code, pushing the boundaries of what's possible within a statically-typed language.

Type-level programming in TypeScript involves using <i>types as values</i> and manipulating them to achieve various tasks at compile time. Here's an exhaustive list of elements and techniques commonly used in type-level programming in TypeScript:

## **Basic Types**
TypeScript's built-in primitive types such as `number`, `string`, `boolean`, `null`, `undefined`, `object`, `symbol`, `bigint`.

## **Type Aliases**
Using the `type` keyword to create aliases for existing types, allowing for easier readability and reuse.

```ts
// Define a type alias for a tuple representing a 2D point
type Point = [number, number];

// Define a function that calculates the distance between two points
function calculateDistance(point1: Point, point2: Point): number {
    const [x1, y1] = point1;
    const [x2, y2] = point2;
    const dx = x2 - x1;
    const dy = y2 - y1;
    return Math.sqrt(dx * dx + dy * dy);
}

// Usage example
const pointA: Point = [0, 0];
const pointB: Point = [3, 4];
const distance = calculateDistance(pointA, pointB);
console.log(distance); // Output: 5

```

## **Interfaces**
Defining structural types representing object shapes.

```ts
// Define an interface representing a user with a minimum age
interface User {
    name: string;
    age: number;
}

// Define a type-level constraint to ensure a user's age is at least 18
type AdultUser<T extends User> = T['age'] extends number ? (T['age'] extends infer Age ? Age extends number ? Age extends number & (Age >= 18) ? T : never : never : never : never);

// Define a function to create an adult user
function createAdultUser<T extends User>(user: AdultUser<T>): AdultUser<T> {
    return user;
}

// Usage example
const validUser = createAdultUser({ name: 'John', age: 25 }); // This is valid
const invalidUser = createAdultUser({ name: 'Alice', age: 17 }); // This will cause a type error

```

## **Unions and Intersections**
Combining multiple types into one using `|` for union types and `&` for intersection types.

```ts
// Define two types
type Square = {
    sideLength: number;
};

type Circle = {
    radius: number;
};

// Create a union type
type Shape = Square | Circle;

// Usage
const square: Shape = {
    sideLength: 5
};

const circle: Shape = {
    radius: 3
};

```


## **Type Guards**
Using type predicates (e.g., `value is SomeType`) to refine types within conditional statements.

```ts
// You can use the `typeof` operator to check the type of a variable at runtime.
function isNumber(x: any): x is number {
    return typeof x === "number";
}

const value: number | string = 42;

if (isNumber(value)) {
    // Within this block, TypeScript knows that 'value' is of type 'number'
    console.log(value.toFixed(2));
}

// The `instanceof` operator is used to check whether an object is an instance // of a specific class or constructor function.
class Dog {
    breed: string;
    constructor(breed: string) {
        this.breed = breed;
    }
}

function isDog(x: any): x is Dog {
    return x instanceof Dog;
}

const pet: Dog | Cat = new Dog("Labrador");

if (isDog(pet)) {
    // Within this block, TypeScript knows that 'pet' is of type 'Dog'
    console.log(pet.breed);
}

```

## **Generics**
Writing reusable code that operates on different types by parameterizing them.
```ts
type Keys<T> = keyof T; // <---- <T>

// Usage
interface Person {
    name: string;
    age: number;
}

type PersonKeys = Keys<Person>; // PersonKeys is "name" | "age"

```

## **Mapped Types** 
Transforming existing types into new types using mapped types like `Partial`, `Required`, `Readonly`, etc.

Mapped types in TypeScript are a powerful feature that allows you to create new types by transforming the properties of an existing type. Mapped types are especially useful when you need to create new types based on the structure of existing ones, such as transforming all properties to be optional or readonly.

The syntax for mapped types involves iterating over the keys of an existing type and applying transformations to create a new type.

(mapped types and index types are syntactically similar but always remember that mapped types have the keyword 'in' in it)

Here's a basic example of a mapped type:

```typescript
type Person = {
    name: string;
    age: number;
};

// Create a new type where all properties are optional
type PartialPerson = {
    [Key in keyof Person]?: Person[Key]; // <<-- beware of the keyword 'in'
};

// Usage
const partialPerson: PartialPerson = {}; // Valid: All properties are optional
```

In this example:
- `keyof Person` retrieves the keys of the `Person` type, which are `"name"` and `"age"`.
- `[Key in keyof Person]` iterates over each key.
- `Person[Key]` accesses the type of each property.

Mapped types support various transformations, including:

1. Making properties optional:
   ```typescript
   type PartialPerson = {
       [Key in keyof Person]?: Person[Key];
   };
   ```

2. Making properties readonly:
   ```typescript
   type ReadonlyPerson = {
       readonly [Key in keyof Person]: Person[Key];
   };
   ```

3. Making properties nullable:
   ```typescript
   type NullablePerson = {
       [Key in keyof Person]: Person[Key] | null;
   };
   ```

4. Removing properties:
   ```typescript
   type AgelessPerson = {
       [Key in keyof Person as Exclude<Key, "age">]: Person[Key];
   };
   ```

Mapped types are highly versatile and can be used to create new types based on complex transformations of existing types, providing a flexible way to define type variations in TypeScript.


## **Conditional Types** 
Conditional expressions within types to determine the resulting type based on a condition.

```ts
type TypeName<T> =
  T extends string ? "string" :
  T extends number ? "number" :
  T extends boolean ? "boolean" :
  T extends undefined ? "undefined" :
  T extends Function ? "function" :
  "object";

// Examples
type T0 = TypeName<string>;  // "string"
type T1 = TypeName<"hello">; // "string"
type T2 = TypeName<42>;      // "number"
type T3 = TypeName<true>;    // "boolean"
type T4 = TypeName<undefined>; // "undefined"
type T5 = TypeName<() => void>; // "function"
type T6 = TypeName<string[]>; // "object"

```


## **Recursive Types**
Defining types that reference themselves, enabling the creation of recursive data structures.
```ts
interface TreeNode<T> {
    value: T;
    left?: TreeNode<T>;
    right?: TreeNode<T>;
}

// Usage
const tree: TreeNode<number> = {
    value: 1,
    left: {
        value: 2,
        left: {
            value: 4
        },
        right: {
            value: 5
        }
    },
    right: {
        value: 3
    }
};

```

## **Index Types**
Accessing and manipulating properties of objects using index types (`keyof`, `[]`).
(mapped types and index types are syntactically similar but always remember that mapped types have the keyword 'in' in it).

```ts
//String index signatures allow you to define a type for accessing properties by // string keys on an object.
interface Dictionary<T> {
    [key: string]: T;
}

// Usage
const dict: Dictionary<number> = {
    "a": 1,
    "b": 2,
    "c": 3
};

console.log(dict["a"]); // Output: 1


// Numeric index signatures allow you to define a type for accessing elements by // numeric keys on an array-like object.
interface NumericArray {
    [index: number]: number;
}

// Usage
const arr: NumericArray = [1, 2, 3];

console.log(arr[0]); // Output: 1


```


## **Type Assertions**
Type assertion in TypeScript is a way to tell the compiler to treat a value as a specific type, regardless of its inferred type.
```ts
// Angle-bracket Syntax:
let someValue: any = "hello";
let strLength: number = (<string>someValue).length;

// As-Keyword Syntax:
let someValue: any = "hello";
let strLength: number = (someValue as string).length;
```

## **Template Literal Types**
They allow you to create new types by concatenating or transforming string literal types using template string syntax.

With template literal types, you can perform string manipulations at the type level, enabling you to create more expressive and precise type definitions.
```ts
type Greeting = "Hello, " | "Hi, ";
type Name = "Alice" | "Bob";

type PersonalizedGreeting = `${Greeting}${Name}`; // "Hello, Alice" | "Hello, Bob" | "Hi, Alice" | "Hi, Bob"

```

## **Discriminated Unions** 
Using a common discriminant property to differentiate between members of a union type.
```ts
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}

interface Circle {
    kind: "circle";
    radius: number;
}

type Shape = Square | Rectangle | Circle;

function area(shape: Shape): number {
    switch (shape.kind) {
        case "square":
            return shape.size * shape.size;
        case "rectangle":
            return shape.width * shape.height;
        case "circle":
            return Math.PI * shape.radius ** 2;
    }
}

```

## **Type Queries**
Inspecting and extracting information about types using type queries (e.g., `typeof`, `keyof`, `infer`).

Type queries in TypeScript are expressions that refer to types defined in the codebase. They allow you to extract the type of a variable, property, or function parameter as a type literal.

Type queries are useful in situations where you need to obtain the type of a specific entity dynamically, especially when working with complex or generic types.

Here's how you can use type queries:

- **Variable Type Query:**
   You can use type queries to refer to the type of a variable.

```typescript
let x = { a: 1, b: "hello" };

// Type of 'x' is inferred as { a: number, b: string }
type XType = typeof x;
```

 - **Property Type Query:**
   Type queries can be used to refer to the type of a property in an object.

```typescript
type Person = {
    name: string;
    age: number;
};

// Type of 'name' is inferred as string
type NameType = Person["name"];
```

 - **Function Parameter Type Query:**
   You can use type queries to refer to the type of a function parameter.

```typescript
function greet(name: string) {
    console.log("Hello, " + name);
}

// Type of 'name' parameter in 'greet' function is inferred as string
type NameType = Parameters<typeof greet>[0];
```

- **Type Query with Classes and Interfaces:**
   Type queries can also refer to the types defined within classes and interfaces.

```typescript
interface Circle {
    radius: number;
    area(): number;
}

// Type of 'area' method in 'Circle' interface
type AreaFunction = Circle["area"];
```

- **Type Query with Infer**
  Type queries are evaluated statically by the TypeScript compiler, meaning they are resolved during the compilation process and do not have any runtime impact. They provide a powerful mechanism for extracting and reusing type information within your codebase.

```ts
type ArrayElementType<T> = T extends (infer U)[] ? U : never;

// Example usage
type StringArray = Array<string>;
type NumberArray = Array<number>;

type ElementTypeOfStringArray = ArrayElementType<StringArray>; // ElementTypeOfStringArray is inferred as 'string'
type ElementTypeOfNumberArray = ArrayElementType<NumberArray>; // ElementTypeOfNumberArray is inferred as 'number'

/*
In this example, the `ArrayElementType` conditional type takes a generic type `T`. It checks whether `T` extends an array type `(infer U)[]`. If it does, it assigns the type of the array elements to the type variable `U` using `infer`. Otherwise, it returns `never`.
*/
```

- **Type Query with keyof**
  The `keyof` keyword in TypeScript is a powerful operator that is used to produce a union type of all known, enumerable property keys of an object type. It allows you to extract the keys of an object as string or numeric literals, creating a union of these keys that you can then use in various contexts, such as indexing or creating new types.
  
```ts
interface Person {
    name: string;
    age: number;
    address: string;
}

type PersonKeys = keyof Person;

// PersonKeys is now "name" | "age" | "address"

```


## **Phantom Types**
Phantom types are a type-level programming technique used to enforce certain constraints or invariants at compile-time. They involve using type parameters that don't directly participate in runtime behaviour but are used solely for type checking purposes. Phantom types are often used to track information or states within the type system.
```ts
// Phantom type representing a status
interface Status<T> {}

// Phantom type representing "active" status
interface Active extends Status<"active"> {}

// Phantom type representing "inactive" status
interface Inactive extends Status<"inactive"> {}

// User type with a phantom type parameter representing status
interface User<StatusType extends Status<any>> {
    id: number;
    name: string;
    status: StatusType;
}

// Function to activate a user
function activateUser(user: User<Inactive>): User<Active> {
    return { ...user, status: {} as Active };
}

// Function to deactivate a user
function deactivateUser(user: User<Active>): User<Inactive> {
    return { ...user, status: {} as Inactive };
}

// Example usage
const inactiveUser: User<Inactive> = { id: 1, name: "John", status: {} as Inactive };
const activeUser: User<Active> = activateUser(inactiveUser);
const deactivatedUser: User<Inactive> = deactivateUser(activeUser);

```


These elements provide a powerful foundation for type-level programming in TypeScript, enabling developers to express and enforce complex constraints, design robust APIs, and achieve safer and more maintainable codebases.

## Example I:  typelevel addition
```ts
// Phantom type representing a number
interface Num<T extends number> {}

// Type-level addition function
type Add<A extends number, B extends number> =
    A extends 0
    ? B
    : Add<
        Num<A extends infer N ? N extends number ? N - 1 : never : never>,
        Num<B extends infer N ? N extends number ? N + 1 : never : never>
      >;

// Example usage
type Result = Add<2, 3>; // Result is 5

```

- We define a phantom type `Num<T>` to represent a number.
- We define a type-level addition function `Add<A, B>`. It's a recursive function that subtracts 1 from the first number (`A`) and adds 1 to the second number (`B`) until the first number becomes 0. This is done using conditional types and recursion.
- The result of the addition is represented as the type of the phantom number after all recursive operations have been completed.


