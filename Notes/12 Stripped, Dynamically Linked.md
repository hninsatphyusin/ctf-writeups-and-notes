what to do when a binary is stripped and dynamically linked

gdb 
```
info files 
```
find entry point, data section text section etc 

```
x/10i entry_address
```

Run this below in gdb (not gef)
```
break __libc_start_main
```
![](../Assets/Screenshot%202025-03-16%20at%2010.23.02%20PM.png)
The first argument in the breakpoint here is the address of the main function 



