
- Compile for debugging: `gcc -Wall -g program.c`
- start gdb: `gdb a.out`
- optionally start with CLAs: `gdb --args a.out arg1 arg2`

Commands:

| Commands | Use |
|-|-|
|`refresh`|Refresh the display|
|`layout next`|See your code|
|`break POINT`| Set a breakpoint, Can be a line number function name, etc|
|`next` (`n` for short)|Step|
|`continue`|Continue till the next breakpoint|
|`print VARIABLE`|Print a variables value|
|`print *arr@len*`|Print an array|
|`watch VARIABLE`|Watch a variable for change|



