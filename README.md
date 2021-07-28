# Syntax

## Variable assignment
Variables in TegraScript do not need explicit type definitions:
```
variable = function(arg1, arg2)
```

In TegraScript, user provided functions have no arguments and are written like this:
```
helloWorld = {
    print("hello world!")
}

helloWorld()
```

Arrays in TegraScript can be of 4 types: StringArray, ByteArray, IntegerArray and EmptyArray
```
strings = ["a", "b", "c"]
numbers = [1,2,3,4,5]
bytes = ["BYTE[]",1,2,3,4,5]
empty = [] # Can be converted to other typed arrays by adding a veriable into it
```

## Variable usage
In TegraScript, all variables are global. This means that any variable declared anywhere can be used anywhere

You can use variables in place of static numbers or strings
```
three = 3
1 + 2 + three
```

Most variable types have function members. See below for all types of variables
```
three = 3
three.print()
```

Arrays can be accessed as follows:
(the [arg] syntax is internally converted to variable.get(arg), which is also valid syntax if you wish to use it)
```
numbers = [1,2,3,4,5]
print(numbers[1]) # prints 2
```

When calling a user provided function, everything between the `()` gets evaulated, and the result gets discarded. This means that you can use the `()` to define variables. This used to a TegraScriptv2 bug, but is left in for backwards compatibility
```
a = {
    b = b + 1
}

a(b = 5)
```

Although normal assignment will work too
```
a = {
    b = b + 1
}
b = 5
a()
```

You can also invert integers as follows:
```
!1 # becomes 0
!0 # becomes 1

1.not() # becomes 0
```

## Operator precedence
TegraScript has no operator precedence. It runs operators from left to right, always. You can put code inbetween `()` to calculate it before it is used.
```
println(2 + 2 * 2) # prints 8
println(2 + (2 * 2)) # prints 6
```

## Flow control
TegraScript has the following functions for flow control:
- `if(int)` Checks if the integer is non-0, then executes the user provided function behind it. Example: `if (1) { print("Hello world!") }`. 
- if returns an elseable class. You can call `.else()` on it to get an else. Example: `if (0) { } .else() { print("hi") }`
- `while(int)` Checks if the integer is non-0, then keeps executing the user provided function behind it until the integer is 0. Example: `i = 0 while (i < 10) { print(i) i = i + 1 }`
- `break()` Raises an exception that is caught by while and foreach loops. Example: `while (1) { break() } # This will end`
- `exit()` Raises an exception to exit the script

No this is not a sensible language, thanks for asking

# Comments
Comments in TegraScript are started with `#`, and last until the end of the line.

There are some special comments to aid in prerequisites.
- `#REQUIRE VER x.x.x` Requires a minimum TegraScript version to run the script. x.x.x should be the minimum version, so like `#REQUIRE VER 4.0.0`
- `#REQUIRE MINERVA` Requires extended memory to run the script. This should be used if you work with large files, saves or if your script is generally very big (20kb+)
- `#REQUIRE KEYS` Requires keys to be dumped to run the script`
- `#REQUIRE SD` Requires the sd to be mounted to run the script`

# Classes

## Integer Class
Integers in TegraScript are always 8 bytes in length

Static defenition example:
```
10 # 10
0x10 # 16
```

| Operator | RVal    | Result  | Description           |
|----------|---------|---------|-----------------------|
| +        | Integer | Integer | Addition              |
| -        | Integer | Integer | Subtraction           |
| *        | Integer | Integer | Multiplication        |
| /        | Integer | Integer | Division              |
| %        | Integer | Integer | Modulo                |
| <        | Integer | Integer | Smaller than          |
| >        | Integer | Integer | Bigger than           |
| <=       | Integer | Integer | Smaller or equal than |
| >=       | Integer | Integer | Bigger or equal than  |
| ==       | Integer | Integer | Equal to              |
| !=       | Integer | Integer | Not equal to          |
| &&       | Integer | Integer | Logical AND           |
| \|\|     | Integer | Integer | Logical OR            |
| &        | Integer | Integer | And                   |
| \|       | Integer | Integer | Or                    |
| <<       | Integer | Integer | Bitshift left         |

| Member  | Arguments | Argument descriptions | Result  | Description                      |
|---------|-----------|-----------------------|---------|----------------------------------|
| print() | -         | -                     | None    | Prints the integer               |
| not()   | -         | -                     | Integer | Inverts the integer              |
| str()   | -         | -                     | String  | Converts the integer to a string |


# String Class
Strings in TegraScript are immutable

Static defenition example:
```
"Hello" # Hello as text
"\r\n" # Return to left side of the screen, newline
```

| Operator | RVal    | Result       | Description                           |
|----------|---------|--------------|---------------------------------------|
| +        | String  | String       | Adds 2 strings together               |
| -        | Integer | String       | Reduces string size by integer amount |
| ==       | String  | Integer      | Checks string equality                |
| !=       | String  | Integer      | Checks string inequality              |
| /        | String  | String Array | Splits left string every right string |

| Member  | Arguments | Argument descriptions | Result       | Description                          |
|---------|-----------|-----------------------|--------------|--------------------------------------|
| print() | -         | -                     | None         | Prints the string                    |
| len()   | -         | -                     | Integer      | Gets the length of the string        |
| bytes() | -         | -                     | Byte Array   | Returns the string as a byte array   |
| get()   | Integer   | 0: Index of character | String       | Returns the character at given index |
| split() | String    | 0: String to split on | String Array | Splits a string                      |


# Array class
Array classes can be of 4 types: Integer array, string array, byte array and empty array

An empty array will be converted into an integer or string array if an integer or string gets added.

You can add integers to byte arrays, and internally they'll be converted to bytes before being added

ArrayReference: arrayReference classes contain a .project() function. This returns the array. You can project anytime, but do note the result is not memory safe and not garuanteed to be valid after changing the array. It's best to re-project before every use.

You can get an index from the array by using `array[index]`, Example: `[1,2,3][0]`

You can also set an array index by using `array[index] = variable`. Example: `a = [1,2,3].copy() a[0] = 69`

Arrays that are static `[1,2,3]` are read-only and will need to be .copy()'d before modification, because they are evaluated before runtime. Arrays that are not static (`[a,b,c]`) will be evaluated every time the script runs past it, and thus do not need to be copied to be modified

| Operator | RVal                    | Result  | Description                       |
|----------|-------------------------|---------|-----------------------------------|
| +        | (Dependant on type)     | None    | Adds type to array                |
| -        | Integer                 | None    | Removes integer length from array |
| ==       | ByteArray, IntegerArray | Integer | Compares 2 number arrays          |

| Member       | Arguments                  | Argument descriptions        | Result                     | Description                                                                                                            |
|--------------|----------------------------|------------------------------|----------------------------|------------------------------------------------------------------------------------------------------------------------|
| get()        | Integer                    | 0: index of array            | String or Integer          | Gets a variable at index. You can also use array[index]                                                                |
| len()        | -                          | -                            | Integer                    | Get length of integer                                                                                                  |
| slice()      | Integer, Integer           | 0: skip, 1: take             | ArrayReference (see above) | Gets a read-only slice of the array                                                                                    |
| foreach()    | String                     | 0: Name of variable          | None                       | Foreaches trough the array using the `{}` behind the foreach. Sets the variable in the string to the iterated variable |
| copy()       | -                          | -                            | Array                      | Copy's the array. Required to edit read-only arrays                                                                    |
| set()        | Integer, String or Integer | 0: index, 1: variable to set | None                       | Sets the variable at array index. You can also use array[index] = var                                                  |
| add()        | (Dependant on type)        | 0: to add                    | None                       | Adds type to array                                                                                                     |
| contains()   | (Dependant on type)        | 0: to check if contains      | Integer                    | Checks if array contains item. 0 if false, 1 if true                                                                   |
| bytestostr() | -                          | -                            | String                     | Converts a byte array to string                                                                                        |
| find()       | (Same as type)             | 0: needle                    | Integer                    | Searches needle in haystack. Returns offset, or -1 if not found                                                        |

# Dictionary class
You can initiate a dictionary by using `dict()`. This will create an empty dictionary. You can assign to a dictionary by doing the following: `a = dict() a.b = 1` After this, you can access the variables you put in by doing the following: `print(a.b)` This class is used as returns in some functions in the standard library

# Save class
You can initiate a save class by using `readsave("path/to/save")`. This uses a lot of memory. 

| Member   | Arguments         | Argument descriptions          | Result    | Description                                                               |
|----------|-------------------|--------------------------------|-----------|---------------------------------------------------------------------------|
| read()   | String            | 0: Path                        | ByteArray | Reads a file inside the save                                              |
| write()  | String, ByteArray | 0: Path, 1: ByteArray to write | Integer   | Writes to a file inside the save. Returns 0 on success, non zero on error |
| commit() | -                 | -                              | None      | Flushes the writes made to save and signs the save                        |

