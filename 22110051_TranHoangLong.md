# Lab #1,2110051, Tran Hoang Long, Information Security_ Nhom 01FIE
# Task 1: Transfer files between computers  
**Answer 1**:

Thực hiện Bài 1

Bước 1: Vào container vm1 (bên gửi) bash Sao chép mã ```docker exec -it vm1 bash```

Bước 2: Tạo file văn bản
Tạo file message.txt với nội dung bất kỳ:

bash Sao chép mã ``` echo "Đây là nội dung của file gửi đi." > /shared/message.txt```

Bước 3: Tạo cặp khóa RSA
Tạo cặp khóa riêng tư và công khai:

bash Sao chép mã
```openssl genrsa -out private_key.pem 2048 openssl rsa -in private_key.pem -pubout -out public_key.pem ```

Bước 4: Tạo chữ ký số
Tạo chữ ký số từ file message.txt bằng khóa riêng tư:

Sao chép mã ```openssl dgst -sha256 -sign private_key.pem -out /shared/message.sig /shared/message.txt```

Bước 5: Gửi file và chữ ký

File và chữ ký sẽ được lưu trong thư mục /shared, container vm2 cũng truy cập được.

Bước 6: Vào container vm2 (bên nhận)

```docker exec -it vm2 bash```

Bước 7: Xác minh chữ ký
Kiểm tra file đã được chuyển:

Sao chép mã ```ls /shared```

![image](https://github.com/user-attachments/assets/38ca81b0-df50-44ef-af80-5b2c26f09a9e)

Xác minh chữ ký bằng khóa công khai:

```openssl dgst -sha256 -verify /shared/public_key.pem -signature /shared/message.sig /shared/message.txt```
Nếu chữ ký hợp lệ, bạn sẽ thấy:

```Verified OK```

![image](https://github.com/user-attachments/assets/aa842b94-4083-4b41-8016-69a25246dcd6)

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:

Bước 1: Tạo cặp khóa RSA (Khóa công khai và khóa riêng)

Trên vm1 (máy gửi):

Tạo khóa riêng RSA:

```openssl genpkey -algorithm RSA -out private_key.pem -aes256```

Lệnh này sẽ tạo ra một khóa riêng RSA, bảo vệ nó bằng mật khẩu (AES256).

Trích xuất khóa công khai từ khóa riêng:

```openssl rsa -pubout -in private_key.pem -out public_key.pem```

Lệnh này sẽ tạo ra khóa công khai từ khóa riêng private_key.pem và lưu nó vào file public_key.pem.

![image](https://github.com/user-attachments/assets/8d2febd0-7a0c-4932-a305-0241932e6454)

Bước 2:
Dưới đây là cách bạn có thể mã hóa lại file với các tùy chọn an toàn hơn:

```openssl enc -aes-256-cbc -salt -in file_to_transfer.txt -out file_to_transfer.txt.enc -pass file:./aes_key.bin -pbkdf2 -iter 10000```

Giải thích: Mã hóa đối xứng file với AES

-pbkdf2: Sử dụng phương pháp PBKDF2 để làm tăng độ an toàn cho quá trình tạo khóa.

-iter 10000: Số vòng lặp (10000 là giá trị mặc định trong OpenSSL), giúp tăng độ phức tạp của việc tạo khóa và bảo vệ trước các cuộc tấn công từ điển.

Bước 3: Mã hóa khóa AES bằng RSA

Trên vm1 (máy gửi):
Mã hóa khóa AES (aes_key.bin) bằng khóa công khai RSA của vm2:

```openssl rsautl -encrypt -inkey public_key.pem -pubin -in aes_key.bin -out aes_key.bin.enc```

Lệnh này sẽ mã hóa khóa AES (aes_key.bin) bằng khóa công khai RSA và lưu kết quả vào``` file aes_key.bin.enc```

Bước 5: Giải mã khóa AES trên vm2

Trên vm2 (máy nhận):

Giải mã khóa AES  ```(aes_key.bin.enc)``` bằng khóa riêng RSA của vm2:

```openssl rsautl -decrypt -inkey private_key.pem -in aes_key.bin.enc -out aes_key.bin```

Lệnh này sử dụng khóa riêng RSA của vm2 để giải mã khóa AES và lưu nó vào ```file aes_key.bin.```

Bước 6: Giải mã file với AES trên vm2

Trên vm2 (máy nhận):

Giải mã file với khóa AES đã giải mã:

```openssl enc -d -aes-256-cbc -in file_to_transfer.txt.enc -out file_to_transfer.txt -pass f```
