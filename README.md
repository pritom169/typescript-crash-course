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