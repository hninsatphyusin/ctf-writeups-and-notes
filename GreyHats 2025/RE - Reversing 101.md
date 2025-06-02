
Disassembling the binary in binary ninja shows this
```
00402e1c    int32_t main(int32_t argc, char** argv, char** envp)
00402e2b        void* fsbase
00402e2b        int64_t rax = *(fsbase + 0x28)
00402e3a        int64_t s
00402e3a        __builtin_memset(&s, c: 0, n: 0x100)
00402f75        ignore_me_init_buffering()
00402f89        printf(format: "please input the correct passwor…")
00402fa7        __isoc99_scanf(format: "%255s", &s)
00402fc5        *(&s + strcspn(&s, U"\n")) = 0
00402fd7        int32_t rax_5 = a(&s)
00402fdf        int32_t rax_8
00402fdf        
00402fdf        if (rax_5 == 0xf)
00402ffd            c(&s, 0xf, b())
0040301b            rax_8 = memcmp(&s, &enc, 0xf)
0040301b        
00403022        if (rax_5 != 0xf || rax_8 != 0)
00403044            puts(str: "incorrect password. try again.")
00403022        else
0040302e            puts(str: "correct password! answer the qui…")
0040302e        
00403052        *(fsbase + 0x28)
00403052        
0040305b        if (rax == *(fsbase + 0x28))
00403063            return 0
00403063        
0040305d        __stack_chk_fail()
0040305d        noreturn
```
It basically takes in an password and then runs it through 3 functions a, b and c to confirm that it is the correct password. 

Lets take a closer look at a, b and c

## A - strlen 
```
00401209    void* a(char* arg1)
0040121e        if (*arg1 == 0)
00401220            return nullptr
00401220        
0040122b        char* var_10_1 = arg1
0040122b        
0040123e        do
00401230            var_10_1 = &var_10_1[1]
0040123e        while (*var_10_1 != 0)
0040123e        
00401244        return var_10_1 - arg1
```
It pretty much traverses from the start of the string to the end until it reaches the terminating byte, so it is basically a function like strlen. The main program checks whether the password is of length `0xf` which is 15

## B 
Don't exactly know what it does, but it doesn't take in any value and then gives out an `int64` value. We can examine this in gdb to know what value it calculates. 

Set a breakpoint after b is called in main
```
b *main+453
```
run the program and then key in a 15 char as the temporary password (e.g. `123456789123456`)

the result of functions are stored in rax after it returns to we can see the rax
```
$rax   : 0xc1de1494171d9e2f
```

## C
It takes in the str, the number 15 and the number that b generates (0xc1de1494171d9e2f) and does some calculation.
```
00402aea    int64_t c(int64_t arg1, int64_t arg2, int64_t arg3)
00402b0e        void* fsbase
00402b0e        int64_t rax = *(fsbase + 0x28)
00402b1d        uint32_t var_12c = 0
00402b2e        char var_120 = arg3.b
00402b3f        uint8_t var_11f = (arg3 u>> 8).b
00402b50        uint8_t var_11e = (arg3 u>> 0x10).b
00402b61        uint8_t var_11d = (arg3 u>> 0x18).b
00402b72        uint8_t var_11c = (arg3 u>> 0x20).b
00402b83        uint8_t var_11b = (arg3 u>> 0x28).b
00402b94        uint8_t var_11a = (arg3 u>> 0x30).b
00402ba5        uint8_t var_119 = (arg3 u>> 0x38).b
00402bdf        char var_118[0x108]
00402bdf        
00402bdf        for (int32_t i = 0; i s<= 0xff; i += 1)
00402bc7            var_118[sx.q(i)] = i.b
00402bc7        
00402c99        for (int32_t i_1 = 0; i_1 s<= 0xff; i_1 += 1)
00402c12            int32_t temp0_1
00402c12            int32_t temp1_1
00402c12            temp0_1:temp1_1 = sx.q(i_1)
00402c13            uint32_t rdx_3 = temp0_1 u>> 0x1d
00402c2c            int32_t temp2_1
00402c2c            char temp3_1
00402c2c            temp2_1:temp3_1 = sx.q(zx.d((&var_120)[sx.q(((temp1_1 + rdx_3) & 7) - rdx_3)])
00402c2c                + zx.d(var_118[sx.q(i_1)]) + var_12c)
00402c2d            uint32_t rdx_5 = temp2_1 u>> 0x18
00402c37            var_12c = zx.d(temp3_1 + rdx_5.b) - rdx_5
00402c45            char rax_38 = var_118[sx.q(i_1)]
00402c6b            var_118[sx.q(i_1)] = var_118[sx.q(var_12c)]
00402c81            var_118[sx.q(var_12c)] = rax_38
00402c81        
00402c9f        uint32_t var_130 = 0
00402ca9        uint32_t var_12c_1 = 0
00402ca9        
00402dcc        for (void* i_2 = nullptr; i_2 u< arg2; i_2 += 1)
00402ccc            int32_t temp4_1
00402ccc            char temp5_1
00402ccc            temp4_1:temp5_1 = sx.q(var_130 + 1)
00402ccd            uint32_t rdx_9 = temp4_1 u>> 0x18
00402cd7            var_130 = zx.d(temp5_1 + rdx_9.b) - rdx_9
00402cf8            int32_t temp6_1
00402cf8            char temp7_1
00402cf8            temp6_1:temp7_1 = sx.q(var_12c_1 + zx.d(var_118[sx.q(var_130)]))
00402cf9            uint32_t rdx_12 = temp6_1 u>> 0x18
00402d03            var_12c_1 = zx.d(temp7_1 + rdx_12.b) - rdx_12
00402d11            char rax_62 = var_118[sx.q(var_130)]
00402d37            var_118[sx.q(var_130)] = var_118[sx.q(var_12c_1)]
00402d4d            var_118[sx.q(var_12c_1)] = rax_62
00402db4            *(i_2 + arg1) ^=
00402db4                var_118[sx.q(zx.d(var_118[sx.q(var_12c_1)] + var_118[sx.q(var_130)]))]
00402db4        
00402de0        if (rax == *(fsbase + 0x28))
00402de8            return rax - *(fsbase + 0x28)
00402de8        
00402de2        __stack_chk_fail()
00402de2        noreturn
```

We can identify that this is a encryption algorithm called RC4 because it initialises a 2D array that is populated from 0 to 0xff
```
00402bdf        char var_118[0x108]
00402bdf        
00402bdf        for (int32_t i = 0; i s<= 0xff; i += 1)
00402bc7            var_118[sx.q(i)] = i.b
```
and then you do bytes shifts in the entries of the 2D array

after which there is also a Pseudo-Random Generation Algorithm in which the 2D array is modified by swaps and XORing. 

So the result of B was actually the key for the encryption of RC4 encryption. Thankfully, RC4 uses the same key for both encryption and decryption so we can use it to decrypt the enc to get the actual key.

Now the output of c is compared to `enc`, which is `\xd1\x15\x8a\xee\xb5\xbb\x0c\xa4\xab\xb7`

(u can see it in when u click on it in binary ninja)
![](../Assets/Screenshot%202025-06-02%20at%202.49.49%20PM.png)


## Python Reversing Script
Now we just need to take the 
```
#!/usr/bin/env python3

ENC = bytes([0xd1, 0x58, 0x15, 0x8a, 0xee, 0xb5, 0xbb, 0x52, 0x0c, 0x6b, 0xa4, 0xab, 0x6d, 0x7d, 0xb7])

def rc4(key_bytes, data):
    S = list(range(256))
    j = 0
    for i in range(256):
        j = (j + S[i] + key_bytes[i % len(key_bytes)]) & 0xFF
        S[i], S[j] = S[j], S[i]
    i = j = 0
    out = bytearray(len(data))
    for idx, c in enumerate(data):
        i = (i + 1) & 0xFF
        j = (j + S[i]) & 0xFF
        S[i], S[j] = S[j], S[i]
        K = S[(S[i] + S[j]) & 0xFF]
        out[idx] = c ^ K
    return bytes(out)


key = 0xc1de1494171d9e2f
key_bytes = [(key >> (8*i)) & 0xFF for i in range(8)]
plaintext = rc4(key_bytes, ENC)
password = plaintext.decode('ascii')
print(password)
```