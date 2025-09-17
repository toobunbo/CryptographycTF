
# Chapter 1: Từ Ceasar đến "bất khả xâm phạm" Vigenere

À thì nếu có câu hỏi kiểu :"hai ơi có loại mật mã nào vừa dễ hiểu, lại ít toán học, xem xong là giải được CTF mà lại còn ngầu không? ~~vãi lồn~~" thì ừ, Có. Chào mừng đến với chapter đầu tiên.

## Mật mã đơn giản (Simple Substitution)

Bản thân mật mã có thể được chia ra làm hai nhánh, được gọi
là **chuyển vị** và **thay thế**. Ở nội dung này ta sẽ chỉ đề cập đến mật mã **thay thế** và **chuyển vị mà không giống chuyển vị**.
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
--- debug --- 
Bản rõ gốc:           meet at midnight.
Bảng chữ cái mã hóa: QWERTYUIOPASDFGHJKLZXCVBNM
Bản mã tương ứng:      DTTZ QZ DORFOUIZ.

```
- Mật mã Ceasar thậm chí còn đơn giản hơn, khi ông thay thế từng chữ cái trong plaintext bằng một chữ cái đứng cách đó 3 vị trí trong bảng, song rõ ràng là bằng cách sử dụng sự dịch chuyển bất kỳ từ 1 đến 25 vị trí, ta có thể tạo ra 25 loại mật mã khác nhau cái mà ngày nay ta gọi là **Rotation**, tiêu biểu là _ROT13_
  
Rotation.py:
```
--- Ceasar Demo--- 
Bản rõ gốc:    I love TDTU!
Độ dịch:         3
Bản mã tương ứng: L ORYH WGWX!
```


### Giải mã như nào?

- Bắt đầu với Rotation trước đi, có thể thấy do giới hạn về số lượng bảng chữ cái (tiếng Anh), một thuật toán Brute Force nho nhỏ là có thể giải quyết được. ~~tự viết đi m~~
- Với mã hoá thay thế bằng bảng chữ cái thì sao? Có **26!** tổ hợp hoán vị có thể xảy ra, khiến cho việc BF trở nên bất khả thi.

"Vậy là mã hoá thay thế ký tự nó an toàn chưa?" Có, cho tới khi một ông Ả Rập tên là al-Kindī, thi triển kỹ thuật **Phân tích tần suất**. *Mã thay thế dùng một bảng chữ cái (monoalphabetic substitution cipher) phá sản từ đây.*

## Phân tích tần suất 
al-Kindī phân tích việc giải mật mã thay thế trong trường hợp biết nó được viết với ngôn ngữ nào, theo 3 ý chính sau: 
1. Tìm một văn bản có cùng ngôn ngữ.
2. Đếm số lần xuất hiện của từng chữ cái trong văn bản thường. Ký hiệu số từ xuất hiện nhiều nhất là "thứ nhất", "thứ hai",..
3. Thay thế từ xuất hiện nhiều nhất trong văn bảng mã hoá bằng số từ đã được ký hiệu tương ứng ở bước 2.

   <img width="727" height="670" alt="image" src="https://github.com/user-attachments/assets/aaf3dff3-61ff-469f-8505-4680575662b4" />

### Phân tích một văn bản mật Mã
<img width="1091" height="472" alt="image" src="https://github.com/user-attachments/assets/f8a4bb93-2252-4ae5-89a7-b23508b52ec7" />

- Sau khi áp dụng phân tích tần suất:

```
--- Văn bản giải mã  ---
poadens figg wns hit khiwh ti onye eatoer wafe ir moadcreh, woamo lnv onppeh ti toe list oihest peipde; eatoer redntayeu ir henr fraehcs, woamo as mertnahdv lire uhusund. oe dayec ndihe ah oas oiuse ah snyadde riw, woatoer hihe pehetrntec. n sahgde cilestam suffamec ti serye oal. oe brenkfnstec nhc cahec nt toe mdub, nt oiurs lntoelntamnddv fajec, ah toe snle riil, nt toe snle tnbde, heyer tnkahg oas lends wato itoer lelbers, lumo dess brahgahg n guest wato oal; nhc weht oile nt ejnmtdv lachagot, ihdv ti retare nt ihme ti bec. oe heyer usec toe misv monlbers woamo toe refirl priyaces fir ats fnyiurec lelbers. oe pnssec teh oiurs iut if toe twehtv-fiur ah snyadde riw, eatoer ah sdeepahg ir lnkahg oas tiadet.
```
Vẫn chưa giải mã hoàn chỉnh, nhưng ta có thể dự đoán được những từ còn lại. Ví dụ: "poadens figg wns...", nghe khá giống tên của một người nổi tiếng "Phileas Fogg", còn "wns" thì chắn chắn là "was" rồi. Áp dụng thay đổi:
- o --> h
- a --> i
- d --> l
- n --> a
- i --> o
  
Tương tự với các chứ cái còn lại, ta được văn bản sau khi giải mã:

```
phileas fogg was not known to have either wife or children, which may happen to the most honest people; either relativeu or near friends, which is certainly more unusual. he lived alone in his house in saville row, whither none penetrated. a single domestic sufficed to serve him. he breakfasted and dined at the club, at hours mathematically fixed, in the same room, at the same table, never taking his meals with other members, much less bringing a guest with him; and went home at exactly midnight, only to retire at once to bed. he never used the cosy chambers which the reform provides for its favoured members. he passed ten hours out of the twenty-four in saville row, either in sleeping or making his toilet
```
Done.
<img width="1077" height="800" alt="image" src="https://github.com/user-attachments/assets/38217b5b-b408-4592-af98-a2ab3f1cd6b9" />

Đùa thôi, có quá nhiều vấn đề ở cách làm này:
- Dễ thấy nhất là chiều dài của ciphertext phải đủ ngắn để ta có thể chỉnh sữa thủ công. Mà thật ra cũng chẳng ai ngồi chỉnh sửa thủ công, người ta quăng lên `Quipquip` :))). 
- Sử dụng các chương trình có sẵn như `Quipquip` là cách tối ưu nhất khi gặp Subtitution trên CTF. Vậy nếu **Ciphertext quá lớn, siêu lớn, không copy được** để quăng lên Quipquip được thì sao? ~~thì ngửa~~.

Tất nhiên là không ngửa. 


## Cách người sành điệu giải mã Substitution
### Out trình "Phân tích tần suất" cổ điển với N-Gram Statics

N-gram là một chuỗi gồm N ký tự liên tiếp. Nó hoạt động như một "dấu vân tay" thống kê, ghi lại các mẫu ký tự đặc trưng của một ngôn ngữ.
- Unigram: ```(E, T , H,..)``` phản ánh tần suất của từng ký tự riêng lẻ. Đây là phương pháp **phân tích tần suất** cổ điển ta đã bàn.
- Bigram ```(TH, ER, IN...)```: Cho thấy các cặp ký tự nào thường đi liền với nhau, cung cấp ngữ cảnh cơ bản.
* v.v

Ở phương pháp này ta sẽ sử dụng Quadgram (N = 4) để triển khai, một giá trị vừa đủ dài để nắm bắt các từ có nghĩa nhưng cũng đủ dài để xuất hiện thường xuyên trong kho dữ liệu. Sự cân bằng hoàn hảo giữa **tính đặc trưng** và **tính phổ quát**. 

Quan trọng nhất *Quadgram* đánh giá ký tự dựa trên **mối quan hệ của nó với các ký tự lân cận**, phản ánh đúng bản chất của ngôn ngữ. Điều này làm nó  vượt trội hơn nhiều so với phương pháp phân tích tần suất truyền thống.

### Các bước triển khai thuật toán ~~Qiupquip~~ giải mã

Chuẩn bị 1 file quadgrams.txt - Một từ điển tham chiếu chứa tần suất của mọi cụm 4 chữ cái trong tiếng Anh. Quy trình:

1. Thu thập Dữ liệu: Sử dụng một kho văn bản khổng lồ (ví dụ: hàng ngàn cuốn sách từ Project Gutenberg).

2. Chuẩn hóa: Toàn bộ văn bản được làm sạch - chuyển thành chữ hoa và loại bỏ mọi thứ không phải là chữ cái.

3. Đếm Tần suất: Sử dụng kỹ thuật "cửa sổ trượt" để quét qua toàn bộ văn bản và đếm số lần xuất hiện của mỗi quadgram (từ AAAA đến ZZZZ).

Kết quả thu về sẽ có dạng:
```
TION 13168375
NTHE 11234972
THER 10218035
THAT 8980536
OFTH 8132597
FTHE 8100836
THES 7717675
WITH 7627991
...
```

-  Giãi mã Ciphertext: ZQHHNF  
1. Chia ciphertext thành các quadgram bằng kỹ thuật "cửa số trượt": ```Ciphertext = [ZQHH, QHHN, HHNF] ```.
2. Thay thế ngẫu nhiên các ký tự trong Ciphertext
3. Tham chiếu Ciphertext đến quadgrams.txt. Tính toán Score.
4. Thực hiện một vài thay đổi nhỏ (ví dụ H --> P, F --> E):  ```Ciphertext = [ZQPP, QPPN, PPNE] ```. Tính toán score.
5. Lặp lại sau N lần thử, lưu lại kết quả có điểm cao nhất.
6. Tiếp tục thay thế ngẫu nhiên Ciphertext ban đầu và lặp lại các bước. Sau K lần lặp lại, in ra kết quả có Score cao nhất:  ```HAPPEN```.

Áp dụng với bài toán trên:
```
==================================================
QUÁ TRÌNH GIẢI MÃ HOÀN TẤT!
Khóa giải mã tốt nhất được tìm thấy: XDSVUNMPZRFOKETBALIQJYWCGH
Văn bản gốc:
hzsrnqc klyy wqc flo mflwf ol zqdn nsoznj wskn lj xzsrbjnf, wzsxz gqv zqhhnf ol ozn glco zlfnco hnlhrn; nsoznj jnrqosdne lj fnqj kjsnfbc, wzsxz sc xnjoqsfrv gljn efeceqr. zn rsdnb qrlfn sf zsc zlecn sf cqdsrrn jlw, wzsoznj flfn hnfnojqonb. q csfyrn blgncosx cekksxnb ol cnjdn zsg. zn pjnqmkqconb qfb bsfnb qo ozn xrep, qo zlejc gqozngqosxqrrv ksanb, sf ozn cqgn jllg, qo ozn cqgn oqprn, fndnj oqmsfy zsc gnqrc wsoz loznj gngpnjc, gexz rncc pjsfysfy q yenco wsoz zsg; qfb wnfo zlgn qo naqxorv gsbfsyzo, lfrv ol jnosjn qo lfxn ol pnb. zn fndnj ecnb ozn xlcv xzqgpnjc wzsxz ozn jnkljg hjldsbnc klj soc kqdlejnb gngpnjc. zn hqccnb onf zlejc leo lk ozn ownfov-klej sf cqdsrrn jlw, nsoznj sf crnnhsfy lj gqmsfy zsc olsrno.

Văn bản được giải mã:
PHILEAS FOGG WAS NOT KNOWN TO HAVE EITHER WIFE OR CHILDREN, WHICH MAY HAPPEN TO THE MOST HONEST PEOPLE; EITHER RELATIVEU OR NEAR FRIENDS, WHICH IS CERTAINLY MORE UNUSUAL. HE LIVED ALONE IN HIS HOUSE IN SAVILLE ROW, WHITHER NONE PENETRATED. A SINGLE DOMESTIC SUFFICED TO SERVE HIM. HE BREAKFASTED AND DINED AT THE CLUB, AT HOURS MATHEMATICALLY FIXED, IN THE SAME ROOM, AT THE SAME TABLE, NEVER TAKING HIS MEALS WITH OTHER MEMBERS, MUCH LESS BRINGING A GUEST WITH HIM; AND WENT HOME AT EXACTLY MIDNIGHT, ONLY TO RETIRE AT ONCE TO BED. HE NEVER USED THE COSY CHAMBERS WHICH THE REFORM PROVIDES FOR ITS FAVOURED MEMBERS. HE PASSED TEN HOURS OUT OF THE TWENTY-FOUR IN SAVILLE ROW, EITHER IN SLEEPING OR MAKING HIS TOILET.
==================================================

```

Thật sự Done.



















