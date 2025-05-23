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