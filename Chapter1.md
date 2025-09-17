
# Chapter 1: Từ Ceasar đến "bất khả xâm phạm" Vigenere

À thì nếu có câu hỏi kiểu :"hai ơi có loại mật mã nào vừa dễ hiểu, lại ít toán học, xem xong là giải được CTF mà lại còn ngầu không? ~~vãi lồn~~" thì ừ, Có. Chào mừng đến với chapter đầu tiên.

## Mật mã đơn giản (Simple Substitution)

Bản thân mật mã có thể được chia ra làm hai nhánh, được gọi
là **chuyển vị** và **thay thế**. Ở nội dung này ta sẽ chỉ đề cập đến mật mã **thay thế** và **chuyển vị có cấu trúc**.
### Cách triển khai
Tên sao thì nó vậy thôi, vì nội dung này không còn quá xa lạ nên nói qua một chút về lịch sử mật mã nhở.

Một trong những mô tả đầu tiên về mã hóa sử dụng phương pháp thay thế
xuất hiện trong _Kāma-Sūtra_, một bản viết tay ở thế kỷ 4 sau CN của nhà thông thái Bàlamôn tên là Vātsyāyana. _Kāma-Sūtra_ khuyên phụ nữ
nên học sáu mươi tư nghệ thuật, như nấu ăn, may vá, massage,và làm nước hoa... Môn thứ bốn mươi lăm trong danh sách là **mlecchitavikalpā**, nghệ thuật viết thư bí mật, nhằm giúp người phụ nữ che giấu những quan hệ bất chính của mình. ~~:))~~ 

Nguyên tắc hoạt động cũng rất đơn giản:
- Mật mã thay thế: Một trong những kỹ thuật được là tạo những cặp chữ cái trong bảng chữ cái một cách ngẫu nhiên, rồi sau đó mỗi chữ cái trong thư sẽ được thay thế bằng chữ cùng cặp với nó.
<image></image>

Substitution.py: 

```
def substitution(plaintext: str, bang_chu_cai_ma_hoa: str) -> str:
    ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    if len(bang_chu_cai_ma_hoa) != 26 or not bang_chu_cai_ma_hoa.isalpha():
        return "Lỗi: Bảng chữ cái mã hóa phải là một chuỗi 26 chữ cái."
    key = {ALPHABET[i]: bang_chu_cai_ma_hoa.upper()[i] for i in range(26)}
    
    ciphertext = ""
    for char in plaintext.upper():
        ciphertext += key.get(char, char)
            
    return ciphertext

if __name__ == "__main__":
    plaintext = "meet at midnight."
    key = "QWERTYUIOPASDFGHJKLZXCVBNM"
    ciphertext = substitution(plaintext, key)
    print(f"Bản rõ gốc:           {plaintext}")
    print(f"Bảng chữ cái mã hóa: {key}")
    print(f"Bản mã tương ứng:      {ciphertext}")

--- debug --- 
Bản rõ gốc:           meet at midnight.
Bảng chữ cái mã hóa: QWERTYUIOPASDFGHJKLZXCVBNM
Bản mã tương ứng:      DTTZ QZ DORFOUIZ.

```
- Mật mã Ceasar thậm chí còn đơn giản hơn, khi ông thay thế từng chữ cái trong plaintext bằng một chữ cái đứng cách đó 3 vị trí trong bảng chữ cái, song rõ ràng là bằng cách sử dụng sự dịch chuyển bất kỳ từ 1 đến 25 vị trí, ta có thể tạo ra 25 loại mật mã khác nhau cái mà ngày nay ta gọi là **Rotation**, tiêu biểu là _ROT13_
  
Rotation.py:
```
def ma_hoa_dich_vong(plaintext: str, Shiftkey: int) -> str:
    ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    ciphertext = ""

    for char in plaintext.upper():
        if char in ALPHABET:
            current = ALPHABET.find(char)
            newIndex =(current + ShiftKey) % 26
            ciphertext += ALPHABET[newIndex]
        else:
            ciphertext += char
    return ciphertext

if __name__ == "__main__":
    plaintext = "Attack at dawn!"
    ShiftKey = 3 # với Ceasar là 3  
    
    ciphertext = ma_hoa_dich_vong(plaintext, ShiftKey)
    
    print(f"Bản rõ gốc:    {plaintext}")
    print(f"Độ dịch:         {ShiftKey}")
    print(f"Bản mã tương ứng: {ciphertext}")

--- Ceasar Demo--- 
Bản rõ gốc:    I love TDTU!
Độ dịch:         3
Bản mã tương ứng: L ORYH WGWX!
```


### Giải mã như nào?

- Bắt đầu với Rotation trước đi, có thể thấy do giới hạn về số lượng bảng chữ cái (tiếng Anh), một thuật toán Brute Force nho nhỏ là có thể giải quyết được. ~~tự viết đi m~~
- Với mã hoá thay thế bằng bảng chữ cái thì sao? Có **26!** tổ hợp hoán vị có thể xảy ra, khiến cho việc BF trở nên bất khả thi. "Vậy là mã hoá thay thế ký tự nó an toàn chưa?" Có, cho tới khi một ông Ả Rập tên là al-Kindī, phát triển một kỹ thuật gọi là **Phân tích tần suất**. *Mã thay thế dùng một bảng chữ cái (monoalphabetic substitution cipher) phá sản từ đây.*

## Phân tích tần suất 
al-Kindī giải thích theo 3 ý chính sau: 
1. Tìm một văn bản có cùng ngôn ngữ.
2. Đếm số lần xuất hiện của từng chữ cái trong văn bản thường. Ký hiệu số từ xuất hiện nhiều nhất là "thứ nhất", "thứ hai",..
3. Thay thế từ xuất hiện nhiều nhất trong văn bảng mã hoá bằng số từ đã được ký hiệu tương ứng ở bước 2.
   
### Phân tích một văn bản mật Mã
<img width="1091" height="472" alt="image" src="https://github.com/user-attachments/assets/f8a4bb93-2252-4ae5-89a7-b23508b52ec7" />

- Sau khi áp dụng phân tích tần suất:

```
--- Văn bản giải mã  ---
poadens figg wns hit khiwh ti onye eatoer wafe ir moadcreh, woamo lnv onppeh ti toe list oihest peipde; eatoer redntayeu ir henr fraehcs, woamo as mertnahdv lire uhusund. oe dayec ndihe ah oas oiuse ah snyadde riw, woatoer hihe pehetrntec. n sahgde cilestam suffamec ti serye oal. oe brenkfnstec nhc cahec nt toe mdub, nt oiurs lntoelntamnddv fajec, ah toe snle riil, nt toe snle tnbde, heyer tnkahg oas lends wato itoer lelbers, lumo dess brahgahg n guest wato oal; nhc weht oile nt ejnmtdv lachagot, ihdv ti retare nt ihme ti bec. oe heyer usec toe misv monlbers woamo toe refirl priyaces fir ats fnyiurec lelbers. oe pnssec teh oiurs iut if toe twehtv-fiur ah snyadde riw, eatoer ah sdeepahg ir lnkahg oas tiadet.
```
Vẫn chưa giải mã hoàn chỉnh, nhưng ta có thể dự đoán được những từ còn lại. Ví dụ: "poadens figg wns...", nghe khá giống tên của một người nổi tiếng "Phileas Fogg", còn "wns" thì chắn chắn là "was" rồi. Áp dụng thay đổi:
- O --> h
- a --> i
- d --> l
- n --> a
- i --> o
  
Tương tự với các chứ cái còn lại, ta được văn bản sau khi giải mã:

```
phileas fogg was not known to have either wife or children, which may happen to the most honest people; either relativeu or near friends, which is certainly more unusual. he lived alone in his house in saville row, whither none penetrated. a single domestic sufficed to serve him. he breakfasted and dined at the club, at hours mathematically fixed, in the same room, at the same table, never taking his meals with other members, much less bringing a guest with him; and went home at exactly midnight, only to retire at once to bed. he never used the cosy chambers which the reform provides for its favoured members. he passed ten hours out of the twenty-four in saville row, either in sleeping or making his toilet
```
Done.
</image>

Đùa thôi, có quá nhiều vấn đề ở cách làm này:
- Dễ thấy nhất là chiều dài của ciphertext phải đủ ngắn để ta có thể chỉnh sữa thủ công. Mà thật ra cũng chẳng ai ngồi chỉnh sửa thủ công, người ta quăng lên `Quipquip` :))). 
- Sử dụng các chương trình có sẵn như `Quipquip` là cách tối ưu nhất khi gặp Subtitution trên CTF. Vậy nếu **Ciphertext quá lớn, siêu lớn, không copy được** để quăng lên Quipquip được thì sao? ~~thì ngửa~~.


~~Viết block học thuật mà chưa thấy chỗ học thuật~~

### Kết bài
Đọc đến đây rồi có thấy cấn cấn ở đâu không? Có quá nhiều vấn đề ở cách 






