**C\#**

# Data types

Primitives (`int`, `char`, `float`, `byte`, etc.) and custom `struct`s are **value types**.

- Accessed by value
- Allocated on the stack
- Instantly removed when they go out of scope

Array, strings, and custom classes are **reference types**.

- Accessed by reference
- Allocated on the heap
- Marked for garbage collection when they go out of scope

```csharp
var a = ["a", "b"];
var b = a;
a[0] = "c";
Console.WriteLine(b[0]); // "c"
```

To **convert** from one type to another:

```csharp
var a = 1; // declare an integer
var b = (byte)a; // convert it to a byte
```

For more advanced conversions, use the `Convert` class, which does things like `Convert.ToByte()` and `Convert.ToInt32`.

## Strings

Strings in C# are immutable.

### Combining strings

```csharp
var firstName = "James";
var lastName = "Harris";

var concatFullName = firstName + " " + lastName;
var interpolFullName = string.Format("{0} {1}", firstName, lastName);

var nameArray = new string[2] {firstName, lastName};
var joinedFullName = string.Join(" ", nameArray);
```

### Verbatim strings

Escape characters such as `\` do not apply within verbatim strings.

```csharp
var standardStringPath = "System path: \n C:\\Windows\\System";
var verbatimStringPath = @"System path: 
C:\Windows\System";
```

### String interpolation

```csharp
var firstName = "James";
var lastName = "Harris";
var fullName = $"{firstName} {lastName}";
```

## Arrays

Once declared, arrays in C# are of a fixed size and type. If you don't know how long the array should be, use a list.

```csharp
var emptyArray = new int[3];
var prefilledArray = new[] { 1, 2, 3 };
```

To find an item in an array:

```csharp
var index = Array.IndexOf(arr, 3); // 2
```

### Multidimensional arrays

#### Rectangular arrays

```csharp
var matrix = new int[2, 5]
{
	{ 1, 2, 3, 4, 5 },
	{ 6, 7, 8, 9, 10 }
};
```

#### Jagged arrays

```csharp
var matrix = new int[3][];
matrix[0] = new int[3] { 1, 2, 3 };
matrix[1] = new int[2] { 1, 2 };
matrix[2] = new int[4] { 1, 2, 3, 4 };
```

## Lists

Lists are like variable-size arrays.

```csharp
using System.Collections.Generic;

var emptyList = new List<int>();
var prefilledList = new List<int>() { 1, 2, 3 };

emptyList.Add(1);
emptyList.Add(someArray);
```

To find an item in a list:

```csharp
var index = prefilledList.IndexOf(3); // 2
```

Instead of `.Length`, lists use `.Count`.

```csharp
prefilledList.Count // 3
```

## Enums

Collections of constants. Defaults to the `int` type.

```csharp
namespace StarWars
{
	public enum Episodes
	{
		ThePhantomMenace = 1,
		AttackOfTheClones = 2,
		RevengeOfTheSith = 3,
		ANewHope = 4,
		TheEmpireStrikesBack = 5,
		ReturnOfTheJedi = 6,
		TheForceAwakens = 7,
		TheLastJedi = 8
	}
	class Program
	{
		static void Main()
		{
			var bestEpisode = (Episodes)6; // ReturnOfTheJedi
		}
	}
}
```

# Debugging

Logging several values with labels:

```csharp
Console.WriteLine(string.Format("var1: {0}, var2: {1}", var1, var2));
```

# Misc

## Generating random numbers

```csharp
var random = new Random();
var myRandomNumber = random.Next(); // outputs integer between 1 and INT_MAX
var randomBetween1and10 = random.Next(1, 10);
```
