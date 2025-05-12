
|Component|Length|Example|
|---|---|---|
|`byte`|8 bits|`0xab`|
|`word`|16 bits - 2 bytes|`0xabcd`|
|`double word (dword)`|32 bits - 4 bytes|`0xabcdef12`|
|`quad word (qword)`|64 bits - 8 bytes|`0xabcdef1234567890`|

Whenever we use a variable with a certain data type or use a data type with an instruction, both operands should be of the same size.

For example, we can't use a variable defined as `byte` with `rax`, as `rax` has a size of 8 bytes. In this case, we would have to use `al`, which has the same size of 1 byte. The following table shows the appropriate data type for each sub-register:

|Sub-register|Data Type|
|---|---|
|`al`|`byte`|
|`ax`|`word`|
|`eax`|`dword`|
|`rax`|`qword`|
