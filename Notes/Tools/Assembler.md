```shell-session
nasm -f elf64 helloWorld.s
```

# Linking 
```shell-session
ld -o helloWorld helloWorld.o
```

Assembler bash script
```bash
#!/bin/bash

fileName="${1%%.*}" # remove .s extension

nasm -f elf64 ${fileName}".s"
ld ${fileName}".o" -o ${fileName}
[ "$2" == "-g" ] && gdb -q ${fileName} || ./${fileName}
```


./assembler.sh filename
