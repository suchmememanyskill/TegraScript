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


## String Class
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


## Array class
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
| len()        | -                          | -                            | Integer                    | Get the length of an array                                                                                             |
| slice()      | Integer, Integer           | 0: skip, 1: take             | ArrayReference (see above) | Gets a read-only slice of the array                                                                                    |
| foreach()    | String                     | 0: Name of variable          | None                       | Foreaches trough the array using the `{}` behind the foreach. Sets the variable in the string to the iterated variable |
| copy()       | -                          | -                            | Array                      | Copy's the array. Required to edit read-only arrays                                                                    |
| set()        | Integer, String or Integer | 0: index, 1: variable to set | None                       | Sets the variable at array index. You can also use array[index] = var                                                  |
| add()        | (Dependant on type)        | 0: to add                    | None                       | Adds type to array                                                                                                     |
| contains()   | (Dependant on type)        | 0: to check if contains      | Integer                    | Checks if array contains item. 0 if false, 1 if true                                                                   |
| bytestostr() | -                          | -                            | String                     | Converts a byte array to string                                                                                        |
| find()       | (Same as type)             | 0: needle                    | Integer                    | Searches needle in haystack. Returns offset, or -1 if not found                                                        |

## Dictionary class
You can initiate a dictionary by using `dict()`. This will create an empty dictionary. You can assign to a dictionary by doing the following: `a = dict() a.b = 1` After this, you can access the variables you put in by doing the following: `print(a.b)` This class is used as returns in some functions in the standard library

## Save class
You can initiate a save class by using `readsave("path/to/save")`. This uses a lot of memory. 

| Member   | Arguments         | Argument descriptions          | Result    | Description                                                               |
|----------|-------------------|--------------------------------|-----------|---------------------------------------------------------------------------|
| read()   | String            | 0: Path                        | ByteArray | Reads a file inside the save                                              |
| write()  | String, ByteArray | 0: Path, 1: ByteArray to write | Integer   | Writes to a file inside the save. Returns 0 on success, non zero on error |
| commit() | -                 | -                              | None      | Flushes the writes made to save and signs the save                        |


# Standard library

| Function | Arguments | Argument descriptions | Result | Description | Example |
|---|---|---|---|---|---|
| if | Integer | 0: Execute if not 0 | Elseable (.else(){}) | Executes function behind if, if the integer is non 0 | if (1) { print("Hello world!") } |
| while | Integer | 0: Execute if not 0 | None | Executes function behind while as long as integer is non 0 | i = 0 while (i < 10) { println(i) i = i + 1 } |
| exit | - | - | None | Exits the script | exit() |
| break | - | - | None | Exits a loop, or the script if not in a loop | while (1) { break() } println("The loop broke") |
| readsave | String | 0: Path to save | Save | Opens a save. Causes a fatal error if the save failed to open | mountsys("SYSTEM") readsave("bis:/save/8000000000000120").commit() |
| dict | - | - | Dictionary | Creates an empty dictionary | a = dict() |
| print | Any arg count of (Integer, String) | any: To be printed | None | Calls .print() on every argument. Puts spaces inbetween each argument | print("Hello world!") |
| println | Any arg count of (Integer, String) | any: To be printed | None | Calls print, then prints a newline | println("Hello world") |
| printpos | Integer, Integer | 0: X position, range 0-78, 1: Y position, range 0-42 | None | Sets the print position | printpos(20,20) |
| setpixel | Integer, Integer, Integer | 0: X position, range 0-1279, 1: Y position, range 0-719, 2: Hex color (0xRRGGBB) | None | Sets a pixel on the screen to the desired color | setpixel(0,0,0xFFFFFF) |
| setpixels | Integer, Integer, Integer, Integer, Integer | 0: X0 position, 1: Y0 position, 2: X1 position, 3: Y1 position, 4: Hex color | None | Sets a bunch of pixels on the screen to the desired color in the user provided ranges | setpixels(0,0,1279,719,0xFF0000) |
| emu | - | - | Integer | Returns if an emummc is enabled. returns 1 if enabled | print(emu()) |
| cwd | - | - | String | Returns the current working directory. Returns 'sd:/' if ran from builtin script | print(cwd()) |
| clear | - | - | None | Clears the screen and resets the cursor to the top left | clear() |
| timer | - | - | Integer | Get the current system time in ms | a = timer() println("Very intensive operation") print("Time taken:", timer() - a) |
| pause | - | - | Dictionary (a,b,x,y,down,up,right,left,power,volplus,volminus,raw) | Waits for user input, then returns a dictionary of what was pressed | p = pause() if (p.a) { println("A was pressed!") } |
| pause | Integer | 0: Bitmask | Dictionary (a,b,x,y,down,up,right,left,power,volplus,volminus,raw) | Same as argless pause() but you can provide your own bitmask. See source/hid/hid.h | pause(0xC) # Waits until either A or B is pressed |
| color | Integer | 0: Hex color (0xRRGGBB) | None | Sets the text color | color(0x00FF00) print("Green text!") |
| menu | StringArray, Interger | 0: Menu options, 1: Starting position | Integer | Makes a menu of the strings in the array. Returns the index of what was pressed. Pressing b always returns 0 | idx = menu(["1", "2", "3"], 0) println(idx, "was pressed!") |
| menu | StringArray, Integer, IntegerArray | 0: Menu options, 1: Starting position, 2: Color/Options array | Integer | Makes a menu. The lower 3 bytes of the 3rd argument is RGB (0xRRGGBB). The upper byte is a bitfield with: 0x1: skip, 0x2: hide, 0x4: fileIcon, 0x8: folderIcon | menu(["1", "2"], 0, [0xFF0000, 0x00FF00]) |
| power | Integer | 0: https://github.com/suchmememanyskill/TegraExplorer/blob/master/bdk/utils/util.h#L26 | None | Sets the power state. This is an advanced function.  | power(3) # Powers off and resets regulators |
| mountsys | String | 0: Mount target | Integer | Mounts a partition on sys/internal storage. Returns 0 on success, 1 on failure | mountsys("SYSTEM") |
| mountemu | String | 0: Mount target | Integer | Same as mountsys, but on emummc instead. | mountemu("SYSTEM") |
| ncatype | String | 0: Path to nca | Integer | Returns the nca type. Returns -1 on error. See | - |
| emmcread | String, String | 0: File destination, 1: Partition on sys | Integer | Dumps the provided partition to a file. Returns 0 on success, non zero on failure | emmcread("sd:/PRODINFO", "PRODINFO") |
| emmcwrite | String, String | 0: File source, 1: Partition on sys | Integer | Restores the file to the provided partition. Returns 0 on success, non zero on failure | emmcwrite("sd:/PRODINFO", "PRODINFO") |
| emummcread | String, String | 0: File destination, 1: Partition on emu | Integer | Same as emmcread() | emummcread("sd:/PRODINFO", "PRODINFO") |
| emummcwrite | String, String | 0: File source, 1: Partition on emu | Integer | Same as emmcwrite() | emummcwrite("sd:/PRODINFO", "PRODINFO") |
| readdir | String | 0: Path to dir | Dictionary (result, files, folders, fileSizes) | Reads a directory. Puts all files in files (StringArray), folders in folders (StringArray), and file sizes in fileSizes (IntegerArray). .result is an Integer, and 0 on success | a = readdir("sd:/") if (a.result) { exit() } a.files.foreach("x") { println(x) } |
| deldir | String | 0: Path to dir | Integer | Deletes a directory. Prints whatever it's deleting. Returns 0 on success | deldir("sd:/sxos") |
| mkdir | String | 0: Path to new dir | Integer | Creates a new directory. Returns 0 on success | mkdir("sd:/tegraexplorer") |
| copydir | String, String | 0: Path to dir, 1: Path to dir where the dir will be copied into | Integer | Copies a folder into another folder. Returns 0 on success | copydir("sd:/tegraexplorer", "sd:/switch") # This will copy sd:/tegraexplorer to sd:/switch/tegraexplorer |
| copyfile | String, String | 0: Path to source file, 1: Path to dest file | Integer | Copies source file to destination. Returns 0 on success | copyfile("sd:/hbmenu.nro", "sd:/hbmenu_2.nro") |
| movefile | String, String | 0: Source Path, 1: Destination path | Integer | Renames or moves the file (or folder) to destination. Returns 0 on success | movefile("sd:/hbmenu.nro", "sd:/lol.nro") |
| delfile | String | 0: Path to file | Integer | Deletes a file. Returns 0 on success | delfile("sd:/boot.dat") |
| readfile | String | 0: Path to file | ByteArray | Reads a file and returns a byte array. This could be a memory intensive operation. Raises a fatal error if there was an error reading the file | a = readfile("sd:/hbmenu.nro") |
| writefile | String, ByteArray | 0: Path to file, 1: Bytes to write | Integer | Writes bytes to a file. Returns 0 on success | writefile("sd:/yeet", ["BYTE[]", 1,2,3]) |
| fsexists | String | 0: Path | Integer | Checks if a file or folder exists. Returns 1 when it exists, 0 if it does not | fsexists("sd:/hbmenu.nro") |
| payload | String | 0: Path to payload | Integer | Boots to a payload. Returns an integer on failure | payload("sd:/atmosphere/reboot_payload.bin") |
| combinepath | Any arg count of (String) | any: String to be combined | String | Combines path parts together into a path | combinepath("sd:/", "tegraexplorer", "firmware") # Returns "sd:/tegraexplorer/firmware" |
| escapepath | String | 0: String to escape a folder | String | Escapes a folder path (to go 1 folder back). See example | escapepath("sd:/tegraexplorer/firmware") # Returns "sd:/tegraexplorer" |