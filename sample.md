# Task 1: Encrypt and Decrypt Text file
1. Create a text file named `plain.txt`:
*First, we write a message and save it in a text file:*<br>

```sh
echo "Sir, when will we meet again?...When the dandelions bloom, my dear." > plain.txt
```
Verify current folder for newly created file
Create a text:
![image](https://github.com/user-attachments/assets/3f902b71-6984-416c-8bbe-19db104eaede)
Verify: Check the current folder to ensure the file plain.txt has been created successfully - ```ls```

## 2. Encrypt the file using AES-256 in ECB mode:

```sh
openssl enc -aes-256-ecb -nosalt -in plain.txt -out ecb_encrypted.txt -K 00112233445566778899AABBCCDDEEFF00112233445566778899AABBCCDDEEFF
``` 
<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage1.png?raw=true"><br>

Cyphertext can clearly be seen in the screenshot.
## 3. View the encrypted file using `xxd`:

```sh
xxd ecb_encrypted.txt
```

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage2.png?raw=true"><br>

Hex bytes of encrypted file can clearly be seen in the screenshot.
You can view the hexadecimal representation of the encrypted file with the following command:``` xxd ecb_encrypted.txt```

 ![image](https://github.com/user-attachments/assets/d72fe691-4a38-4e86-aa22-860bacbb9481)

``` 00000000: d03a e017 500a 51fb 35c4 e304 4620 a7c9  .:..P.Q.5...F ..```

```00000000```: This is the offset, indicating the position of the first byte in this row. Each line begins with a number indicating the offset from the beginning of the file. Here,``` 00000000``` means that the bytes start from the first byte of the file.

```d03a e017 500a 51fb 35c4 e304 4620 a7c9```: This is a hexadecimal data string (each pair of numbers is a byte). These numbers are the encrypted bytes of the file encrypted by AES-256 in ECB mode.

```.:..P.Q.5...F ..```: This is the ASCII equivalent of the hex bytes. If the byte cannot be represented as a valid ASCII character, it is represented by a dot ```(.).```

```00000010, 00000020,...``` are the offsets of the next bytes.

The hex byte sequences are similar to the first line, representing the encoded data of the file.

## 4. Decrypt the file:

```sh
openssl enc -d -aes-256-ecb -nosalt -in ecb_encrypted.txt -out ecb_decrypted.txt -K 00112233445566778899AABBCCDDEEFF00112233445566778899AABBCCDDEEFF
```
![image](https://github.com/user-attachments/assets/ab706f94-9cad-4ab3-835b-4d7acf610ce2)

The origin content can be seen after decryption in the screenshot.
## 5. **For CBC mode**:

- Encrypt:

```sh
openssl enc -aes-256-cbc -nosalt -in plain.txt -out cbc_encrypted.txt -K 00112233445566778899AABBCCDDEEFF00112233445566778899AABBCCDDEEFF -iv 0102030405060708090A0B0C0D0E0F10
```

![image](https://github.com/user-attachments/assets/29d5e944-9e1e-4b22-a386-ae4171328cd6)

To view the contents of the cbc_encrypted.txt file in encrypted form.

To view the correct encoding in the terminal, use the command:```cat cbc_encrypted.txt```

![image](https://github.com/user-attachments/assets/05ee5e69-c991-498d-bdec-e756fd775398)

Decrypt:
To decrypt the CBC encrypted file, use:
```sh
openssl enc -d -aes-256-cbc -nosalt -in cbc_encrypted.txt -out cbc_decrypted.txt -K 00112233445566778899AABBCCDDEEFF00112233445566778899AABBCCDDEEFF -iv 0102030405060708090A0B0C0D0E0F10
```
Verify Decryption
Check the content of the decrypted file to confirm it matches the original.```cat cbc_decrypted.txt```

![image](https://github.com/user-attachments/assets/7ebf31c5-9e5e-4d65-818b-5ae9617e9004)

# Task 2. Encryption Mode – ECB vs. CBC
This lab compares the behaviour of ECB and CBC encryption modes
**Question 1**: Exploration of various ECB & CBC  with openssl
**Answer 1**:
## 1. Download the bitmap file `origin.bmp`.
![image](https://github.com/user-attachments/assets/5d48548d-6632-40fe-ba6b-ea94c6b32d4c)
## 2. Split the file into header and body:

```sh
dd if=origin.bmp of=header.bin bs=1 count=54
dd if=origin.bmp of=body.bin bs=1 skip=54
```

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage7.png?raw=true"><br>

## 3. Encrypt the body using CBC mode:

<span>*I reused the KEY and IV values from the first task, just to make sure the consistency.*</span><br>

```sh
openssl enc -aes-256-cbc -nosalt -in body.bin -out encrypted_body.bin -K 00112233445566778899AABBCCDDEEFF00112233445566778899AABBCCDDEEFF -iv 0102030405060708090A0B0C0D0E0F10
```

<span>*After using the `cat` command to look at the `encrypted_body.bin`, we can see it was fully encrypted.*</span><br>

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage8.png?raw=true"><br>

## 4. Combine the header and encrypted body:

```sh
cat header.bin encrypted_body.bin > partially_encrypted.bmp
```

<img width="500" alt="Screenshot" src="https://github.com/AlexanderSlokov/Security-Labs-Submission/blob/main/asset/encryptingLargeMessage9.png?raw=true"><br>
