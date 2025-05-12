## Main Function
![](../Assets/Screenshot%202025-04-01%20at%2010.45.44%20AM%201.png)
What the main function does is to display some terminal art and some text and then asks for user input. Then this user input is checked using the check input function.
![](../Assets/Screenshot%202025-04-01%20at%2010.46.41%20AM%201.png)

Here the argument of check_input, which from main is the user_input is compared to `rax_1` which is the result of `decode_secret()`. `strcmp` returns 0 when two strings are identifical. 

Checking the decode_secret()
![](../Assets/Screenshot%202025-04-01%20at%2010.47.02%20AM.png)


The secret password is just the base64 decoded of "emFyZmZ1bkdsZWFW" reversed which is 'VaelGnuffraz'. Entering it into the program reveals the flag:
![](../Assets/Screenshot%202025-04-01%20at%2010.50.33%20AM.png)
### Alternative Solution 
![](../Assets/Screenshot%202025-04-01%20at%2010.52.02%20AM.png)
In the `check_input` function, if the strcmp returns 0, the function `decode_flag` is ran and thats where the flag is. Decoding it from base64 and reversing `result` will also get us the flag. 

Can click on the text in binary ninja which will lead you to 
`00004080  char flag[0x41] = "LmB9ZDNsNDN2M3JfYzFnNG1fM251cntCVEhgIHNpIGxsZXBzIHRlcmNlcyBlaFQ=", 0`
which is `HTB{run3_m4g1c_r3v34l3d}`