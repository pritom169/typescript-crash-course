# What is TypeScript?
TypeScript is a language built on top of Javascript with additional functionalities as
- Static typing
- Code completion
- Refactoring
- Shorthand notations.

The most important feature of TypeScript is `statically typed`. For example, a languages like
C++, C#, Java are statically typed language. Means it should be written such as

```typescript
int number = 10;
number = "a"; // Ah! Can not happen! Can not simply assign a String to an integer.
```
However, JavaScript is not a statically typed language
```javascript
let number = 10;
number = "a";
Math.round(number);
```

This would create an error in runtime until and unless we have created an Unit Test to test it.

It comes with a drawback. Till now the Browser does not understand TypeScript, hence we have to
take in the compilation time into consideration. The TS code will be first compiled and than will
be translated into JS. This process is called transpilation.

## First TS Code
Let's write some code into TS file. Name the file as index.ts

```typescript
let age: number = 20;
```

We can run this file using `tsc index.ts` in terminal and it will generate a file named `index.js`. 
The content of the file will be 

```javascript
var age = 20;
```

## Configuring TS file
We can simply create `tsconfig.json` file using the terminal command `tsc --init`. When we go inside
`tsconfig.json` file we will see many settings. Some of the settings that needs to be changed are
```json
{
  "target": "es2016", // es2016 is supported by most of the modern browser
  "module": "commonjs", // Let's keep this as it is
  "outDir": "./dist", // all the generated JS files will be stored in the `dist` directory
  "noEmitOnError": true // If there are some error in the TS file, no JS code will be generated
}
```
We can simply run the configured `tsconfig.json` file using `tsc` command

## Debugging TS code
If we want to debug a TS code we need to make some adjustments.

- We have to go the `tsconfig.json` and uncomment `"sourceMap": true`.
- We have to go to the launch.json file in VSCode and add the line
`"preLaunchTask": "tsc: build - resources/tsconfig.json"`

# Foundations
## Built in Types
JS has the following primitive types:
- number
- string
- boolean
- null
- undefined
- object

TS extends the primitive types even more:
- any
- unknown
- never
- enum
- tuple

In TS we can type
```typescript
let sales: number = 123_456_789;
let course: string = 'TypeScript';
let is_published: boolean = true;
```

However, we can remove the types using
```typescript
let sales = 123_456_789;
let course = 'TypeScript';
let is_published = true;
```
In this case, TS automatically infers the type as number, string and boolean.

## Any
```typescript
let level;
level = 1;
level = 'a';
```

If we look at the code, when we declared variable `level` we see it has been
assigned `Any` type. Then we have assigned `1` and `a` to it which introduces type conflict.
However, the whole purpose of TypeScript is to maintain type safety, and hence we should not
write such codes as best practise.

## Array
```javascript
let numbers = [1, 2, "Ball"];
```

In JS in a same array there could be elements with multiple primitive types.

```typescript
let numbers: number = [1, 2, 3];
let numbers1: number = [1, 2, "Ball"]; // It will show an error as the last element is not number
let numbers2 = [1, 2, 3]; // The TS compiler will assume that it's a number array

// The following written code is also valid but not a good practise as it's not type safe
let numbers3 = [];
numbers2[0] = 1;
numbers2[1] = '1';

// In TS if we want to declare an empty array, it is a best practice to declare with a variable
// type. 
let numbers4 : number[] = [];
```

## Tuples
```typescript
let user: [number, string] = [1, 'Pritom'];

// For typesafety one can not assign a different type of variable other than
// number and string respectively.

// If we compile the TS we will see under the hood TS are essentially JS arrays.

// One of the gaps of TS arrays are push in Tuples. 
user.push(1);

// The best practice for tuples is to restrict them upto two values.
```

## Enums
```typescript
//PascalCase
enum Size {Small, Medium, Large};   // Under the hood typescript automatically assigns 1,2,3 to
let mySize: Size = Size.Medium;     // Small, Medium and Large respectively.
console.log(mySize);

// If we want to assign custom values, we can simply do it by
enum Size1 {Small = 5, Medium = 6, Large = 7};
```

Now let's convert the first block of TS code to JS code by going to Terminal by typing

```shell
tsc
tsc node dist/index.js
```

If we do that, we will see the following code being generated
```javascript
var Size;
(function (Size) {
    Size[Size["Small"] = 1] = "Small";
    Size[Size["Medium"] = 2] = "Medium";
    Size[Size["Large"] = 3] = "Large";
})(Size || (Size = {}));
;
let mySize = Size.Medium;
console.log(mySize);
```

The generated JS code is quite lengthy and verbose but we don't have to worry about it for the
time being. One quick fix we can implement is by adding `const`. Hence, the final TS code will be
```typescript
const enum Size {Small, Medium, Large};
let mySize: Size = Size.Medium;
console.log(mySize);
```

Now the generated code will be much more simpler
```javascript
"use strict";
let mySize = 2;
console.log(mySize);
```

## Function
Let's write some function code.
```typescript
function calculateTax(income: number) {
    
}
```

The function calculate does not have any return value. Now let's return some value
```typescript
function calculateTax(income: number) {
    return 2;
}
```

If we see in the VS code, TS compiler inferred the return type as number. However,
the best practice is to always annotate the return types. It has the following benefits
- If we are designing an API, it becomes much better a team member about which value
it should return.
- In addition to that, if we return wrong value type, `string` instead of `number`, it will
show an error sign.

Here is a full demonstration how a function with parameters should be written.
```typescript
function calculateTax(income: number): number {
    return 2;
}
```

However, there is one issue. The issue is we are not using `income` variable but there is
no error sign given. In oder to resolve it we have to go to tsconfig.json file and explicitly
turn on the settings `"noUnusedParameters": true`. Now let's go into another direction.

```typescript
function calculateTax(income: number): number {
    let x;
    if (income < 50_000)
        return income * 1.2;
}
```

There is one error in the function mentioned above, if the income is less than 50000, then we
it will return undefined value. In order our TS compiler to detect the error before running
the TS code we have to explicitly uncomment the line `"noImplicitReturns": true,`

```typescript
function calculateTax(income: number): number {
    let x;
    if (income < 50_000)
        return income * 1.2;
     return income * 1.3;
}
```

As we can see, inside the function locally x has been initiated, but it has never been used. We also
don't see any error warning sign till now. In order to resolve this, we  have to explicitly
uncomment the line in `"noUnusedLocals": true`.

Let's go one step further.

```typescript
function calculateTax(income: number, taxYear: number): number {
    if (taxYear < 2022)
        return income * 1.2
    return income * 1.3 
}

calculateTax(40000, 2022)
calculateTax(40000, 2022, "Hello There")
```

In JS the last line would be valid, but in TS it would not be a valid function call as it will
say the function is asking for two parameter, and it is giving three.
 
```typescript
function calculateTax(income: number, taxYear?: number): number {
    if ((taxYear || 2022) < 2022)
        return income * 1.2
    return income * 1.3 
}

calculateTax(40000)
```

Now we can just pass the income not the taxYear. We can do that by making taxYear optional.
Since taxYear is optional and we also have to take care the option type if in the if checking by
`if ((taxYear || 2022) < 2022)`.

```typescript
function calculateTax(income: number, taxYear = 2022): number {
    if (taxYear < 2022)
        return income * 1.2
    return income * 1.3 
}

calculateTax(40000)
```

We can simply remove all the hassles just by giving a default value in the parameter for year.

## Objects
```javascript
let employee = { id: 1 };
employee.name = 'Mosh';
```

The above code is completely valid in JS however that will show error in TS as it will give an error
saying the `Property 'name' does not exist on type '{ id: number; }'.`

A very simple JS object looks like this one
```typescript
let employee: {
    id: number,
    name: string,
} = { id: 1, name: "Pritom" };
```

There is a problem with this object, I can change the id which should not be the case. So let's make the id
read only
```typescript
let employee: {
    readonly id: number,
    name: string,
} = { id: 1, name: "Pritom"}
```

Now let's say every employee object should have a default function. Inside the object the structure of function
needs to be there.

```typescript
let employee: {
    readonly id: number,
    name: String,
    retire: (date: Date) => void
} = { 
    id: 1, 
    name: "Pritom",
    retire: (date: Date) => {
        console.log(date)
    }
}
```

# Advanced Types in TypeScript
## Generics
There are many problems with the above object
- If we want to create another employee object we have to recreate the whole shape
- The other employee can have different properties which is not possible to support via this shape
- Last but not the least, the whole structure of the code makes it look verbose

The whole code will change be changed into something like this

```typescript
type Employee = {
    readonly id: number,
    name: string,
    retire: (date: Date) => void
}

let employee: Employee = { 
    id: 1, 
    name: "Pritom",
    retire: (date: Date) => {
        console.log(date)
    }
}
```

## Union Types
When we use Union type we can use two variables. An examples of Union type is given 

```typescript
function kgToLbs(weight: number | string): number {
    //Narrowing
    if (typeof weight === 'number'){
        return weight * 2.2;
    } else {
        return parseInt(weight) * 2.2;
    }
}

kgToLbs(10);
kgToLbs('10kg');
```

## Intersection Types
In an intersection type a variable can be both types at the same time. When a variable is an Intersection of
two types it should implement two types.

```typescript
type Draggable = {
    drag: () => void
}

type Resizable = {
    resize: () => void
}

type UIWidget = Draggable & Resizable;

let textBox: UIWidget = {
    drag: () => {},
    resize: () => {}
}
```

## Literal types
Literal types limits the choice of what we can assign to a variable. For example a `number` can only be 50 or 100.
The code will clear all the confusion

```typescript
type Quantity = 50 | 100;
let quantity: Quantity = 100;

type Metric = 'cm' | 'inch';
```

## Nullable Types
Let look at this JS code.

```javascript
function greet(name: string){
    console.log(name.toUpperCase())
}

greet(null);
```

The JS code is valid. However, there is one problem. When we pass null, the `.toUpperCase()` becomes invalid.
That will return exception. As a result, TS compiler by default stops us from using null or undefined values.

```typescript
function greet(name: string | null | undefined) {
    if (name)
        console.log(name.toUpperCase())
    else
        console.log('Hola!')
}
```

In order to pass a `null` or `undefined` to the function we have to use the Union operator

## Optional Chaining
Let's look at this TS code.
```typescript
type Customer = {
    birthday: Date
};

// Let's write a function that will take 
function getCustomer(id: number): Customer | null {
    return (id === 0) ? null : {birthday : new Date()};
}

let customer = getCustomer(0);
console.log(customer.birthday)
```

It shows error and underlines the customer. As customer can be null hence it is showing an error. However, it can 
be resolved.

```typescript
if (customer !== null)
    console.log(customer.birthday)
```

Now let's go one step further.
```typescript
function getCustomer(id: number): Customer | null | undefined {
    return (id === 0) ? null : {birthday : new Date()};
}

let customer = getCustomer(0)
if (customer !== null && customer !== undefined)
    console.log(customer.birthday)
```

However, we can do better.

```typescript
function getCustomer(id: number): Customer | null | undefined {
    return (id === 0) ? null : {birthday : new Date()};
}

let customer = getCustomer(0)
// Optional property access operator
console.log(customer?.birthday)
```

## Nullish Coalescing Operator
When we are calling an API, it can return no value or the value we are not expecting.

```typescript
function greetUser(name?: string) :string {
    const displayName : string = name ?? "Guest";
    return `Hello, ${displayName}!`;
}
```

## Type Assertions
Type assertion allows the developer to treat a value as a specific when when the developer knows more about
the type than TS can infer. There are two ways to do Type assertions: `<>` or `as`

```typescript
const canvas = document.getElementById('myCanvas') as HTMLCanvasElement;

// Be careful what you assert as assertions can be dangerous!
```

## The unknown Type
`unknown` is the type-safe counterpart to `any`. It represents any value but requires type checking before you
can do anything with it. It's the top type in TypeScript's hierarchy.

```typescript
let userInput: unknown;

userInput = 5;
userInput = "hello";
userInput = true;

// console.log(userInput.toUpperCase());

function processValue(value: unknown){
    if (typeof value == "number"){
        return value * 2
    };

    if (value instanceof Date) {
        return value.getTime();
    }

    return "Unknown type";
}
```

## never type
`never` represents values that never occur. It's the bottom type in TS's type hierarchy and used for functions
that never return normally (throws errors or infinite loops) and for exhaustive type checking

```typescript
// Functions that never return
function throwError(message: string): never {
  throw new Error(message);
  // No return statement - function never completes normally
}

function infiniteLoop(): never {
  while (true) {
    console.log("This runs forever");
  }
}

// Exhaustive checking with never
type Shape = "circle" | "square" | "triangle";

function getArea(shape: Shape): number {
  switch (shape) {
    case "circle":
      return Math.PI * 5 * 5;
    case "square":
      return 5 * 5;
    case "triangle":
      return (5 * 5) / 2;
    default:
      // If we add a new shape type and forget to handle it,
      // TypeScript will error here
      const exhaustiveCheck: never = shape;
      throw new Error(`Unhandled shape: ${exhaustiveCheck}`);
  }
}

// Never in conditional types
type NonNullable<T> = T extends null | undefined ? never : T;

// Variables can't actually be never (except through type narrowing)
function example(x: string | number) {
  if (typeof x === "string") {
    // x is string here
  } else if (typeof x === "number") {
    // x is number here
  } else {
    // x is never here - all possibilities exhausted
    const shouldNeverReach: never = x;
  }
}
```

# Object-Oriented Programming (OOP)
## Definition
OOP is a programming paradigm based on the concept of "objects" which contain:
- Data (properties/attributes)
- Code (methods/functions)

Key principles:
1. Encapsulation - Bundling data and methods together
2. Inheritance - Creating new classes based on existing ones
3. Polymorphism - Same interface, different implementations
4. Abstraction - Hiding complex implementation details

## Classes and Objects

```typescript
class Person {
    name: string;
    age: number;

    // Constructor - Special method called when creating new objects
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    //Method (behavior)
    greet(): string {
        return `Hello, I'm ${this.name} and I'm ${this.age} years old.`;
    }

    //Method that modifies state
    haveBirthday(): void {
        this.age ++;
        console.log(`Happy birthday! ${this.name} is now ${this.age} years old.`);
    }
}

// Creating Objects
const person1 = new Person("Alice", 30); 
const person2 = new Person("Bob", 25); 

// Using the objects
console.log(person1.greet()); // "Hello, I'm Alice and I'm 30 years old.
console.log(person2.greet()); // "Hello, I'm Bob and I'm 25 years old.

person1.haveBirthday(); // "Hello, I'm Bob and I'm 25 years old.
```

**Key Points:**
- Classes define the structure; objects are instances with actual data
- The `constructor` is called automatically when using `new`
- `this` keyword refers to the current instance
- Each object has its own copy of the properties but shares the methods

## Read-only and Optional Properties
- The `readonly` modifier prevents a property from being changed after it's initialized. This is 
useful for properties that should remain constant throughout an object's lifetime.
- The `?` modifier makes a property optional, meaning it may or may not be present 
when creating an object.

```typescript
class Car {
    readonly brand: string;        // Cannot be changed after initialization
    readonly vin: string;          // Vehicle Identification Number - should never change
    model: string;                 // Can be changed
    year?: number;                 // Optional - might not be specified
    color?: string;                // Optional property
    
    constructor(brand: string, vin: string, model: string, year?: number, color?: string) {
        this.brand = brand;
        this.vin = vin;
        this.model = model;
        this.year = year;    // Could be undefined
        this.color = color;  // Could be undefined
    }
    
    // Method that can modify mutable properties
    updateModel(newModel: string): void {
        this.model = newModel;     // ✓ OK - not readonly
    }
    
    // Method that attempts to modify readonly property
    // changeBrand(newBrand: string): void {
        // this.brand = newBrand;  // ✗ Error: Cannot assign to 'brand' because it is read-only
    // }
    
    // Method to safely access optional properties
    getCarInfo(): string {
        let info = `${this.brand} ${this.model}`;
        
        // Check if optional properties exist before using them
        if (this.year !== undefined) {
            info += ` (${this.year})`;
        }
        
        if (this.color) {  // Truthy check works for optional properties
            info += ` in ${this.color}`;
        }
        
        return info;
    }
}

// Creating instances with different combinations of optional properties
const car1 = new Car("Toyota", "1234567890", "Camry", 2023, "Blue");
const car2 = new Car("Honda", "0987654321", "Civic"); // No year or color
const car3 = new Car("Ford", "1122334455", "Mustang", 2022); // No color

console.log(car1.getCarInfo()); // "Toyota Camry (2023) in Blue"
console.log(car2.getCarInfo()); // "Honda Civic"
console.log(car3.getCarInfo()); // "Ford Mustang (2022)"

// This would cause a compile-time error:
// car1.brand = "Lexus"; // Error: Cannot assign to 'brand' because it is read-only
```

**Key Points:**
- `readonly` properties can only be assigned in the constructor or at declaration
- Optional properties might be `undefined`, so always check before using
- Read-only ensures data integrity for properties that shouldn't change
- Optional properties provide flexibility in object creation


### Access Control Keywords
TypeScript provides three access modifiers that control the visibility of class members:

#### Public (Default)
- Accessible from anywhere - inside the class, outside the class, and in subclasses
- If no modifier is specified, the member is public by default

#### Private
- Only accessible within the same class where it's defined
- Not accessible from outside the class or even in subclasses
- Helps with encapsulation - hiding internal implementation details

#### Protected
- Accessible within the class and its subclasses
- Not accessible from outside the class hierarchy
- Useful for sharing functionality between parent and child classes while keeping it hidden from external code

```typescript
class BankAccount {
    public accountNumber: string;      // Anyone can access
    private balance: number;           // Only this class can access
    protected bankName: string;        // This class and subclasses can access
    private transactionHistory: string[] = []; // Private array
    
    constructor(accountNumber: string, initialBalance: number, bankName: string) {
        this.accountNumber = accountNumber;  // Public - OK to access anywhere
        this.balance = initialBalance;       // Private - only accessible here
        this.bankName = bankName;           // Protected - accessible in subclasses too
    }
    
    // Public method - anyone can call this
    public deposit(amount: number): void {
        if (amount > 0) {
            this.balance += amount;  // Can access private property within the class
            this.recordTransaction(`Deposited $${amount}`);
        }
    }
    
    // Public method for withdrawal
    public withdraw(amount: number): boolean {
        if (amount > 0 && amount <= this.balance) {
            this.balance -= amount;
            this.recordTransaction(`Withdrew $${amount}`);
            return true;
        }
        return false;
    }
    
    // Private method - only this class can call it
    private recordTransaction(transaction: string): void {
        const timestamp = new Date().toISOString();
        this.transactionHistory.push(`${timestamp}: ${transaction}`);
    }
    
    // Private method for internal calculations
    private calculateInterest(): number {
        return this.balance * 0.02; // 2% interest
    }
    
    // Protected method - subclasses can access this
    protected getBalance(): number {
        return this.balance;
    }
    
    // Public method that uses private methods internally
    public getBalanceWithInterest(): number {
        const interest = this.calculateInterest(); // Can call private method
        return this.balance + interest;
    }
    
    // Public method to get account summary
    public getAccountSummary(): string {
        return `Account: ${this.accountNumber}, Balance: $${this.balance}, Bank: ${this.bankName}`;
    }
}

// Usage examples
const account = new BankAccount("123456789", 1000, "MyBank");

// Public members - accessible
console.log(account.accountNumber);  // ✓ "123456789"
account.deposit(500);               // ✓ OK
console.log(account.getAccountSummary()); // ✓ OK

// Private members - not accessible from outside
// console.log(account.balance);           // ✗ Error: Property 'balance' is private
// account.recordTransaction("test");      // ✗ Error: Method is private
// console.log(account.calculateInterest()); // ✗ Error: Method is private

// Protected members - not accessible from outside (only in subclasses)
// console.log(account.bankName);       // ✗ Error: Property 'bankName' is protected
// console.log(account.getBalance());   // ✗ Error: Method is protected
```

## Parameter Properties
Parameter properties are a TypeScript shorthand that automatically creates and initializes class 
properties from constructor parameters. Instead of declaring properties separately and then assigning 
them in the constructor, you can do both in one step.

```typescript
// Traditional way (verbose)
class StudentTraditional {
    public name: string;
    private studentId: number;
    protected grade: string;
    
    constructor(name: string, studentId: number, grade: string) {
        this.name = name;
        this.studentId = studentId;
        this.grade = grade;
    }
}

// Parameter properties way (concise)
class Student {
    constructor(
        public name: string,           // Creates: public name: string
        private studentId: number,     // Creates: private studentId: number
        protected grade: string,       // Creates: protected grade: string
        public readonly school: string, // Creates: public readonly school: string
        public age?: number           // Creates: public age?: number (optional)
    ) {
        // Constructor body can contain additional logic if needed
        console.log(`New student created: ${this.name}`);
    }
    
    // Methods can access all the parameter properties
    getStudentInfo(): string {
        return `${this.name} (ID: ${this.studentId}, Grade: ${this.grade})`;
    }
    
    // Private property is accessible within the class
    //private getInternalId(): number {
    //    return this.studentId;
    // }
    
    // Method that shows how to handle optional properties
    getFullInfo(): string {
        let info = this.getStudentInfo() + ` at ${this.school}`;
        if (this.age !== undefined) {
            info += `, Age: ${this.age}`;
        }
        return info;
    }
}

// Creating instances
const student1 = new Student("Alice", 12345, "A", "MIT", 20);
const student2 = new Student("Bob", 67890, "B", "Harvard"); // No age specified

// Accessing public properties
console.log(student1.name);     // ✓ "Alice"
console.log(student1.school);   // ✓ "MIT"

// Cannot access private/protected properties
// console.log(student1.studentId); // ✗ Error: Property 'studentId' is private
// console.log(student1.grade);     // ✗ Error: Property 'grade' is protected

// Cannot modify readonly property
// student1.school = "Stanford";    // ✗ Error: Cannot assign to 'school' because it is read-only

console.log(student1.getFullInfo()); // "Alice (ID: 12345, Grade: A) at MIT, Age: 20"
console.log(student2.getFullInfo()); // "Bob (ID: 67890, Grade: B) at Harvard"

// Mixed approach - parameter properties with additional properties
class Course {
    private enrolledStudents: Student[] = []; // Additional property declared separately
    
    constructor(
        public courseName: string,
        public courseCode: string,
        private maxStudents: number = 30  // Parameter property with default value
    ) {}
    
    enrollStudent(student: Student): boolean {
        if (this.enrolledStudents.length < this.maxStudents) {
            this.enrolledStudents.push(student);
            return true;
        }
        return false;
    }
    
    getCourseInfo(): string {
        return `${this.courseCode}: ${this.courseName} (${this.enrolledStudents.length}/${this.maxStudents})`;
    }
}

const course = new Course("TypeScript Fundamentals", "TS101", 25);
course.enrollStudent(student1);
course.enrollStudent(student2);
console.log(course.getCourseInfo());
```

**Key Points:**
- Parameter properties reduce boilerplate code significantly
- You can mix access modifiers, readonly, and optional modifiers
- The constructor can still contain additional logic
- You can combine parameter properties with traditional property declarations
- Default values can be specified for parameter properties

## Getters and Setters
```typescript
class User {
  private _name = "";

  // called when you read `user.name`
  get name(): string {
    console.log("Getting name");
    return this._name;
  }

  // called when you assign `user.name = "Alice"`
  set name(v: string) {
    console.log("Setting name");
    if (v.length < 2) throw new Error("Name too short");
    this._name = v;
  }
}

const user = new User();
user.name = "Bob";    // “Setting name”
console.log(user.name); // “Getting name” → “Bob”
```

**Key Points:**
- Getters are accessed like properties but can execute code
- Setters allow validation and transformation of incoming values
- Computed properties can derive values from other properties
- Read-only properties can have only getters (no setters)
- Getters and setters provide encapsulation while maintaining a property-like interface

## Index Signatures
Index signatures allow you to define types for properties that you don't know about ahead of 
time. They're useful when you need to create objects with dynamic property names or when working 
with dictionary-like structures.

```typescript
interface StringMap {
  [key: string]: string;
}

const labels: StringMap = {
  foo: "Foo",
  bar: "Bar",
};
labels.baz = "Baz";
```

**Key Points:**
- Index signatures allow dynamic property access with type safety
- You can mix index signatures with explicit properties
- The key type can be `string`, `number`, or `symbol`
- All explicitly declared properties must be compatible with the index signature type
- Useful for dictionaries, configuration objects, and dynamic data structures
- Provides flexibility while maintaining some type safety

## Static Members
Static members belong to the class itself rather than to any instance of the class. 
They can be accessed without creating an object and are shared across all instances. Static 
members are useful for utility functions, constants, and class-level data.

```typescript
class Logger {
  static count = 0;

  constructor() {
    Logger.count++;
  }

  static reset() {
    Logger.count = 0;
  }
}

new Logger();
console.log(Logger.count); // 1
Logger.reset();
```

**Key Points:**
- Static members belong to the class, not instances
- Access using `ClassName.memberName`, not instance references
- Static methods cannot access instance members directly
- Useful for utility functions, constants, counters, and factory patterns
- Static properties are shared across all instances
- Common patterns: Singleton, Factory, Registry, and Utility classes

## Inheritance
Inheritance is a fundamental OOP concept that allows you to create new classes based on existing classes. 
The new class (child/derived/subclass) inherits properties and methods from the existing class 
(parent/base/superclass), and can add its own features or modify inherited behavior.

```typescript
class Animal {
    constructor (public name: string) {}
    move() { console.log(`${this.name} moves`)}
}

class Dog extends Animal {
    bark() {console.log("Woof!")}
}

const d = new Dog("Fido")
d.move()
d.bark()
```

**Key Points:**
- Use `extends` keyword to create inheritance relationships
- Child classes must call `super()` in their constructor before using `this`
- `super.methodName()` calls the parent class method
- Child classes inherit all public and protected members from parent
- Private members are not inherited (not accessible in child classes)
- Inheritance creates an "is-a" relationship (Dog is-a Animal)
- TypeScript supports single inheritance (one parent class per child)

## Method Overriding
Method overriding occurs when a child class provides a specific implementation of a method that 
is already defined in its parent class. The child class method "overrides" the parent class 
method, replacing its behavior while maintaining the same method signature.

```typescript
class Animal {
  speak() { console.log("..."); }
}

class Cat extends Animal {
  override speak() {
    console.log("Meow");
  }
}

new Cat().speak(); // “Meow”
```

### Key Concepts:
- **Same Signature**: Method name, parameters, and return type must match
- **Runtime Polymorphism**: The actual method called depends on the object's type at runtime
- **Super Calls**: Child methods can call parent methods using `super.methodName()`
- **Complete Replacement**: Child method completely replaces parent behavior (unless calling super)

## Polymorphism
`Poly -> Many` and `Morphism -> form`

```typescript
interface Drawable {
    draw(): void
}

class Circle implements Drawable {
    constructor(public radius: number) {}
    draw() {
        console.log(`Drawing a circle of radius ${this.radius}`)
    }
}

class Square implements Drawable {
    constructor(public side:number) {}
    draw() {
        console.log(`Drawing a square of size ${this.side}`)
    }
}

function renderAll(shapes: Drawable[]){
    shapes.forEach(shapes => shapes.draw())
}

const shapes: Drawable[] = [
    new Circle(5),
    new Square(10)
]

renderAll(shapes);
```

## Private vs. Protected
- private: accessible only within the defining class
- protected: accessible in the class and its subclasses

```typescript
class Base {
  private secret = 123;
  protected hint = "peek";

  show() {
    console.log(this.secret); // OK
  }
}

class Sub extends Base {
  demo() {
    // console.log(this.secret); // ❌ private → Error
    console.log(this.hint);      // ✅ protected → OK
  }
}
```

## Abstract Classes and Methods
An abstract class is declared using the abstract keyword and can contain both concrete 
implementations and abstract methods that must be implemented by subclasses. You cannot create 
instances of abstract classes directly - they exist solely to be extended by other classes.

```typescript
abstract class Shape {
    constructor(public color: string) {}

    abstract render(): void
}

class Circle extends Shape {
    constructor(public radius: number, color: string){
        super(color);
    }

    override render(): void {
        console.log('Rendering a circle!')
    }
}

let shape = new Circle(10, 'red');
shape.render();
```

## Interfaces
We have seen some abstract classes. But when we just need to implement some basic variables and
methods, without adding distinct functionality to the class we can use Interfaces.

```typescript
interface Calendar {
    name: string,
    addEvent(): void,
    removeEvent(): void
}

interface CloudCalendar extends Calendar {
    sync(): void;
}

class GoogleCalendar implements Calendar {
    constructor(public name: string){

    }

    addEvent(): void {
        throw new Error("Method not implemented.");
    }
    removeEvent(): void {
        throw new Error("Method not implemented.");
    }
    
}
```