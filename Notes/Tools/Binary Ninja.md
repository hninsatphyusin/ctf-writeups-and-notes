hit `space` to toggle between graph view and linear view 
`n` - to rename selected variable
`g` - go to address
`cmd` + `f` => to find something

# tagging rows - lines
go under the `cross references` view
right click on the line u want to tag 
click `tag selected rows`
select `needs analysis`
then can view in the left sidebar in the tags view

# Opening command palette 
`cmd` + `p`
then can type `show address` - to enable the address at the side of the disassembled code 
can open python terminal also 


`esc` - to back to the windows you were looking at previously
`cmd` + `z` -> undo 

can change representation by right clicking and changing in `display as`

# Patching
can also right click and choose `patch` to change the jumps 
can press `e` on the line u want to patch to change anything in that line
can select the whole box and then `e` to change functionality then `save as`, `save file contents only`, choose binary name and now you have a new cracked binary.

# Scripting 

Seeing all functions
```python 
for function in bv.functions: 
	print(function)
```
clicking on the output functions will lead you to the functions in the middle view as well

Enumerating blocks in functions
```python
for function in bv.functions: 
	for block in function:
		print(block) 
```

Printing every single instruction in this binary
```python
for function in bv.functions:
	for block in function: 
		for instruction in block: 
			print(instruction)
```

Finding number of a certain function in the binary
```python
for function in bv.functions:
	if 'strcmp' in str(function):
		print(f'found: {count}')
		count+=1
```

Finding strings
```python
for string in bv.strings:
	print(string)
```

Get function by name 
```python
bv.get_functions_by_name()
```
returns a list of function, to choose the first one, do list indexing 

Enumerating through the instructions in a function
```python
for func in bv.functions:
	for inst in func.llil.instructions:
		print(f"{inst.address} : {inst}")
```
theres also `func.hlil.instructions

alternatively:
```
>>> for block in current_function.low_level_il:
...     for instr in block:
...         print (instr.address, instr.instr_index, instr)
...
4196422 0 push(rbp)
4196423 1 rbp = rsp {var_8}
4196426 2 rsp = rsp - 0x110
4196433 3 rax = rbp - 0xc0 {var_c8}
...
```