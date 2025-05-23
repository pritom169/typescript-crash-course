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