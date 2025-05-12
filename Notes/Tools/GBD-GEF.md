90070200000000000000000000

check which functions are defined within the binary
```
info functions
```

variables 
```
info variables 
```

disassemble 
```
disas <function_name>
```
# Breakpoints 
```
b <function_name>
```

run the program 
```
r 
```

break at certain address
```
b *0x40100a
```

# Examining 
format: 
```
x/FMT ADDRESS
```
FMT - examine format, consisting of 3 parts below:

|Argument|Description|Example|
|---|---|---|
|`Count`|The number of times we want to repeat the examine|`2`, `3`, `10`|
|`Format`|The format we want the result to be represented in|`x(hex)`, `s(string)`, `i(instruction)`|
|`Size`|The size of memory we want to examine|`b(byte)`, `h(halfword)`, `w(word)`, `g(giant, 8 bytes)`|
example 
```shell-session
gef➤  x/4ig $rip
```

```shell-session
gef➤  x/s 0x402000
```

## View all registers 
```
registers
```

step instruction: `si` (count optional e.g. `si 3`) 
next instruction: `ni`
`si` goes into the function, `ni` doesn't

step: will continue until it exists from the current function

# Modifying

# patch command
```shell-session
Syntax: patch (qword|dword|word|byte|string) LOCATION VALUES
```

# set command to change register values 
```shell-session
gef➤  set $rdx=0x9
```


