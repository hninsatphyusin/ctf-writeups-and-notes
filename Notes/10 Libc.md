# Importing libc functions 
```nasm
global  _start
extern  printf
```

# Dynamic Linker 
```shell-session
 nasm -f elf64 fib.s &&  ld fib.o -o fib -lc --dynamic-linker /lib64/ld-linux-x86-64.so.2 && ./fib
```

