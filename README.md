# TegraScript
The scripting language of TegraExplorer

## Functions

Function | Args | Description | Output
|:-|:-|:-|:-|
`printf(...)` | ...: can be any count of args. Stuff like ("This ", "Is", $Pretty, @Neat) works | writes text to the screen | returns 0
`printInt(int arg1)` | arg1: Int variable to print | Displays an int variable to the screen in the format `@var: (number)` | returns 0
`setPrintPos(int arg1, int arg2)` | arg1: sets cursor position x, arg2: sets cursor position y | sets cursor to a position on the screen. Max X is 42, max Y is 78 | returns 0
`clearscreen()` | - | clears the screen | returns 0
`setColor(string arg1)` | arg1: color string. Valid args: `RED`, `ORANGE`, `YELLOW`, `GREEN`, `BLUE`, `VIOLET`, `WHITE` | Changes the color of the text printed | returns 0
`if(int arg1)` | arg1: checks if interger is true or false (not 0 or 0) | Part of flow control. Runs code inside {} | returns 0
`if(int arg1, string arg2, int arg3)` (overload) | See `check()` and the first `if()` | Part of flow control. Runs code inside {}. Accepts statements like `if (1, == , 1) {}` | returns 0
`goto(int location)` | location: interger aquired by `getLocation()` | jumps to the character offset specified. sets @RETURN based on current location | returns 0
`getPosition()` | - | Returns the current script location, for use with `goto()` | returns > 0
`math(int arg1, string arg2, int arg3)` | arg1 "operator arg2" arg3. Valid operators (arg2s): `"+"`, `"-"`, `"*"`, `"/"` | Does a math operation and returns the result | returns the result of the math operation
`check(int arg1, string arg2, int arg3)` | arg1 "operator arg2" arg3. Valid operators (arg2s): `"=="`, `"!="`, `">="`, `"<="`, `">"`, `"<"` | Does a check and returns the result. result is either 0 or 1 | returns 0 or 1
`invert(int arg1)` | - | makes non 0 integers a 0, and vise versa | returns 0 or 1
`setInt(int arg1)` | - | returns arg1, for setting of variables | returns arg1
`setString(string in, $svar out)` | $svar is a string variable, written as `$var` | copies in to out | returns 0
`setStringIndex(int in, $svar out)` | looks up earlier defined strings in order. User defined strings start at index 1. $svar is a string variable | Copies string table index to out | returns 0
`combineStrings(string s1, string s2, $svar out)` | $svar is a string variable | combines s1 and s2 (as s1s2) and copies it into out | returns 0
`compareStrings(string s1, string s2)` | - | compares s1 to s2. If they are the same, the function returns 1, else 0 | returns 0 or 1
`subString(string in, int startoffset, int size, $svar out)` | in: input string. startoffset: starting offset, 0 is the beginning of the string. size: length to copy, -1 copies the rest of the string. out: output var | Copies part of a string into another string | returns 0
`inputString(string startText, int maxlen, $svar out)` | startText: Starting input text. maxlen: Maximum allowed characters to enter. out: output var | Displays an input box for the user to input text. Note, only works with joycons attached! | returns 1 if user cancelled the input, 0 if success
`stringLength(string in)` | - | Gets the length of the input string | returns the length of the input string
`pause()` | - | pauses the script until it recieves user input. result will be copied into `@BTN_POWER`, `@BTN_VOL+`, `@BTN_VOL-`, `@BTN_A`, `@BTN_B`, `@BTN_X`, `@BTN_Y`, `@BTN_UP`, `@BTN_DOWN`, `@BTN_LEFT` and `@BTN_RIGHT` | returns >0
`wait(int arg1)` | arg1: amount that it waits | waits for the given amount of time, then continues running the script | returns 0
`exit()` | - | exits the script | -
`fs_exists(string path)` | path: full path to file | check if a file exists. 1 if it exists, 0 if it doesn't | returns 0 or 1
`fs_move(string src, string dst)` | src/dst: full path to file | move file from src to dst | returns >= 0
`fs_mkdir(string path)` | path: full path to file | creates a directory at the given path (note: returns 8 if the folder already exists | returns >= 0
`fs_del(string path)` | path: full path to file | deletes a file (or empty directory) | returns >= 0
`fs_delRecursive(string path)` | path: full path to folder | deletes a folder with all subitems | returns >= 0
`fs_copy(string src, string dst)` | src/dst: full path to file | copies a file from src to dst | returns >= 0
`fs_copyRecursive(string src, string dst)` | src/dst: full path to file | copies a folder with subitems from src to dst (note that dst is the parent folder, src will be copied to dst/src) | returns >= 0
`fs_openDir(string path)` | path: full path to folder | opens a directory for use with `fs_readDir()` and sets `@ISDIRVALID` to 1 | returns >= 0
`fs_closeDir()` | - | closes current open directory and sets `@ISDIRVALID` to 0 | returns 0
`fs_readDir()` | - | reads entry out of dir. Atomatically calls `fs_closeDir()` if the end of the dir is reached. Saves result to `$FILENAME` and `@ISDIR` | returns 0
`fs_combinePath(string left, string right, $var out)` | - | Will combine paths, like `sd:/` and `folder` to `sd:/folder`, also `sd:/folder` and `folder2` to `sd:/folder/folder2` | returns 0
`fs_extractBisFile(string path, string outfolder)` | - | take .bis and extract it into outfolder | returns >= 0
`mmc_connect(string mmctype)` | mmctype: either `SYSMMC` or `EMUMMC` | connects SYSMMC or EMUMMC to the system. Specify partition with `mmc_mount()` | returns 0
`mmc_mount(string partition)` | partition: either `SYSTEM` or `USER` | mounts partition in previously connected mmc | returns >= 0
`mmc_dumpPart(string type, string out)` | type: Either `BOOT` or a partition on the gpt. out: Out folder: for `BOOT` this needs to be a folder, otherwise a filepath | Dumps a part from the e(mu)mmc. Determined by earlier mmc_connect's | returns >= 0
`mmc_restorePart(string path)` | path: Needs to be `BOOT0`, `BOOT1` or a valid partition on the gpt. FS Partitions are not allowed | Restores a file to the e(mu)mmc. Determined by earlier mmc_connect's | returns >= 0


## Variables

TegraScript has 2 kinds of variables, @ints and $strings.
- You can define @ints by writing `@variable = setInt(0);` (or any function for that matter).
- You can define $strings with the use of `setString();`, `setStringIndex();` and `combineStrings();`.

You can use these variables in place of int or string inputs, so for example `@b = setInt(@a)` or `setString($a, $b)`

Note though that the int variables can't be assigned negative values

### Built in variables
There are some built in variables:
- `@EMUMMC`: 1 if an emummc was found, 0 if no emummc was found
- `@RESULT`: result of the last ran function
- `@JOYCONN`: 1 if both joycons are connected, 0 if not
- `$CURRENTPATH`: Represents the current path

(if `fs_readdir()` got ran)
- `@ISDIRVALID`: Is the open directory valid
- `$FILENAME`: Represents the current filename
- `@ISDIR`: 1 if the last read file is a DIR, otherwise 0

(if `pause()` got ran)
- `@BTN_POWER`, `@BTN_VOL+`, `@BTN_VOL-`, `@BTN_A`, `@BTN_B`, `@BTN_X`, `@BTN_Y`, `@BTN_UP`, `@BTN_DOWN`, `@BTN_LEFT`: result of the `pause()` function, represents which button got pressed during the `pause()` function

(if `goto()` got ran)
- `@RETURN`: sets @RETURN based on current location. can be used to go back to (after) the previously ran goto()

## Flow control

You can use `if()`, `goto()` and `math()` functions to control the flow of your program. Example:
```
@i = setInt(0)
@LOOP = getLocation()
if (@i, <= , 10){
    @i = math(@i, + , 1)
    printInt(@i)
    goto (@LOOP)
}

pause()
```
This will print numbers 0 to 10 as `@i: x`

Another example:

```
printf("Press a button")

pause()

if (@BTN_VOL+){
    printf("Vol+ has been pressed")
}
if (@BTN_VOL-){
    printf("Vol- has been pressed")
}
if (@BTN_POWER){
    printf("Power has been pressed")
}

pause()
```

# Changelog

#### 29/05/2020
*It's midnight already? i just got started*

With the release of TegraExplorer v2.0.3, the arg parser has been changed. If you find any bugs, please make an issue in either the TegraExplorer repository, or here.

3 new commands have been added
- subString()
- inputString()
- stringLength()

2 new built in variables has been added
- @JOYCONN (1 when both joycons are connected, 0 when at least 1 joycon is disconnected)
- @RETURN (Read on for how this works)

Minus values are now considered valid by the scripting language, but, printing them will not work well (the print function can only print u32's)

Errors have been significantly improved. There are now 2 types of errors, ERR IN FUNC, which indicates a scripting function failed, and SCRIPT LOOKUP FAIL, which means the function you inserted doesn't exist (can also mean the wrong amount of args were supplied). The Loc value on the error screen now shows the line number it failed at, rather than the character offset

Goto functions now generate a variable called @RETURN, which, will return to after the goto. This aids in the making of pseudo-functions. Example:

```
@functionsActive = setInt(0)

@friiFunction = getPosition()
if (@functionsActive) {
    printf("Hi this is a function, i think")
    goto(@RETURN)
}

@functionsActive = setInt(1)
goto(@friiFunction)

pause()
```

#### 03/05/2020
*God fucking dammit it's 2am again*

With the release of TegraExplorer v2.0.0, the pause() function changed. It adds some buttons from controllers if they are connected. See the pause() section above to see what buttons are mapped. Note that if no joycons are connected, power = a, up = vol+, down = vol-. Also note that the screen dimentions have changed, so your text might not fit anymore.

#### 26/04/2020
With the release of TegraExplorer v1.5.2, there has been 1 new feature implemented.

printf() now can print multiple variables. `printf("This ", "Is", $Pretty, @Neat)` is valid syntax now

#### 12/04/2020
With the release of TegraExplorer v1.5.1, there has been some breaking changes. `?LOOP` and `goto(?LOOP)` is no longer valid syntax. Replace this with `@LOOP = getPosition()` and `goto(@LOOP)`.

Other than this, `@check = check(1, "==", 1) if (@check) {}` can be simplified to `if (1, == , 1) {}`. For `math()` functions, you don't have to enclose operators in "" anymore (just like check/if), like `@math = math(1, + , 1)`
