**TypeScript**

# Built-in types

## In functions

```ts
function returnsAString(): string {
	return 'abc';
}
function returnsNothing(stringArg: string): void {
	console.log(stringArg);
}
var arrowFunctionThatReturnsString = (stringArg: string, numberArg: number): string => {
	return `${stringArg} ${numberArg}`;
}
function spreadParameter(...params: string[]) {...}
```

## In arrays
```ts
let names: string[] = ["Dresden", "Bob"];
let names: Array<string> = ["Dresden", "Bob"];
let teams: string[][] = [["Dresden", "Bob"], ["Thomas", "Justine"]]; // multidimensional array
let tuple: [string, number] = ["Level", 7];
```

# Custom types

## Enums

Usesd when you want to **enum**erate all the possible values a variable could have. 

```ts
enum Direction {
	North,
	East,
	South,
	West
}
let SantasDirection: Direction = Direction.North;
```

This is a *numeric enum*--by default, the values are internally stored as numbers, and that's how they'll appear in (for example) `console.log()`. You can manually set the numbers like so:

```ts
enum Direction {
	North = 8,
	East = 6,
	South = 2,
	West = 4
}
```

Or you can set this up as a *string enum* so the values will be stored as strings:

```ts
enum Direction {
	North = 'NORTH',
	East = 'EAST',
	South = 'SOUTH',
	West = 'WEST'
}
```

## Type aliases

```ts
type Coord = [number, string, number, string];
let Dallas: Coord = [32.7767, 'N', 96.7970, 'W'];

type StringsToNumberFunction = (str: string, num: number) => number;
```

## Generics

```ts
type Family<T> = { parents: [T, T], mate: T, children: T[] };
let numberFamily: Family<number> = { parents: [3, 4], mate: 9, children: [5, 30, 121] };
let stringFamily: Family<string> = { parents: ['A', 'B'], mate: 'C', children: ['D', 'E', 'F'] };

type Human = { name: string };
let humanFamily: Family<Human> = {
	parents: [ { name: "Malcolm Dresden" }, { name: "Margaret LeFay" }],
	mate: { name: "Susan Rodriguez" },
	children: [{ name: "Maggie Dresden" }]
}
```

### Generic functions

Type placeholders look like `<this>` and let you pass input into your type definition.

```ts
// This use of <T> ensures that the output array is filled with the same type as the input parameter.
function getFilledArray<T>(input: T, n: number): T[] {
	return Array(n).fill(value);
}
getFilledArray<string>('cheese', 3); // ['cheese', 'cheese', 'cheese']
getFilledArray<boolean>(true, 2); // [true, true]
getFilledArray<{ tasty: boolean }>({ tasty: true }, 2); // [{ tasty: true }, { tasty: true }]
```

## Objects and interfaces

`type` can be used for anything; `interface` is specifically for describing objects, which makes it a good fit for `class`es. `type` is declared with an `=`; `interface` is not.

```ts
interface Person {
	name: string;
	age: number;
	introduce: function;
}

class Student implements Person {...} // Students must have at least a name, an age, and an introduction function
```

Definitions can be nested as deeply as you like:

```ts
interface Robot {
  about: {
    general: {
      id: number;
      name: string;
    };
  };
  getRobotId: () => string;
}
```

They can be composed to make them more readable:

```ts
interface About {
  general: General;
}
 
interface General {
  id: number;
  name: string;
  version: Version;
}
 
interface Version {
  versionNumber: number;
}
```

They can build on ("extend") each other.

```ts
interface Shape {
  color: string;
}
 
interface Square extends Shape {
  sideLength: number;
}
 
const mySquare: Square = { sideLength: 10, color: 'blue' };
```

### Index signatures

Index signatures let you account for variable property names.

```ts
interface SolarEclipse {
	[latitude: string]: boolean;
}

{
  '40.712776': true;
  '41.203323': true;
  '40.417286': false;
}
```

### Optional type members

```ts
interface Shape {
	x: number;
	y: number;
	color?: string; // color is optional
}
```

# Unions

Unions let you declare a variable or parameter to be one of a few different types.

```ts
let ID: string | number; // ID is either a string or a number
function getMargin(margin: string | number) { ... } // function will accept a string or a number
const times: (string | number)[]; // array of strings and/or numbers
```

## Type narrowing

```ts
function doSomething(input: string | boolean) {
	input.toLowerCase(); // TypeScript error. If doSomething gets passed a boolean, the toLowerCase method won't work on it.
}

function doSomething(input: string | boolean) {
	if (typeof input == 'string') { // Error fixed.
		input.toLowerCase();
	}
}
```

The `in` operator checks if a property exists on an object itself or anywhere within its prototype chain.

```ts
type Tennis = {
  serve: () => void;
}
 
type Soccer = {
  kick: () => void;
}
 
function play(sport: Tennis | Soccer) {
  if ('serve' in sport) {
    return sport.serve();
  }
 
  if ('kick' in sport) {
    return sport.kick();
  }
}
```