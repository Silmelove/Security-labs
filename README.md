# Security-labs
Security-labs
# task1: lab1: Conduct buffer overflow attack .
# BOF1:
![image](https://github.com/user-attachments/assets/2b43fc82-d1d0-429a-9185-167a06871e68)
# stack frame 
![image](https://github.com/user-attachments/assets/9664aca8-6a02-4b0b-b865-4f8ad47ff1ad)
+ when enter more than 200 characters , the data overflow the memory allocated for array[200]
  and overwrites the return address of the ``` vuln() ```.This can cause the program to return to the address of the ```secretFunc()``` function instead of returning to ```main()``` as usual.
  
grep secretFunc: grep is used to search for occurrences of secretFunc in the disassembled output.
This helps you find the memory address or location where the secretFunc is defined.

![image](https://github.com/user-attachments/assets/7a19fee4-0f26-44a1-957e-0e6462709205)

```0804846b```: This is the memory address of the secretFunc in the binary. 
It means that when the program is loaded into memory, the secretFunc will start at this address ```(0x0804846b in hexadecimal)```.
![image](https://github.com/user-attachments/assets/1f73da86-e5e7-4c1b-817c-20d284d695c1)

# BOF2:
![image](https://github.com/user-attachments/assets/468be265-c57b-4c58-b30c-ecc1734694ed)
 # Stack frame:
 ![image](https://github.com/user-attachments/assets/fb843a4e-eff3-44d7-92cb-f0a20a907756)
 Buffer Overflow Vulnerability: The ```fgets()``` call reads up to 44 characters (```fgets(buf, 45, stdin)```),
 but buf is only 40 bytes long. This creates a vulnerability because writing more than 40 characters will overwrite the memory beyond buf.
 
 Overwriting check: If a user enters more than 40 characters, the excess characters will overwrite the value of check, which is stored right after buf in the stack.
 By carefully crafting the input, an attacker can set check to``` 0xdeadbeef```, triggering the final condition and printing ```"Yeah! You win!"```.
 
 1.On ```/seclabs/bof$ ``` 
 
 2.```gcc -g bof2.c -o bof2.out -fno-stack-protector -mpreferred-stack-boundary=2```
 
 3.```echo $(python -c"print('a'*41)") | ./bof2.out```
 
 ![image](https://github.com/user-attachments/assets/7e478f27-5af5-4d9d-91af-808899757ebc)

4. ```echo $(python -c"print('a'*40) + '\xef\xbe\xad\xde' ") | ./bof2.out```
   ![image](https://github.com/user-attachments/assets/a971c2cf-d6c9-4cd2-8654-ecc72f541718)


# BOF3:
# Stack frame:
![image](https://github.com/user-attachments/assets/0b216046-97f6-46eb-bdcf-dd8139463c40)

1.```objdump -d bof3.out | grep shell```

  ![image](https://github.com/user-attachments/assets/73c84e74-725f-4340-a96b-22ea5d54d4fa)

2.```echo $(python -c"print('a'*128) + '\x5b\x84\x04\x08' ") | ./bof3.out```

![image](https://github.com/user-attachments/assets/0d8480bf-edff-4d90-b10e-3a5a6aa37231)


+ Normal: If there is no buffer overflow, after``` fgets()``` retrieves data from the user,
the function pointer func will still point to sup(), and the program will print``` "Congrat!".```
+ Buffer Overflow: If you enter more than 128 characters, the excess bytes will overwrite adjacent memory locations on the stack, including the function pointer ```func```.
   If you overwrite func with the address of``` shell()```, when ```func() ```is called, the``` shell()``` function will be executed,
   and the program will print "You made it! The shell() function is executed".







 




