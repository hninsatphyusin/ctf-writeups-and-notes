Order of its bytes in which they are stored or retrieved from memory

# Types of endianness 
1. Little-Endian 
	- Retrieval is right-to-left
	- Storage is left-to-right
1. Big-Endian 
	1. Retrieval is left-to-right
	2. Storage is right-to-left

Example: 
Address:  `0x0011223344556677`
![](../Assets/Screenshot%202025-01-12%20at%201.18.53%20PM.png)

Intel/AMD x86 - Little-endian byte order 

## Pushing string 
Have to push it in reverse 

Example: 
`Hello` -> o, l, l, e, H

