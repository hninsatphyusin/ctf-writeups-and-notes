Running the binary shows that it displays a maze and then you can navigate around the maze with ur arrow keys
![[Screenshot 2025-04-01 at 4.04.16 PM.png]]

Inspecting the pseudo-C code in binary ninja we see most of the code actually just being the actual maze logic. 

```
000014e2            if (window_y == 13 && window_x == 37)
000014e2            {
000015a0                wattr_on(stdscr, 0x80000, 0);
000015b6                wattr_on(stdscr, 0x200000, 0);
000015bb                void* rbp_1 = &data_40c0;
000015bb                
000015d0                for (int32_t j_1 = 6; j_1 != 0x1e; )
000015d0                {
000015d2                    uint64_t j_2 = (uint64_t)j_1;
000015d4                    j_1 += 1;
000015d4                    
000015eb                    if (wmove(stdscr, 6, j_2) != 0xffffffff)
00001603                        waddch(stdscr, (uint64_t)*(uint8_t*)(&data_4120 + (int64_t)*(uint32_t*)rbp_1));
00001603                    
000015c9                    rbp_1 += 4;
000015d0                }
000015d0                
0000161b                wattr_off(stdscr, 0x200000, 0);
00001631                wattr_off(stdscr, 0x80000, 0);
000014e2            }
```

Here this suggest that when the window size is 13x37, the display of the maze will be different. 

We resize the window size of the terminal using python.
```
python3 -c "import os; print('\x1b[8;13;37t')"
```
Then executing the binary again
![[Screenshot 2025-04-01 at 4.14.48 PM.png]]
Compared to the other easy challenge, this is easier.