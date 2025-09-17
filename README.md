
# Cryptography CTF 
* **Cryptography (mật mã học)** đã không còn quá xa lạ với những người làm trong lĩnh vực An toàn thông tin ~~hoặc không~~. 
* Tuy nhiên ở CTF lại là một phạm trù khác, cũng giống như các category khác, phần lớn thử thách sẽ tập trung vào khai thác lỗ hổng triển khai của hệ thống (ở đây là những thuật toán mã hoá hiện đại). 

`Fun Fact`: Từ _"Cryptography"_ về mặt kỹ thuật được hiểu là _"Nghệ thuật viết code"_... ~~Xem hết Series này để trở thành nghệ nhân~~ 🌝

### Sơ lược 

Phạm vi của các thử thách mật mã trong CTF rất rộng lớn. Nó bắt đầu với các mật mã cổ điển, như các loại mật mã thay thế sẽ được đề cập trong bài viết này. Khi tiến xa hơn, người chơi sẽ gặp phải các khái niệm hiện đại hơn như:   

- **Hàm băm (Hashing)**: Các thuật toán như _MD5, SHA_, và các vấn đề liên quan như ``"collision"``

- **Mật mã dòng (Stream Ciphers)**: Các phương pháp mã hóa dữ liệu từng bit hoặc từng byte, thường sử dụng phép toán _XOR_. 
- **Mật mã bất đối xứng (Asymmetric Cryptography)**: Các hệ thống sử dụng cặp khóa công khai-khai riêng, như `RSA`. ~~cá nhân tôi thích con hàng này nhất~~  

### Các thuật ngữ cơ bản
-   **Plaintext**: Thông điệp gốc, chưa được mã hóa.

-   **Ciphertext**: Thông điệp đã được mã hóa, không thể đọc được nếu không có khóa.

-   **Key**: Thông tin bí mật được sử dụng để mã hóa và giải mã.

-   **Encryption (Mã hóa)**: Quá trình chuyển đổi plaintext thành ciphertext.
  
-   **Decryption (Giải mã)**: Quá trình chuyển đổi ciphertext trở lại plaintext.

<img width="720" height="480" alt="image" src="https://github.com/user-attachments/assets/95aced98-cbd0-4d87-a000-4c2cfaca2626" />
