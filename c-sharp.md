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
