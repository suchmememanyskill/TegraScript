# TegraScript
The scripting language of TegraExplorer

Notice: TegraScript v2 is entirely different than v1. If you still have v1 scripts, you'll have to rewrite them.

Included in this repository:
- [fwdump.te](https://suchmememanyskill.github.io/TegraScript/scripts/fwDump.te) dumps the firmware with the actual firmware version as it's name
- [systemRestore.te](https://suchmememanyskill.github.io/TegraScript/scripts/systemRestore.te) restores the output of [emmchaccgen](https://github.com/suchmememanyskill/EmmcHaccGen). Place the generated boot.bis and SYSTEM directory next to this script
- [systemwipe.te](https://suchmememanyskill.github.io/TegraScript/scripts/systemwipe.te) is essentially a 'factory reset'. This will wipe everything off the switch and make it boot up like it was fresh out of the box. Error (eprt) reports are kept, so this is not safe to use with sxos. 

Any of this scripts can be ran by putting them on the sd, then inside tegraexplorer navigating to the sd, selecting the file and running it.

## General Syntax

### Variables

Variables in TegraScript do not need explicit type definitions:
```
variable = function(arg1, arg2) # this calls function with 2 arguments: arg1 and arg2, and stores it in variable
```

Variables can be of the following types:
- Integer
- String
- Integer Array
- String Array
- Byte Array
- Empty Array 

Creating and accessing Array variables goes as follows:
```
variable = [1,2,3,4,5] # This creates an integer array and stores it into variable
function(variable[2]) # This calls function with 1 argument, index 2 of variable, which is 3
```

In tegrascript, operations are evaluated from left to right. This is important for math type operations. See the operator section for what type definitions you can put operators against. As a quick primer:
```
variable = 2 + 2 * 2 # This puts 8 into variable, as the calculations get evaluated from left to right
variable = 2 + (2 * 2) # But! we can also use brackets to prioritise calculations
variable = "a" + "b" # Adding 2 strings together is also a supported operator
```

Note: Minus integer values are supported this time around

Another note: You can do !variable to flip the integer inside

Another another note: Every object in TScript is not a reference type! every time you try to modify an array it re-makes the array. In practise array operations are slow

Every variable in TegraScript v2 is global, thus you can access any variable in any self-defined function

### Built in variables

TegraScript has 2 built in variables.
- `_CWD`, which is a string, and represents the current working directory, thus the directory the currently running script is in.
- `_EMU`, which is an int, and represents if an emummc is present. 1 for present, 0 for not present

When running `dirRead()` as a function, besides returning a list of filenames, also sets an integer array called `fileProperties` that holds if the representing index of the filenames is a folder or not. 1 for a folder, 0 for a file

### Functions

TegraScript has support for functions

Defining and using a function goes as follows:
```
function = { # We are defining a function called function here, with the {}
    # We put the code we want to run inside the function here
    var = 1 + 2 + 3
}

function() # after running this, variable will be set to 6
```

But you may see an issue: there are no arguments! Fear not, as every variable in TegraScript is global. You can thus solve it with the following syntax:
```
function = {
    b = a * 2 # We want to multiply a by 2 and put it into b, but how do we define a?
}

function(a = 10) # Ah! We can just define it in the function args

a = 20
function() # Or if you prefer, you can define it normally as well
```

### Flow control

TegraScript has the following functions for flow control: `if()`, `else()`, `while()`, `return()`, `exit()`

- `if()` checks it's first arg, if it's 1, it runs the next {}, otherwise it skips over it
- `else()` checks if the last statement was an if, and if that if skipped, we run the next {}
- `while()` checks it's first arg, if it's 1, it runs the next {}, and jumps back to the while
- `return()` exits the current running function, if there is one
- `exit()` exits the script entirely

Let's try to build a loop that iterates 30 times, and printing even numbers
```
i = 0
while (i < 30){ # check if i is below 30, if so, run the code between the brackets
    if (i % 2 == 0){ # is i dividable by 2?
        println(i)
    }

    i = i + 1 # don't forget the + 1 of i! otherwise an infinite loop will haunt you
}
```

## Operators

### Integer, Integer
Operator | Output
|:-|:-|
`+` | sum of both integers
`-` | integer minus integer
`*` | multiplication of both integers
`/` | integer divided by integer
`%` | integer division remainder (modulo) of integer
`<` | 1 if left is smaller, otherwise 0
`>` | 1 if left is bigger, otherwise 0
`<=`| 1 if left is smaller or equal, otherwise 0
`>=`| 1 if left is bigger or equal, otherwise 0
`==`| 1 if left and right are equal, otherwise 0
`!=`| 1 if left and right are not equal, otherwise 0
`&&`| 1 if left and right are non 0, otherwise 0. Also if output is 0, disregards rest of statement
`\|\|`| 1 if left or right are non 0, otherwise 0. Also if output is 1, disregards rest of statement
`&` | Binary operator. ANDs both integers together
`\|` | Binary operator. ORs both integers together
`<<`| Binary operator. Bitshifts the left integer to the left by right integer's amount
`>>`| Binary operator. Bitshifts the left integer to the right by right integer's amount

### String, String
Operator | Output
|:-|:-|
`+` | Adds both strings together
`==`| 1 if strings are equal, otherwise 0
`-` | Removes the end of the first string if the second string matches the end of the first. (Example: `"abcdef" - "def"` is `"abc"`)
`/` | Splits the left string based on the right string. Returns a string array

### (Integer Array, Byte Array), Integer
Operator | Output
|:-|:-|
`+` | Adds the right integer into the left array
`-` | Removes right integer amount of entries from the left array
`:` | Removes right integer amount of entries from the beginning of the left array

### String, Integer
Operator | Output
|:-|:-|
`-` | Removes right integer amount of characters from the left string
`:` | Removes right integer amount of character from the beginning of the left string

### StringArray, String
Operator | Output
|:-|:-|
`+` | Adds a string to an array

### EmptyArray, (String, Int)
Operator | Output
|:-|:-|
`+` | Creates the array of the right type and puts the given value as the first entry

## Functions

### Flow control functions
Name | Description | OutType
|:-|:-|:-|
`if(int arg)`       | Runs the next {} if arg is non zero | None 
`else()`            | Runs the next {} if the last statement was an if that skipped their {} | None
`while(int arg)`    | Runs the next {} if arg is non zero. Jumps to the if after exiting the {} | None
`return()`          | Breaks out of a function | None
`exit()`            | Exits the current script | None

### Utilities
Name | Description | OutType
|:-|:-|:-|
`print(...)`         | Prints all args provided to the screen. Can print Integer, String, IntegerArray. `\r` and `\n` is supported | None
`println(...)`       | Same as `print(...)` but puts a newline at the end | None
`color(string color)`| Sets the print color. Supported inputs: `"RED"`, `"ORANGE"`, `"YELLOW"`, `"GREEN"`, `"BLUE"`, `"VIOLET"`, `"WHITE"` | None
`len(var arg1)`      | Gets the length of a string or an array | Integer
`byte(IntegerArray arg1)`| Converts an integer array to a byte one | ByteArray
`bytesToStr(ByteArray arg1)`| Converts a byte array to a string | String
`printPos(int x, int y)` | Sets the printing position on screen. X/Y are in whole character sizes (16x16) | None
`clearscreen()`      | Clears the screen full of your nonsense | None
`drawBox(int x1, int y1, int x2, int y2, int color)` | Draws a box from x1/y1 to x2/y2 with the color as color (raw: 0x00RRGGBB) | None
`wait(int ms)`       | Waits for ms amount | None
`pause()`            | Pauses until controller input is detected. Returns the controller input as raw u32 bitfield | Integer
`version()`          | Returns an Integer array of the current TE version | IntegerArray
`menu(StringArray options, int startPos)` | Makes a menu with the cursor at startPos, with the provided options. Returns the current pos when a is pressed. B always returns 0 | Integer
`menu(StringArray options, int startPos, StringArray colors)` | Same as above, but the entries now get colors defined by the colors array. Uses the same colors as the `colors()` function | Integer
`menu(StringArray options, int startPos, StringArray colors, IntegerArray modifier)` | Same as above, but entries can be hidden or skipped defined by the modifier array. 0 for normal, 1 for skip, 2 for hide | Integer

Note about `pause()`. You need to work with raw bitfields. Have an example
```
# The most common controls
JoyY = 0x1
JoyX = 0x2
JoyB = 0x4
JoyA = 0x8

LeftJoyDown = 0x10000
LeftJoyUp = 0x20000
LeftJoyRight = 0x40000
LeftJoyLeft = 0x80000

if (pause() & JoyX){
    println("X has been pressed!")
}
```

### FileSystem functions
Name | Description | OutType
|:-|:-|:-|
`fileRead(string path)`     | Reads the file at the given path and returns it's contents in a byte array | ByteArray
`fileWrite(string path, ByteArray data)` | Writes data to the given path. Returns non zero on error | Integer
`fileExists(string path)`   | Checks if a file or folder exists at the given path. 1 if yes, otherwise 0 | Integer
`fileMove(string src, string dst)` | Moves a file from src to dst. Returns non zero on error | Integer
`fileCopy(string src, string dst)` | Copies a file from src to dst. Returns non zero on error | Integer
`fileDel(string path)`      | Deletes the file located at path. Returns non zero on error | Integer
`pathCombine(...)`          | Needs 2+ string args as input. Combines them into a path. First entry must be the source folder Example: `pathCombine("sd:/", "tegraexplorer")` -> `"sd:/tegraexplorer"` | String
`pathEscFolder(string path)`| Escapes a folder path. Example: `pathEscFolder("sd:/tegraexplorer")` -> `"sd:/"` | String
`dirRead(string path)`      | Reads a folder and returns a StringArray of filenames. Also creates an IntegerArray called `fileProperties` that is the same length as the filenames, and is non zero if the index of the filename is a folder | StringArray
`dirCopy(string src, string dst)`| Copies a folder from src to dst. Dst needs to be the containing folder of where you want src to go (`"sd:/tegraexplorer", "sd:/backup` -> `"sd:/backup/tegraexplorer"`). Returns non zero on error | Integer
`dirDel(string path)`       | Deletes the dir located at path. Returns non zero on error | Integer
`mkdir(string path)`        | Makes a directory at path | Integer
`launchPayload(string path)`| Launches the payload at the given path. Returns non zero on error | Integer

### Storage functions !! Dangerous
Name | Description | OutType
|:-|:-|:-|
`mmcConnect(string loc)` | Loc can be `"SYSMMC"` or `"EMUMMC"`. Returns non zero on error | Integer
`mmcMount(string loc)`   | Loc can be `"PRODINFOF"`, `"SAFE"`, `"SYSTEM"` and `"USER"`. Mounts the filesystem to the prefix `bis:/`. Returns non zero on error | Integer
`mmcDump(string path, string target)`| Dumps target to path. target can be anything in the EMMC menu in TE. Returns non zero on error | Integer
`mmcRestore(string path, string target, int force)`| Restores path to target. target can be anything in the EMMC menu in TE. Force forces smaller files to flash anyway. Returns non zero on error | Integer
`ncaGetType(string path)`| Returns the type of nca that is in path. Make sure you provide an nca and nothing else! Useful for differentiating between Meta and Non-Meta Nca's | Integer
`saveSign(string path)`  | Signs the (system) save at the given location. Make sure you provide a save and nothing else! Returns non zero on error | Integer

# Changelog
### 11/1/2021 @ 22:10 // Wow it isn't 2am for once!

For the release of TE 3.0.1, the following changes were made:
- A new type was added: Empty array type. You can get this type by running `var = []`
- Operators were added for StringArray, String and EmptyArray, (String, Integer)
- launchPayload was added as a function
- Menu now allows for an additional integer array to define hidden or skip flags
- A bug was fixed with <= and >= registering as < and >
- fileCopy's should not leave an ugly [ anymore
- A script now get executed at TE bootup if a script called `startup.te` is found on the root of the SD

### 5/1/2021 @ 1:32am // Sleep is still a lie

- Initial writeup on TScript v2
