
# Avanced C
## Bài 1: Compiler Process
### Compiler
Máy tính không thể hiểu được những đoạn code mà chúng ta viết, chúng chỉ hiểu được những **mã máy**. Vậy nên, phải cần có một quá trình dịch những đoạn code ta viết thành mã máy. Đó là quá trình **COMPILE**.

Quá trình compile bao gồm các bước như sau (sử dụng toolchain là gcc):
- **Preprocessing**: chuyển đổi các file .c .h .cpp .hpp,... thành các file .i .ii. Bước này cơ bản là chèn nội dung của các file được "#include" vào file output, thay thế nội dung đã được khai báo của các macro và xoá các dòng comment có trong chương trình.
```
    gcc -E <input.c> -o <output.i>
```
- **Compile**: dịch các file .i .ii thành các file ngôn ngữ assembly .s.
```
    gcc <input.i> -S -o <output.s>
```
- **Assembler**: dịch file assembly .s thành mã máy .o. File mã máy bao gồm 2 thành phần chính là địa chỉ thanh ghi và giá trị tại địa chỉ đó. File mã máy có thể ở 2 dạng là mã nhị phân (bin) và mã 16 (hex).
```
    gcc -c <input.s> -o <output.o>
```
- **Linker**: liên kết các file mã máy .o lại để tạo thành một file có thể xuất được .exe. Những file .exe là những file được nạp cho máy tính để máy tính có thể xử lý.
```
    gcc <input1.o> <input2.o>... -o <output.s>
```
Note: Nếu muốn chạy file .exe trên VS Code để debug thì nhập câu lệnh: ./\<output\>

### Macro
**MACRO** là những thông tin được xử lý trong bước **Preprocessing** của quá trình **COMPILE**. Có những macro sau:
- #include: khi preprocessing, editor sẽ chèn nội dung của một file vào chương trình. Macro này giúp chia nhỏ chương trình chính thành những file nhỏ hơn để dễ dàng quản lý và có thể tái sử dụng ở những dự án khác.

- #define: định nghĩa một chuỗi code bằng một chuỗi code khác giúp code được gọn, debug dễ. Khi preprocessing, những chuỗi được #define sẽ được thay thế bằng định nghĩa của chúng.

- #undef: xoá định nghĩa trước đó của chuỗi.

- #ifdef, #ifndef: check xem chuỗi đó đã được định nghĩa chưa, nếu có (#ifdef) hoặc không (#ifndef) thì sẽ biên dịch chương trình sau macro cho tới #endif.

- #if, #elif, #else: nếu điều kiện #if đúng thì sẽ biên dịch chương trình sau đó, nếu sai thì bỏ qua và xét tới các #elif, nếu tất cả sai thì biên dịch chương trình sau #else cho tới khi gặp #endif.

Các toán tử của macro: (Xem ví dụ ở folder bài 1)
- Có thể khai báo macro có tham số bằng cách bỏ chúng giữa () sau tên macro (giống với cách khai báo hàm nhưng không thêm kiểu biến).
- Nếu muốn viết macro nhiều dòng thì chèn ký tự "\" vào cuối mỗi dòng của định nghĩa (ngoại trừ dòng cuối).
- Toán tử #: chuẩn hoá biến hoặc chuỗi ký tự sau nó thành dạng string khi preprocessing (đặt chúng giữa "").
- Toán tử ##: nối 2 chuỗi ký tự trước và sau ## lại, thường được dùng để nối 1 chuỗi ký tự với tên 1 biến.

Ngoài ra, có thể sử dụng "\_\_VA_ARGS\_\_" để thay thế cho số lượng tham số không biết trước, dành cho những hàm hay macro mà ta không biết trước số lượng tham số truyền vào hàm.

## Bài 2: Advanced Function Concepts and Debug
### Thư viện stdarg
**STDARG** cung cấp khả năng viết những hàm mà không biết trước số lượng tham số truyền vào hàm.

- **va_list**: là 1 kiểu dữ liệu làm đại diện cho các tham số truyền vào.
```
    va_list <args>;
```
- **va_start**: là nơi chỉ định điểm bắt đầu của danh sách tham số, cần được gọi trước khi truy cập vào bất cứ tham số nào.
    ```
        va_start(args, label);
    ```
    - **args** là tên danh sách kiểu va_list đã được khai báo ở trên.
    - **label** là tên biến của điểm bắt đầu của danh sách tham số sẽ được truy cập bằng va_arg.


- **va_arg**: truy cập 1 tham số trong danh sách tham số. Ban đầu, con trỏ va_arg sẽ đi tới điểm bắt đầu là "label" trong va_start và đọc biến tiếp theo trong danh sách. Sau khi đọc xong, con trỏ va_arg sẽ tự động +1 để đi tiếp trong danh sách.
```
    va_arg(args, typedef)
```
**typedef** là kiểu dữ liệu của tham số cần đọc, nếu typedef khác với kiểu dữ liệu của tham số cần đọc thì giá trị trả về sẽ là giá trị rác.

- **va_end**: kết thúc việc sử dụng danh sách tham số và được gọi ngay trước khi kết thúc hàm. Khi gọi va_end, giá trị con trỏ va_arg sẽ được thu hồi về điểm bắt đầu của va_list.
```
    va_end(args);
```
### Thư viện assert
**ASSERT** cung cấp khả năng dừng chương trình và thông báo lỗi khi gặp một điều kiện nhất định. Nếu điều kiện của assert thoả, chương trình sẽ dừng lại và thông báo một thông điệp lỗi.
```
    va_arg(args, typedef)
```
**ASSERT** được dùng để debug, có thể để phòng ngừa những lỗi thuật toán hay lỗi toán học như chia cho 0. Để tắt tính năng debug thì dùng #define NDEBUG
## Bài 3: Advanced Pointer
**Pointer (Con trỏ)** là một biến lưu giá trị địa chỉ bộ nhớ của đối tượng khác. Pointer giúp các thao tác trên bộ nhớ được linh hoạt hơn. Kích thước của con trỏ phụ thuộc vào kiến trúc của vi xử lý và trình biên dịch.\
Cách khai báo:
```
    int *ptr = &value;
```
Sử dụng ký tự & giúp ta lấy được địa chỉ của biến đằng sau &. Ở ví dụ trên, biến con trỏ tên "ptr" có giá trị là địa chỉ của một biến khác tên là "test".\
Ta có thể lấy địa chỉ của biến "test" thông qua con trỏ "ptr" như sau:
```
    int address = ptr;
```
Để truy cập đến giá trị của biến "test", ta có thể sử dụng biến con trỏ "ptr" như sau:
```
    *ptr = 6;
```
Sử dụng ký tự * giúp ta truy cập được giá trị của biến được biến con trỏ chỉ tới (dereference). Ở đây, ta sử dụng con trỏ "ptr" để truy cập vào giá trị của biến "test" và thay đổi nó bằng 6.\
- **void pointer (con trỏ void)** là biến con trỏ trỏ tới một đối tượng chưa biết trước kiểu dữ liệu (tức là ta không thể biết giá trị của con trỏ). Cách khai báo giống với khai báo con trỏ bình thường nhưng thay kiểu dữ liệu bằng "void"
```
    void *ptr;
```
Void pointer có thể trỏ tới đối tượng với bất kỳ kiểu dữ liệu nào bằng cách ép kiểu dữ liệu của nó:
```
    int value = 5;
    void *ptr = &value;
    *(int*)(ptr) = 10;
```
Ở đây, ta vừa ép kiểu "ptr" là pointer to integer để nó có thể truy cập đến giá trị của "value" và đọc nó dưới dạng số nguyên.
- **Function Pointer (con trỏ hàm)** là biến con trỏ lưu địa chỉ của một hàm. Con trỏ hàm cho phép bạn truyền một hàm như là một đối số cho một hàm khác, lưu trữ địa chỉ của hàm trong một cấu trúc dữ liệu, hoặc thậm chí truyền hàm như một giá trị trả về từ một hàm khác.\
Cách khai báo con trỏ hàm phụ thuộc vào kiểu giá trị trả về của hàm và các kiểu dữ liệu của các tham số truyền vào hàm. Ví dụ:
```
    void function1();
    int function2(int a, int b);

    void (*ptrToFunction1)();
    int (*ptrToFunction2)(int, int);
```
Ta gán địa chỉ cho con trỏ hàm như sau:
```
    ptrToFunction1 = function1;
    ptrToFunction2 = function2;
```
Để truy xuất (thực thi) hàm thông qua con trỏ hàm ta làm như sau:
```
    (*ptrToFunction1)();
    (*ptrToFunction2)(4, 5);
```
Có thể hiểu () được dùng để truy xuất một hàm nào đó.
- **Pointer to Constant (con trỏ đến hằng)** là một con trỏ chỉ tới vùng nhớ hằng, tức là giá trị trong địa chỉ vùng nhớ đó không thể thay đổi. Tuy nhiên, pointer to constant có thể thay đổi địa chỉ mà nó trỏ tới.\
Cách khai báo:
```
    int const *ptr = &value;
    const int *ptr = &value;
```
- **Constant Pointer (con trỏ hằng)** là một con trỏ không thể thay đổi giá trị của nó, tức là không thể thay đổi địa chỉ mà nó trỏ đến. Có thể nói Constant Pointer là ngược lại với Pointer to Constant.\
Cách khai báo:
```
    int *const ptr = &value;
```
- **Pointer to pointer (con trỏ đến con trỏ)** là một con trỏ lưu giá trị địa chỉ của một con trỏ khác.\
Cách khai báo:
```
    int **ptr = &ptr1;
```
- **NULL Pointer** là một con trỏ không trỏ đến bất cứ đối tượng nào. Khi khai báo một pointer thì ta phải gán giá trị cho pointer đó hoặc là gán NULL cho pointer đó để tránh *Dangling Pointer (con trỏ lơ lửng)*. Dangling pointer chứa giá trị rác và khi dereference nó thì sẽ dẫn đến những hành vi không xác định có thể gây sập chương trình. Ngoài ra, NULL pointer có thể giúp kiểm soát pointer, cho ta biết là pointer đã được khởi tạo và đã có giá trị hợp lệ hay chưa.
## Bài 4: Storage Classes
### Extern
**Extern** là một từ khoá dùng để báo cho trình biên dịch biết là đối tượng đằng sau extern đã được khai báo hay định nghĩa ở một nơi khác trong chương trình hoặc ở file khác. Sau khi khai báo extern với đối tượng nào đó thì ta có thể sử dụng đối tượng đó trong vị trí chương trình hiện tại.\
Cách khai báo:
```
    extern int test;
    extern void function1();
    extern int functoin2(int, int);
```
### Static
- **Static Local (biến static cục bộ)** là một biến cục bộ có giá trị được giữ lại qua những lần gọi. Biến cục bộ là biến chỉ só giá trị sử dụng trong khu vực cục bộ đã khai báo nó. Giải thích:
    - Khi khai báo biến cục bộ không kèm từ khoá *static*, địa chỉ của biến sẽ được lưu vào phân vùng *stack*. Phân vùng stack sẽ thu hồi địa chỉ của biến cục bộ sau khi chương trình rời khỏi khu vực cục bộ của biến đó, nên sau khi thoát khỏi khu vực cục bộ này, địa chỉ của biến sẽ bị thu hồi và khi gọi lại khu vực cục bộ đó, máy tính sẽ khởi tạo lại biến đấy và mọi giá trị trước đó sẽ mất.
    - Biến cục bộ được khai báo static sẽ được lưu vào phân vùng *bss* (nếu bằng 0) hoặc *data* (nếu khác 0). 2 phân vùng này có điểm chung là sẽ chỉ thu hồi địa chỉ của biến sau khi chương trình kết thúc. Vì vậy, qua những lần gọi khu vực cục bộ khác nhau, địa chỉ của biến chưa bị thu hồi nên giá trị của biến tại địa chỉ đó chưa mất đi.
- **Static Global (biến static toàn cục)** là một biến toàn cục bị hạn chế phạm vi sử dụng chỉ trong file khai báo nó. Một biến toàn cục có thể dễ dàng được sử dụng bởi các file khác thông qua từ khoá *extern*, tuy nhiên khi khai báo biến toàn cục với *static*, nó sẽ chỉ cho phép file khai báo nó và không cho phép các file khác truy cập.
### Volatile
Việc tối ưu hoá chương trình giúp tăng hiệu suất chương trình. Khi biến được khai báo kèm với từ khoá **volatile**, ta báo cho trình biên dịch biết rằng biến này có thể bị thay đổi bất ngờ hoặc không theo một quy luật nào đó của trình biên dịch, cho nên là đừng tối ưu biến đó. Điều này đảm bảo khi đọc giá trị biến này thì ta đang đọc giá trị mới nhất từ bộ nhớ của nó.
Volatile được sử dụng nhiều khi biến này được sử dụng chung giữa ngắt và chương trình chính hoặc là được sử dụng cho các thanh ghi phần cứng, nơi mà thường thay đổi giá trị mà không tuân theo các quy tắc lập trình thông thường.
```
    volatile int test;
```
### Register
- Khi một biến được khai báo bình thường, biến đó sẽ được lưu vào bộ nhớ RAM. Tuy nhiên khi muốn truy cập đến một biến nào đó, ta truy cập thanh ghi CPU sẽ nhanh hơn truy cập bộ nhớ RAM. Ngoài ra khi thực hiện các phép tính toán, CPU sẽ phải lưu biến đó vào 1 thanh ghi CPU và liên kết nó với biến trên RAM, sau đó từ thanh ghi sẽ đưa đến bộ ALU để tính toán và cập nhật lại trên thanh ghi, và từ thanh ghi sẽ trả lại giá trị về cho RAM. Điều này sẽ gây mất thời gian khi ta phải thực hiện phép toán nhiều lần trong khoảng thời gian ngắn.
- Khi một biến được khai báo kèm từ khoá **register** ở phía trước, nó sẽ báo cho trình biên dịch biết rằng biến này sẽ được lưu vào 1 thanh ghi trên CPU. Điều này sẽ giúp việc truy cập đến biến đó sẽ diễn ra nhanh hơn. Tuy nhiên, trình biên dịch sẽ tối ưu hoá biến này khi không còn chỗ trên CPU hoặc thấy hiệu suất sử dụng của nó là không thoả đáng. Ngoài ra, ta không thể lấy địa chỉ của biến bằng toán tử "&" vì thanh ghi CPU không có địa chỉ bộ nhớ như RAM.
- Các biến register có thể được sử dụng để giảm thời gian tính toán nhiều lần trong khoảng thời gian ngắn như vòng lặp hoặc để ứng dụng trong những dự án cần hoạt động nghiêm khắc ở thời gian thực.
## Bài 6: Bit Manupilation
**Bitmask** là một kỹ thuật được sử dụng để thao tác và quản lý từng bit riêng lẻ trong một số nguyên. Bitmask thường được sử dụng để tối ưu hóa bộ nhớ, thực hiện các phép toán logic trên một cụm bit, và quản lý các trạng thái, quyền truy cập, hoặc các thuộc tính khác của một đối tượng.\
Các toán tử thường sử dụng trong bitmask như sau:
- **NOT (~)**: đảo giá trị của tất cả các bit trong biến.
```
	uint8_t test = 0b 1001 0101;
	~test;		// 0b 0110 1010;
```
- **AND (&)**: Khi 2 bit được AND với nhau có giá trị bằng 1 thì kết quả sẽ là 1, các trường hợp còn lại sẽ bằng 0 (giống phép nhân).
```
	uint8_t test1 = 0b 1001 0101;
	uint8_t test2 = 0b 1010 1001;
	test1 & test2 = 0b 1000 0001;
```
- **OR (|)**: Khi 2 bit được OR với nhau có giá trị bằng 0 thì kết quả sẽ là 0, các trường hợp còn lại sẽ bằng 1 (giống phép cộng).
```
	uint8_t test1 = 0b 1001 0101;
	uint8_t test2 = 0b 1010 1001;
	test1 | test2 = 0b 1011 1101;
```
- **XOR (^)**: Khi 2 bit được OR với nhau có giá trị bằng nhau thì kết quả sẽ là 0, khác nhau sẽ bằng 1.
```
	uint8_t test1 = 0b 1001 0101;
	uint8_t test2 = 0b 1010 1001;
	test1 ^ test2 = 0b 0011 1100;
```
- **Dịch trái (<<)**: Di chuyển các bit của biến bên trái toán tử "<<" về bên trái với số lần là giá trị của biến bên phải toán tử "<<". Khi di chuyển, các bit tràn ra khỏi khung dữ liệu sẽ bị loại bỏ và các bit 0 sẽ điền vào các ô trống.
```
	uint8_t test = 0b 1001 0101;
	test << 2;   //0b 0101 0100
```
- **Dịch phải (>>)**: Di chuyển các bit của biến bên trái toán tử ">>" về bên phải với số lần là giá trị của biến bên phải toán tử ">>". Khi di chuyển, các bit tràn ra khỏi khung dữ liệu sẽ bị loại bỏ và các bit 0 sẽ điền vào các ô trống (nếu số bị dịch là một số có dấu và có giá trị < 0 thì các bit 1 sẽ điền vào các ô trống).
```
	uint8_t test = 0b 1001 0101;
	test >> 2;   //0b 0010 0101
```
## Bài 7: Pending Data
### Struct
- Từ khoá **struct** được sử dụng để định nghĩa một kiểu dữ liệu tuỳ biến cho phép nhóm các biến (có thể là các kiểu dữ liệu) thành một đơn vị duy nhất. Struct thường dùng để định nghĩa một kiểu dữ liệu có các thành phần biến con có liên quan đến nhau như các thanh ghi trong ngoại vi hay một gói tin cần truyền đi. Struct giúp code dễ đọc, tính bảo trì cao và có thể tái sử dụng dễ dàng.\
Cách định nghĩa:
```
	typedef struct Test {
		int x;
		float y;
		double z;
	} Test;
```
Cách khai báo và truy cập đến các biến thành phần:
```
	Test test;
	test.x;
	test.y;
	test.z;
```
- Kích thước của struct là tổng kích thước của các thành viên + padding (nếu có):
	- Trình biên dịch sẽ quét qua các thành viên để tìm ra thành viên có kích thước lớn nhất để quyết định kích thước của 1 lần quét
   	- Các thành viên từ trên xuống dưới sẽ được sắp xếp vào các ô nhớ trong một lần quét đó, nếu kích thước còn lại của lần quét không đủ để chứa 1 thành viên thì phần còn lại đó sẽ để cho padding và thành viên đó sẽ được xếp vào lần quét tiếp theo
   	- Lặp lại quá trình trên cho đến khi tất cả các thành viên đã được sắp xếp vào các ô nhớ.
### Union
- Từ khoá **union** được sử dụng để định nghĩa một kiểu dữ liệu tuỳ biến cho phép nhóm các biến (có thể là các kiểu dữ liệu) thành một đơn vị duy nhất. Khác với "struct", nơi mà các thành viên có bộ nhớ riêng thì với "union", các thành viên chia sẻ bộ nhớ với nhau để tiết kiệm bộ nhớ. Điều này đồng nghĩa với việc chỉ có 1 thành viên được hoạt động ở một thời điểm.\
Cách định nghĩa:
```
	typedef union Test {
		int x;
		float y;
		double z;
	} Test;
```
Cách khai báo và truy cập đến các biến thành phần:
```
	Test test;
	test.x;
	test.y;
	test.z;
```
- Kích thước của struct là kích thước của thành viên lớn nhất + padding (nếu có)
## Bài 8: Memory Layout
Các file .hex được nạp vào VĐK sẽ được lưu vào bộ nhớ FLASH sử dụng programmer hoặc bootloader. Khi VĐK được cấp nguồn thì chương trình sẽ được copy vào RAM để CPU thực thi những lệnh trong chương trình. RAM được phân thành những phân vùng sau:
### Text
- Chứa các mã máy (các lệnh được thực thi)
- Lưu hằng số, con trỏ kiểu char
- Chỉ có quyền đọc và thực thi, không có quyền ghi
- Được thu hồi bộ nhớ khi chương trình kết thúc, tồn tại trong suốt vòng đời chương trình.
### Data
- Chứa các biến toàn cục, biến static đã được khởi tạo với giá trị khác 0
- Có quyền đọc/ghi trong suốt vòng đời chương trình
- Được thu hồi bộ nhớ khi chương trình kết thúc, tồn tại trong suốt vòng đời chương trình.
### bss
- Chứa các biến toàn cục, biến static chưa khởi tạo giá trị hoặc đã khởi tạo giá trị bằng 0
- Có quyền đọc/ghi trong suốt vòng đời chương trình
- Được thu hồi bộ nhớ khi chương trình kết thúc, tồn tại trong suốt vòng đời chương trình.
### Heap
- Quản lý bộ nhớ động được cấp phát trong quá trình thực thi chương trình và được quản lý bởi người lập trình
- Bộ nhớ được cấp phát động bởi các hàm như malloc, calloc, realloc:
	- malloc(size):
 		- Cấp phát bộ nhớ liên tục có kích thước là "size"
   		- Bộ nhớ được cấp phát chưa được khởi tạo (giá trị rác)
       	- Giá trị trả về là con trỏ void nếu thành công và NULL nếu thất bại
	```
 		int *test1 = (int*)malloc(size * sizeof(int));		// Cấp phát "size" ô nhớ động dạng int hoặc có kích thước là "size * sizeof(int)" byte
 	```
	- calloc(num, size):
   		- Cấp phát một vùng nhớ động gồm "num" phần tử, mỗi phần tử có kích thước là "size"
       	- Bộ nhớ được cấp phát có giá trị bằng 0
       	- Giá trị trả về là con trỏ void nếu thành công và NULL nếu thất bại
    ```
    	char *test2 = (char*)calloc(num, sizeof(char));		// Cấp phát vùng nhớ gồm "num" ô kích thước là "sizeof(char)"
    ```
    - realloc(ptr, size):
      	- Thay đổi kích thước của bộ nhớ đã được cấp phát trước đó (hoặc tạo bộ nhớ mới) mà không muốn mất dữ liệu đã lưu trữ trong vùng bộ nhớ đó hoặc có thể giải phóng bộ nhớ đã cấp
      	- "ptr" là con trỏ tới vùng bộ nhớ cần thay đổi kích thước. Nếu "ptr" là NULL, realloc hoạt động như malloc.
      	- "size" là kích thước mới của vùng bộ nhớ. Nếu "size" bằng 0 và "ptr" khác NULL thì realloc sẽ giải phóng bộ nhớ đã được khởi tạo được "ptr" trỏ tới.
      	- Giá trị trả về là địa chỉ của vùng bộ nhớ mới với kích thước đã thay đổi hoặc là trả về NULL nếu thất bại hoặc giải phóng bộ nhớ thành công.
    ```
    	int *test3 = (int*)realloc(test1, (size + 1)*sizeof(int));
    ```
- Bộ nhớ được thu hồi bằng tay bởi lập trình viên với lệnh free(ptr). Hệ điều hành sẽ thu hồi toàn bộ bộ nhớ chưa được giải phóng sau khi chương trình kết thúc, tuy nhiên, nó sẽ dẫn đến rò rỉ bộ nhớ.
```
	free(test1);
	free(test2);
	free(test3);
```
### Stack
- Được quản lý bởi hệ điều hành, chứa các biến toàn cục, các tham số truyền vào
- Có quyền đọc/ghi trong suốt vòng đời chương trình
- Các khung stack được tạo ra khi gọi hàm và sẽ thu hồi khi kết thúc hàm
## Bài 9: Data Structures
### JSON
**JSON (avaScript Object Notation)** là một định dạng truyền tải dữ liệu phổ biến trong lập trình và giao tiếp giữa các máy chủ và trình duyệt web, cũng như giữa các hệ thống khác nhau. Mỗi đối tượng JSON bao gồm một tập hợp các cặp "key" và "value", trong khi mỗi mảng JSON là một tập hợp các giá trị.\
Các định dạng của JSON bao gồm:
```
	typedef enum {
    	JSON_NULL,
    	JSON_BOOLEAN,
    	JSON_NUMBER,
    	JSON_STRING,
    	JSON_ARRAY,
    	JSON_OBJECT
	} JsonType;
```
- JSON_NULL: Kiểu trống
- JSON_BOOLEAN: Kiểu true/false
- JSON_NUMBER: Kiểu số
- JSON_STRING: Kiểu chuỗi ký tự, được nhận biết bằng "
- JSON_ARRAY: Kiểu mảng, mỗi phần tử có thể là một trong các định dạng còn lại, được nhận biết bằng [
- JSON_OBJECT: Kiểu đối tượng bao gồm các cặp "key" và "value" (phân tách nhau bằng dấu :), mỗi "value" có thể mọi loại định dạng JSON, được nhận biết rằng {\
Vì một value chỉ có thể có 1 định dạng nên ta định nghĩa các định dạng JSON bằng "union"
```
	typedef union value {
		int boolean;
		double number;
		char *string;
		struct {
			struct JsonValue *values;
			size_t count;
		} array;
    	struct {
			char **keys;
			struct JsonValue *values;
	    	size_t count;
    	} object;
	} value;
```
### Linked list
**Linked List** là một cấu trúc dữ liệu gồm một chuỗi các phần tử, trong đó mỗi phần tử được gọi là một "node" (nút). Các nút được kết nối với nhau bằng cách sử dụng các con trỏ, tạo thành một chuỗi liên tiếp. Mỗi nút bao gồm:
- Giá trị dữ liệu
- Con trỏ tới địa chỉ của nút tiếp theo trong chuỗi. Nếu đây là nút cuối thì giá trị này sẽ là NULL.\
```
	typedef struct Node
	{
    	int value;
    	struct Node* next;
	} Node;
```
Linked list giúp các thao tác như thêm hay xoá các phần tử trong chuỗi được nhanh chóng và linh hoạt hơn khi sử dụng một mảng tĩnh, đặc biệt là đối với những chuỗi có rất nhiều thành viên. Linked list còn giúp việc quản lý bộ nhớ hiệu quả vì sử dụng những con trỏ đến liên kết các nút lại với nhau.
### Stack
**Stack** là một cấu trúc dữ liệu tuân theo nguyên tắc *Last In, First Out (LIFO)*. Các thao tác cơ bản trên stack là:
- **pop**: Thêm một phần tử vào đỉnh của stack, không được thêm phần tử khi stack đầy.
- **push**: Lấy và loại bỏ phần tử đỉnh của stack, không được lấy phần tử khi stack trống.
- **top**: Trả về giá trị của phần tử đỉnh stack mà không loại bỏ nó.\
Stack bao gồm những thành phần sau:
- **size**: Kích thước stack, cho biết số phần tử tối đa được bỏ vào stack
- **items**: Danh sách các phần tử
- **top**: Vị trí của phần tử đỉnh stack, khi stack trống thì top = -1. Mỗi khi *pop* thì top++, *push* thì top--. (0 <= top < size)
```
	typedef struct Stack {
    	int* items;
    	int size;
    	int top;
	} Stack;
```
### Queue
**Queue** là một cấu trúc dữ liệu tuân theo nguyên tắc *First In, First Out (FIFO)*. Các thao tác cơ bản trên queue là:
- **enqueue**: Thêm một phần tử vào cuối queue, không được thêm phần tử khi queue đầy.
- **dequeue**: Lấy và loại bỏ phần tử đầu của queue, không được lấy phần tử khi queue trống.
- **front**: Trả về giá trị của phần tử đầu queue mà không loại bỏ nó, khi stack trống thì top = -1.\
Queue bao gồm những thành phần sau:
- **size**: Kích thước queue, cho biết số phần tử tối đa được bỏ vào queue
- **items**: Danh sách các phần tử
- **front**: Vị trí của phần tử đầu queue, khi queue trống thì front = -1. Mỗi khi *dequeue* thì front++, khi front >= size thì quay trở lại 0. (0 <= front < size)
- **rear**: Vị trí của phần tử cuối queue, khi queue trống thì rear = -1. Mỗi khi *enqueue* thì rear++, khi rear >= size thì quay trở lại 0. (0 <= rear < size)
```
	typedef struct Queue {
    	int* items;
    	int size;
    	int front, rear;
	} Queue;
```
## Bài 10: Big excersize No.1
### Binary Search
**Binary Search** là một thuật toán tìm kiếm hiệu quả được sử dụng để tìm vị trí của một phần tử cụ thể trong một mảng đã được sắp xếp. Binary Search được sử dụng để tìm kiếm các bản ghi trong cơ sở dữ liệu được sắp xếp. Binary Search liên tục chia mảng thành hai nửa và loại bỏ một nửa không chứa phần tử cần tìm. Cụ thể như sau:
- Sắp xếp mảng theo thứ tự tăng dần
- Đặt phần tử đầu của mảng là *left* (ban đầu là 0), phần tử cuối của mảng là *right* (ban đầu là kích thước mảng -1)
- Xác định phần tử nằm giữa (middle) của mảng bằng cách: (rigth - left)/2 và làm tròn lên
- Nếu giá trị của middle là giá trị cần tìm thì trả về middle, nếu không:
	- Nếu giá trị cần tìm > giá trị middle thì bỏ hết nửa mảng bên trái (left = mid + 1)
 	- Nếu giá trị cần tìm < giá trị middle thì bỏ hết nửa mảng bên phải (rigth = mid - 1)
- Nếu right < left hoặc left > right, trong mảng không có giá trị cần tìm, trả về NULL
- Lặp lại quá trình cho đến khi tìm được giá trị cần tìm.
### File Operations
File **CSV (Comma-Separated Values)** là một loại file văn bản được sử dụng để lưu trữ và truyền tải dữ liệu có cấu trúc dưới dạng bảng, trong đó các dữ liệu của các cột được phân tách bằng dấu phẩy (,) hoặc một ký tự ngăn cách khác. Ví dụ:
```
	Họ và tên, Tuổi, Địa chỉ, Số điện thoại
	John Doe, 30, 123 Main St, 555-1234
	Jane Smith, 25, 456 Oak St, 555-5678
	Bob Johnson, 40, 789 Pine St, 555-8765
```
Ngôn ngữ lập trình C cung cấp một số thư viện và hàm tiêu biểu để thực hiện các thao tác với file:
- **fopen**(const char *file_name, const char *access_mode): Mở file có địa chỉ "file_name" với chế độ truy cập "access_mode" bao gồm những kiểu như sau:
	- r: Mở file với chế độ chỉ đọc file. Nếu mở file thành công thì trả về địa chỉ của phần tử đầu tiên trong file, nếu không thì trả về NULL.
   	- w: Mở file với chế độ ghi vào file. Nếu file đã tồn tại, thì sẽ ghi đè vào nội dung bên trong file. Nếu file chưa tồn tại thì sẽ tạo một file mới. Nếu không mở được file thì trả về NULL.
   	- a: Mở file với chế độ nối. Nếu mở file thành công thì trả về địa chỉ của phần tử cuối cùng trong file. Nếu file chưa tồn tại thì sẽ tạo một file mới. Nếu không mở được file thì trả về NULL.
   	- r+: Mở file với chế độ đọc và ghi file. Nếu mở file thành công thì trả về địa chỉ của phần tử đầu tiên trong file, nếu không thì trả về NULL.
   	- w+: Mở file với chế độ ghi và đọc file. Nếu file đã tồn tại thì trả về địa chỉ của phần tử đầu tiên của file. Nếu file chưa tồn tại thì sẽ tạo một file mới.
   	- a+: Mở file với chế độ nối và đọc file. Nếu file đã tồn tại thì trả về địa chỉ của phần tử cuối cùng của file. Nếu file chưa tồn tại thì sẽ tạo một file mới.
```
	FILE *fp = fopen("test.csv", "a");
```
- errno_t **fopen_s**(FILE **file, const char *file_name, const char *access_mode): Tương tự như "fopen()", khác ở chỗ địa chỉ trả về của hàm sẽ gán về con trỏ FILE đã truyền vào tham số "FILE *file"
```
	FILE *fp;
	fopen_s(&fp, "test.csv", "a");
```
- int **fscanf**(FILE *file, const char *format, ...): Đọc dữ liệu từ một tập tin theo định dạng cụ thể.
	- **file**: Con trỏ đến tập tin
	- **format**: Chuỗi định dạng (tương tự như scanf)
	- ...: Danh sách các đối số để lưu trữ dữ liệu đọc được
	- Hàm sẽ trả về số lượng các mục đã được đọc thành công hoặc trả về "EOF" nếu gặp lỗi hoặc đến cuối tập tin trước khi đọc được bất kỳ mục nào.
```
	FILE *fp;
	int num;
	char *str;

	fscanf(fp, "%d %s", &num, str);
```
- char \***fgets**(char *buffer, int size, FILE *file): Đọc một dòng từ tập tin và lưu trữ nó vào một chuỗi.
	- **buffer**: Mảng ký tự nơi lưu trữ chuỗi đọc được
   	- **size**: Số lượng ký tự tối đa để đọc (bao gồm ký tự null kết thúc)
   	- **file**: Con trỏ đến tập tin
   	- Hàm sẽ trả về con trỏ đến chuỗi đọc được nếu thành công hoặc trả về "NULL" nếu gặp lỗi hoặc chạy đến cuối tập tin.
```
	FILE *fp;
	int characterRead = 50;
	char *buffer;

	fgets(buffer, characterRead, fp);
```
- int **fgetc**(FILE *file): Đọc một ký tự từ tập tin. Hàm sẽ trả về Ký tự được đọc dưới dạng số nguyên hoặc "EOF" nếu gặp lỗi hoặc đến cuối tập tin.
```
	FILE *fp;

	fgetc(fp);
```
- int **fread**(void *buffer, int size, int count, FILE *file): Đọc một số lượng phần tử từ tập tin vào bộ nhớ.
	- **buffer**: Con trỏ đến bộ nhớ nơi lưu trữ dữ liệu đọc được
   	- **size**: Kích thước của mỗi phần tử
   	- **count**: Số lượng phần tử để đọc
   	- Hàm trả về số lượng phần tử đã đọc thành công.
```
	FILE *fp;
	char *buffer;
	int characterRead = 50;

	fread(buffer, sizeof(char), characterRead, fp);
```
- int **fprintf**(FILE *file, const char *format, ...): Ghi dữ liệu vào tập tin theo định dạng cụ thể.
	- **format**: Chuỗi định dạng (tương tự như printf)
   	- ...: Danh sách các đối số chứa dữ liệu cần ghi
   	- Hàm trả về số lượng ký tự đã được ghi hoặc giá trị âm nếu xảy ra lỗi.
```
	FILE *fp;
	int num = 50;
	char *str = "Hello World!";

	fprintf(fp, "Number: %d\n", num);
	fprintf(fp, "String: %s", str);
```
- int **fputs**(const char *str, FILE *file): Ghi một chuỗi vào tập tin.
	- **str**: Chuỗi cần ghi
   	- Hàm trả về giá trị khác âm nếu ghi thành công hoặc EOF nếu gặp lỗi.
```
	FILE *fp;
	char *str = "Hi";

	fputs("Hello World!\n", fp);
	fputs(str, fp);
```
- int **fputc**(int character, FILE *file): Ghi một ký tự vào tập tin.
	- **character**: Ký tự cần ghi
   	- Hàm trả về ký tự được ghi nếu thành công hoặc EOF nếu gặp lỗi.
```
	FILE *fp;
	int character = 'B';

	fputc('A', fp);
	fputc(character, fp);
```
- int **fwrite**(const void *buffer, int size, int count, FILE *file): Ghi một số lượng phần tử từ bộ nhớ vào tập tin.
	- **buffer**: Con trỏ đến bộ nhớ nơi lưu trữ dữ liệu cần ghi
   	- **size**: Kích thước của mỗi phần tử
   	- **count**: Số lượng phần tử cần ghi
   	- Hàm trả về số lượng phần tử đã đọc thành công.
```
	FILE *fp;
	int data[] = {1, 2, 3, 4, 5};

    fwrite(data, sizeof(int), 5, fp);
```
- int **fclose**(FILE *file): Đóng một tập tin. Hàm trả về 0 nếu đóng thành công hoặc EOF nếu gặp lỗi.
```
	FILE *fp;

	fclose(fp);
```
- int **feof**(FILE *file): Kiểm tra xem đã đến cuối tập tin chưa. Hàm trả về Giá trị khác 0 nếu đã đến cuối tập tin hoặc trả về 0 nếu chưa đến cuối tập tin.
```
	FILE *fp;

	feof(fp);
```
- int remove(const char *filename): Xoá một tập tin khỏi hệ thống. Hàm trả về 0 nếu thành công và khác 0 nếu thất bại.
```
	remove("Test.csv");
```
- int rename(const char *oldFilename, const char *newFilename): Đổi tên file hoặc di chuyển file.
```
	rename("Test.csv", "Another_Test.csv");
```
# C++
-  Thư viện **\<iostream\>** là một phần của thư viện chuẩn của C++ được dùng để thao tác vào ra (input/output). Thư viện các đối tượng như "**cout**" để ghi dữ liệu ra màn hình hay "**cin**" đọc dữ liệu từ bàn phím.
```
#include <iostream>

std::cout << "Hello World!" << std::endl;	//endl: endline (kết thúc dòng, dòng mới)

std::cin >> number;
std::cout << "You entered: " << number << std::endl;
```
Khi sử dụng chỉ thị:
```
using namespace std;
```
Bạn có thể bỏ tiền tố "std::" khi sử dụng cout và cin.
```
#include <iostream>
using namespace  std;

scout << "Hello World!" << endl;	//endl: endline (kết thúc dòng, dòng mới)

cin >> number;
cout << "You entered: " << number << endl;
```
- Thư viện **\<string\>** là một phần của thư viện chuẩn C++ và cung cấp lớp "std::string" để làm việc với các chuỗi ký tự.
```
#include <iostream>
#include <string>
using namespace std;

string test = "Hello World!";
cout << test << endl;
```
## Bài 11: Class
Trong C++, từ khóa **class** được sử dụng để định nghĩa một lớp, là một cấu trúc dữ liệu tự định nghĩa có thể chứa dữ liệu và các hàm thành viên liên quan.
```
class className{
};
```
Sau khi khai báo tên của class, bên trong class ta sẽ khai báo các phạm vi truy cập của các member trong class. Có 3 phạm vi truy cập:
- **private**: Các thành viên chỉ có thể được truy cập từ bên trong lớp. Các thành viên này không thể được truy cập trực tiếp từ bên ngoài lớp. (Chỉ có các method khai báo trong class có thể truy cập đến các thành viên, các object không thể).
- **public**: Các thành viên có thể được truy cập từ bất kỳ đâu, cả từ bên trong và bên ngoài lớp. (Nôm na là những object, các method bao gồm cả constructor và destructor được khai báo trong class sẽ truy cập được đến các thành viên).
- **protected**: Các thành viên có thể được truy cập từ bên trong lớp và từ các lớp kế thừa, nhưng không thể được truy cập từ bên ngoài lớp.\
Các thành viên trong class không chỉ có các biến hay các mảng như struct hay union ở C, class cho phép các thành viên có thể là hàm.
```
class HinhChuNhat {
public:
    double chieuDai;
    double chieuRong;
	double tinhDienTich() {
        return chieuDai * chieuRong;
    }
};
```
Ở C++, các thành viên là biến hay mảng sẽ được gọi là "property", các hàm sẽ được gọi là "method".\ 
Nếu muốn truy cập đến các thành viên của class, ta sử dụng các cú pháp tương tự như struct hay union ở C:
```
HinhChuNhat hinh1;
hinh1.chieuDai = 10.0;
hinh1.chieuRong = 5.0;
std::cout << "Dien tich: " << hinh1.tinhDienTich() << '\n';
```
Ở ví dụ trên, 'HinhChuNhat' là một kiểu dữ liệu và 'hinh1' là tên biến. Nhưng ở C++, 'HinhChuNhat' được gọi là "class" và 'hinh1' được gọi là "object".\
Trong các quy tắc viết code trong C++, ta sẽ không viết định nghĩa của các method ở bên trong định nghĩa của class, ta phải định nghĩa các method riêng ở ngoài phần khai báo class.
```
class HinhChuNhat {
public:
    double chieuDai;
    double chieuRong;
	double tinhDienTich() {
        return chieuDai * chieuRong;
    }
	void display();
};

void HinhChuNhat::display(){
	std::cout << " Hello " << '\n';
}
```
Nếu method có tham số truyền vào, ta có thể gán giá trị cho tham số ngay trên phần khai báo định nghĩa cho method. Đây gọi là tham số mặc định. Khi các object truy cập đến các method có tham số truyền vào nhưng không nhập gì cả thì trình biên dịch sẽ lấy tham số mặc định để gán cho method. Nếu object có truyền tham số vào method thì trình biên dịch sẽ truyền giá trị đó vào method thay cho tham số mặc định.\
_Lưu ý_: Các tham số kiểu 'string' sẽ không cho phép tham số mặc định.
```class HinhChuNhat {
public:
    double chieuDai;
    double chieuRong;
	double tinhDienTich() {
        return chieuDai * chieuRong;
    }
	void display(int value = 10);	// Có thể để tham số truyền vào ở đây
};

void HinhChuNhat::display(int value){	// Hoặc ở đây
	std::cout << " Hello: " << value << '\n';
}
```
- **Constructor**: là một method sẽ được tự động gọi khi khởi tạo object. Constructor có tên trùng với tên class và không có kiểu trả về. Constructor thường được sử dụng để thiết lập các giá trị ban đầu cho các thuộc tính của đối tượng hoặc thực hiện các thao tác khởi tạo khác cần thiết.
```
class HinhChuNhat {
public:
	HinhChuNhat();
    double chieuDai;
    double chieuRong;
	double tinhDienTich() {
        return chieuDai * chieuRong;
    }
	void display();
};
HinhChuNhat::HinhChuNhat(){
	chieuDai = 10;
    chieuRong = 9;
}
```
Nếu constructor có tham số truyền vào thì khai khai báo object, ta phải khai báo thêm các tham số cần truyền vào constructor.
```
class HinhChuNhat {
public:
	HinhChuNhat(double dai, double rong);
    double chieuDai;
    double chieuRong;
	double tinhDienTich() {
        return chieuDai * chieuRong;
    }
	void display();
};
HinhChuNhat::HinhChuNhat(double dai, double rong){
	chieuDai = dai;
    chieuRong = rong;
}
```
```
int main(){
	HinhChuNhat hinh1(10, 9);
}
```
- **Destructor** là một method dùng để dọn dẹp các tài nguyên mà một đối tượng đã chiếm dụng trước khi đối tượng bị giải phóng. Destuctor sẽ được tự động gọi khi object được giải phóng. Destructor sẽ có tên trùng với tên của class, thêm ký tự ~ ở phía trước tên và không có kiểu trả về. Destructor không nhận tham số truyền vào.
```
class HinhChuNhat {
public:
	HinhChuNhat(double dai, double rong);
    double chieuDai;
    double chieuRong;
	double tinhDienTich() {
        return chieuDai * chieuRong;
    }
	void display();
	~HinhChuNhat();
};
HinhChuNhat::HinhChuNhat(double dai, double rong){
	chieuDai = dai;
    chieuRong = rong;
}
HinhChuNhat::~HinhChuNhat(){
	std::cout << "Destructor " << '\n';
}
```
Khi một object cục bộ được khai báo, chúng sẽ được lưu vào vùng nhớ _stack_. Khi thoát khỏi hàm, bộ nhớ sẽ giải phóng object đến sau trước rồi mới đến object tới đầu tiên (LIFO). Constructor của object nào chạy trước thì Destructor của object đó sẽ chạy sau và ngược lại.
- Khi khai báo các object, bộ nhớ sẽ phân chia không gian khác nhau để chứa các object này, nên các địa chỉ của các property, method ở các object là khác nhau. Khi một property được khai báo "**static**" ở trong class, địa chỉ của property này là giống nhau ở mọi object và chúng sẽ có vòng đời trong suốt quá trình chương tình chạy. Các static property cần được khai báo trước để bộ nhớ dành cho nó không gian để các object có thể tham chiếu tới (vì static property độc lập với các object).
```
class HinhChuNhat {

public:
    double chieuDai;
    double chieuRong;
    static int var;
};

int HinhChuNhat::var = 0;
```
- Khi một method được khai báo kèm "**static**", method đó sẽ có những đặc điểm sau:
	- Static method độc lập với các object
	- Static method có thể được gọi mà không cần tạo object cho class đó.
   	```
    class HinhChuNhat {
	public:
    	double chieuDai;
    	double chieuRong;
    	static void display();
	};

	void HinhChuNhat::(){
		cout << "Hello" << '\n';
    }

    int main(){
    	HinhChuNhat::display();		// Truy cập đến display() không cần tạo object
    }
    ```
    - Vì độc lập với các object nên static method không thể truy cập đến các property và method không static của class đó. Nếu muốn truy cập thì phải tạo object.
    - Static method có thể truy cập đến các static property và static method bên trong và ngoài class của nó.
    - Static method có vòng đời trong suốt quá trình chương trình chạy.
## Bài 12: OOP
**OOP (Object-Oriented Programming - Lập trình hướng đối tượng)** là một phương pháp lập trình mà trong đó mọi thứ được tổ chức xung quanh các *"object"*. OOP có 4 đặc tính:
- **Encapsulation (tính đóng gói)**: là tính chất mà trong 1 class, ta phải để các property được bảo mật, tức là ta sẽ không cho phép các object truy cập trực tiếp đến các property. Để làm được điều này, ta phải khai báo các property trong phạm vi `private` hoặc `protected`. Nếu muốn truy cập đến các property này thì ta khai sử dụng các `method` ở phạm vi `public`.
- **Inheritance (tính kế thừa)**: là khả năng sử dụng lại các property và method của một class (class con) trong một class khác (class cha). Class con sẽ có quyền truy cập đến các property và method có phạm vi `public` hoặc `protected`. Có 3 kiểu kế thừa:
	- **Public**: Class con sẽ được thừa kế các thành viên của class cha với phạm vi truy cập tương ứng (nếu thành viên là public ở class cha sẽ là public ở class con, protected ở class cha sẽ là protected ở class con).
   	- **Private**: Tất cả các thành viên được kế thừa của class cha sẽ có phạm vi truy cập `private` ở class con.
   	- **Protected**: Tất cả các thành viên được kế thừa của class cha sẽ có phạm vi truy cập `protected` ở class con.
- **Polymorphism (tính đa hình)**: là tính chất xảy ra khi có sự kế thừa của các class với chung một class cha.
- **Abstract (tính trừu tượng)**:
	- **Virtual method (Method ảo)**: là một method trong class cha có thể được ghi đè (`override`) trong các lớp con. Khi bạn khai báo một method là `virtual`, bạn cho phép lớp con cung cấp một phiên bản cụ thể của phương thức đó.
   	- **Pure Virtual Method (Method thuần ảo)**: là một method trong lớp cha mà bạn yêu cầu các lớp con phải cung cấp cài đặt cụ thể. Lớp cha chứa phương thức thuần ảo không thể được khởi tạo và được gọi là `lớp trừu tượng`.\
_Note_:
	- Khi gọi method qua con trỏ hoặc tham chiếu đến lớp cha, method từ lớp con sẽ được gọi nếu lớp con override phương thức đó.
	- Nếu `method ảo` có sử dụng property kiểu `protected` và không bị override, giá trị property của lớp con sẽ được sử dụng nếu property đó được thay đổi trong lớp con (khi object con được tạo thông qua con trỏ đến lớp cha).
 	```
  	class Base {
	protected:
    	string type = "Base";  // Thuộc tính được kế thừa

	public:
    	virtual string getType() const {  // Phương thức ảo
        	return type;
    	}
	};

	class Derived : public Base {
	public:
    	Derived() {
        	type = "DerivedType";  // Thay đổi giá trị thuộc tính trong lớp con
    	}
    	// Không ghi đè phương thức getType()
	};

	int main() {
    	Base* object = new Derived();
    	cout << object->getType() << endl;  // In ra: DerivedType
    	delete object;
	}
  	```
	- **Abstract Class (Lớp Trừu Tượng)** là một lớp có ít nhất một method thuần ảo. Bạn không thể tạo đối tượng từ lớp trừu tượng. Các đối tượng phải được tạo từ các lớp kế thừa cung cấp triển khai đầy đủ cho các phương thức pure virtual.
	```
	// AbstractShape shape;  // Không hợp lệ - không thể khởi tạo lớp trừu tượng
    AbstractShape* shape = new Circle();  // Hợp lệ - khởi tạo lớp kế thừa,
										// new là tạo object trên heap (bth tạo trên stack), cần giải phóng bằng delete
	```
