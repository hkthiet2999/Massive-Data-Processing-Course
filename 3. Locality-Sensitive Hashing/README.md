# D. Thuật toán Locality Sensitive Hashing trong bài toán Finding Similar Documents in Massive Data
 Mình may mắn được tiếp cận với một đề tài khá hay trong khóa học về Massive Data Processing, cụ thể trong đề tài này mình sẽ nghiên cứu về thuật toán Locality Sensitive Hashing (LSH) trong bài toán tìm kiếm các văn bản tương tự trong tập văn bản. Dưới đây là minh họa cho đề tài:   
 
 ![](LSH-for-Finding-Similar-Items/img/midterm_requirements.png)
 
 Dưới đây là phần mình tìm hiểu về thuật toán LSH và ứng dụng vào bài toán tìm các văn bản tương tự trong tập văn bản,phần này chiếm 3 điểm trong đề tài nên mình viết khá ngắn gọn để tiện trình bày trước lớp nhưng cũng rất đầy đủ và chi tiết, mời các bạn đọc qua.
 <a name="chD_I"></a>
 ## I. Tổng quan
 <a name="chD_I_1"></a>
### 1. Bài toán tìm kiếm các văn bản tương tự trong tập văn bản
 Một trong những vấn đề cơ bản trong khai thác dữ liệu là khám phá dữ liệu để tìm các mục tương tự với nhau. Việc tìm kiếm các tài liệu, văn bản, hình ảnh, âm thanh (gọi chung là documents) tương tự nhau là một vấn đề dựa trên các tập hợp dữ liệu lớn và được thực hiện dựa trên phương pháp Shingling. Sau đó, các tập hợp dữ liệu lớn này có thể được nén lại dựa trên sự giống nhau của các tập hợp chính và có thể được phân biệt với các phiên bản nén của chúng, điều này sẽ được thực hiện thông qua phương pháp Min Hashing. Nếu chúng ta  muốn tránh so sánh tất cả các tài liệu với nhau và chỉ so sánh các tài liệu có xác suất cao sẽ trùng lắp thì chúng ta nên sử dụng phương pháp Locality-Sensitive Hashing (LSH). 
 Trong bài báo cáo này, chúng em sẽ trình bày phương pháp sử Locality Sensitive Hashing để tìm kiếm các văn bản tương tự trong tập văn bản. Phương pháp này sẽ lần lượt thực hiện qua ba bước. Bước đầu tiên là Shingling, đây là bước chuyển đổi tập dữ liệu văn bản và thiết lập đại diện mỗi văn bản một ID, sau đó đến bước Min Hashing chuyển đổi tập hợp dữ liệu lớn thành signatures trong khi vẫn giữ được tính chất đặc trưng ban đầu của tập dữ liệu này. Bước cuối cùng là sử dụng phương pháp Locality-Sensitive Hashing để tập trung so sánh các cặp signatures có thể trùng lắp nhau và cho ra kết quả với xác xuất cao nhất có thể.
  <a name="chD_I_2"></a>
 ### 2. Jaccard Similarity
  Để tiếp cận bài toán này, ta cần tập trung vào khái niệm về sự giống nhau giữa các văn bản trong tập dữ liệu văn bản. Các văn bản sẽ được biểu diễn bởi một mục trong tập dữ liệu văn bản lớn, sự giống nhau giữa các văn bản sẽ phụ thuộc vào giao điểm giữa các mục đó với nhau. Dựa vào đó, ta tìm kiếm các mục gần nhất với một mục mà ta đang xét. Để phân tích loại tương tự này, chúng em sẽ phân tích và khảo sát dựa trên Jaccard Similarity.
  Jaccard Similarity của hai tập hợp tương đương với tỷ lệ giá trị của giao điểm của các tập hợp với giá trị của liên hợp của chúng. Cụ thể, Jaccard của hai bộ T và S sẽ được hiển thị dưới dạng SIM (S, T) và được tính toán thông qua phương trình dưới đây:
		`SIM(S,T) = (|S ᴖ T|)/(|S ᴗ T|)`

 Khía cạnh tương tự giữa các văn bản mà chúng ta đang đề cập ở đây là sự tương đồng về mức độ của các ký tự trong một văn bản chứ không phải là sự giống nhau về ý nghĩa của các văn bản. Đó là lý do tại sao chúng ta nên xét tất cả các ký tự hiện có trong tài liệu. Tìm kiếm điểm tương đồng trong các văn bản có các ứng dụng khác nhau, một số ứng dụng bao gồm tìm kiếm các văn bản lặp lại, các văn bản tương tự nhau và phát hiện đạo văn.
<a name="chD_II"></a>
## II. Phương pháp Shingling 
 Giải pháp hiệu quả nhất để hiển thị tài liệu văn bản dưới dạng tập dữ liệu là chọn các chuỗi từ tài liệu và đặt chúng thành một tập hợp (set). Nếu chúng ta làm như vậy, các tài liệu được chia thành các phần nhỏ của câu hoặc thậm chí các cụm từ sẽ có một số yếu tố chung có thể xuất hiện theo trình tự khác nhau trong hai tài liệu. Sử dụng tính chất của Jaccard Similarity của các tập hợp này, người ta có thể tính toán lượng tương tự của chúng. Như chúng ta đã biết, trong một tài liệu sẽ bao gồm rất nhiều chuỗi, mỗi chuỗi bao gồm các ký tự. Đối với một documents, khái niệm “k-shingle” sẽ là bất kỳ chuỗi con nào có độ dài k đã xuất hiện trong tài liệu. Trong phương pháp này, ta sẽ chọn một bộ k-shingles từ mọi tài liệu đã được lặp lại một lần hoặc nhiều lần bên trong nó và phân bổ nó vào tài liệu.
 Ví dụ, giả sử rằng tài liệu D chứa chuỗi “abcdabd” và giá trị của k tương đương với 2. Khi đó tập hợp 2 shingles cho tài liệu D sẽ là {ab, bc, cd, da, bd}. Lưu ý ở đây là chuỗi con “ab” đã xuất hiện trong tài liệu D tới hai lần, nhưng chỉ được coi là một chuỗi ký tự. 
 Ta có thể chọn bất kỳ giá trị hằng số nào cho k. Nếu giá trị mà chúng ta chọn cho k quá nhỏ, thì một số chuỗi được chọn với độ dài k sẽ tồn tại trong phần lớn tài liệu. Trong trường hợp này, chúng ta sẽ có một tập hợp các shingles có mức độ Shingle Similarity cao. Ví dụ: nếu chúng ta lấy giá trị của k là 1 thì phần lớn các trang web sẽ có nhiều ký tự chung và một số ít ký tự không phổ biến. Trong trường hợp này, tất cả các trang web sẽ có độ tương đồng cao. 
 Thực tế là k của chúng ta lớn đến mức độ nào phụ thuộc vào độ dài của tài liệu và độ lớn của tập hợp các ký tự. Tuy nhiên ở bất kỳ giá trị k nào thì luôn có một điểm quan trọng mà chúng ta cần lưu ý là k nên được chọn đủ lớn để khả năng mọi shingle đều có thể xuất hiện trong tài liệu.
<a name="chD_III"></a>
## III. Phương pháp Minhashing
 Các tập dữ liệu chứa shingles thường có khối lượng rất lớn, nếu chúng ta có hàng triệu tài liệu, có thể việc thiết lập tất cả các shingles trong bộ nhớ chính là điều khó có thể thực hiện và tốn nhiều chi phi bộ nhớ. Mục tiêu chính của phương pháp Minhash này là thay thế các tập dữ liệu lớn bằng các biểu diễn nhỏ được gọi là “signatures”, tức chữ ký theo nghĩa đen. Mục đích ta sử dụng các signatures này là thông qua nó ta có thể so sánh các signatures của hai tập shingles và ước tính Jaccard Similarity của các shingles.
 Để tạo signatures, trước tiên chúng ta cần hiển thị các tập hợp shingles bằng cách sử dụng ma trận. Ma trận này có thể hiển thị tất cả các tập hợp được gọi là ma trận đặc trưng (characteristic matrix). Các cột của ma trận này tương ứng với các tập hợp shingles (ký hiệu là S_i) và các hàng của nó tương ứng với các mục của tập hợp phổ quát (ký hiệu là S) nằm trong số các thành phần của tất cả các tập hợp. Trong hàng r và cột c của ma trận này, giá trị “1” sẽ được đặt nếu phần tử của hàng r thuộc tập dữ liệu của cột c, ngược lại sẽ là giá trị “0”.
Ví dụ minh họa bên dưới cho thấy một ma trận đặc trưng cho các tập S1 = {a, d}, S2 = {c}, S3 = {b, d, e} và S4 = {a, c, d} và tập hợp phổ quát S = {a, b, c, d, e}


		Element	S1	S2	S3	S4
		a	1	0	0	1
		b	0	0	1	0
		c	0	1	0	1
		d	1	0	1	1
		e	0	0	1	0

 Các signatures mà chúng em muốn tạo cho các tập hợp bao gồm các kết quả của một số phép tính và mỗi phép tính này là một minhash của ma trận đặc trưng. Tính toán giá trị của minhash cho một tập hợp được hiển thị trong một cột của ma trận đặc trưng bằng cách chọn một hoán vị của các hàng. Trong trường hợp này, giá trị của minhash trong mỗi cột bằng số của hàng đầu tiên (trong chuỗi hoán vị), trong đó cột có giá trị sẽ là “1.” 
 Ví dụ với ma trận đặc trưng ở trên, giả sử rằng chuỗi các hoán vị là “beadc”. Hoán vị này định nghĩa một hàm minhash (ký hiệu là h) ghi các tập hợp trên các hàng. Có nghĩa là hàm minhash sẽ thay thế các dòng của ma trận đặc trưng đó. Cụ thể sau khi hoán vị đã được áp dụng cho các hàng ma trận đặc trưng trở thành ma trận dưới đây:

		
		Element	S1	S2	S3	S4
		b	0	0	1	0
		e	0	0	1	0
		a	1	0	0	1
		d	1	0	1	1
		c	0	1	0	1

 Sau khi đã hoán vị ma trận đặc trưng, để tính toán minhash của tập hợp S1, chúng em sẽ xét các cột liên quan đến nó và coi hàng đầu tiên có giá trị “1” là giá trị minhash. Do đó h(S1) = a, tương tự ta có h(S2) = c, h(S3) = b và h(S4) = a. 
 Một tính chất quan trọng của phương pháp minhashing là có một mối liên quan giữa phương pháp này với Jaccard Similarity của các tập hợp đã bị minhash. Trong mối quan hệ này, khả năng một minhash cho hai tập hợp trong một hoán vị ngẫu nhiên của các hàng có thể tạo ra các giá trị giống hệt nhau bằng giá trị của Jaccard Similarity trong hai tập hợp này. Có nghĩa là đối với hai bộ S1 và S2, khả năng h (S1) = h (S2) bằng SIM (S1, S2) .
 Sau khi xét phương pháp tính minhash, ta cần tạo một ma trận đặc trưng bằng cách sử dụng một số hàm minhash. Đầu tiên chúng ta chọn n hoán vị ngẫu nhiên của các hàng của ma trận đặc trưng. Các hàm minhash được xác định bằng cách sử dụng các hoán vị h1, h2, ... hn. Với mỗi cột của ma trận đặc trưng cho tập S, chúng ta tính vectơ [h1 (S), h2 (S), ..., hn (S)] và vectơ này là signatures của minhash của cột S. Do đó, chúng ta có thể thay thế signatures minhash của cột “i” bằng cột “i” trong ma trận đặc trưng và biến nó thành ma trận đặc trưng. 
 Điều đặc biệt ở đây là số cột trong ma trận signatures chỉ bằng “n”. Do đó, kích thước của ma trận signatures nhỏ hơn rất nhiều so với kích thước của ma trận đặc trưng. Hơn nữa, với mối quan hệ mà phương pháp minhash so với Jaccard Similarity đã đề cập ở trên, tính tương tự của các tài liệu sẽ được giữ nguyên ở một mức độ nào đó và sẽ không biến mất sau các phép biến đổi này. 
 Trên thực tế, không thể áp dụng hoán vị cho một ma trận đặc trưng lớn. Ngay cả việc chọn một hoán vị ngẫu nhiên từ một số lượng lớn các hàng và sắp xếp các dòng vào thứ tự dựa trên hoán vị cũng đã khá tốn thời gian. Giải pháp tối ưu nhất là ta áp dụng hoán vị cho ma trận bằng cách sử dụng các hàm băm. Thay vì chọn n hoán vị ngẫu nhiên từ các hàng, chúng ta chọn ngẫu nhiên n hàm băm của h1, h2,…, hn từ các hàng. Chúng ta coi SIG (i, c) là một phần tử của ma trận chữ ký cho hàm băm i và cột c. Đầu tiên, chúng ta đặt SIG (i, c) bằng vô cực cho tất cả các chữ “i” và “c”. Để tính toán ma trận signatures, ta duyệt lần lượt từng hàng của ma trận đặc trưng. Có thể minh họa thuật toán này bằng 5 bước trong mã giã dưới đây:
 
![](LSH-for-Finding-Similar-Items/img/minhash_pscode.png)
 
  Từ thuật toán trên ta có thể tính toán ma trận minhash của ma trận đặc trưng trong Bảng 1. Đầu tiên, chúng ta cần chọn n hàm minhash. Đối với ví dụ trong Bảng 1, ta đặt n bằng 2 và chọn hai hàm băm là h1 (x) = x + 1 mod 5 và h2 (x) = 3x + 1 mod 5. Sau đó đánh số thứ tự các hàng từ 0 đến 4. Bảng 3 dưới đây là ma trận đặc trưng cùng với các giá trị được tính cho các hàm băm cho mỗi hàng:
  
			Row	Element	S1	S2	S3	S4	x+1 mod 5	3x+1 mod 5
			0	a	1	0	0	1	1		1
			1	b	0	0	1	0	2		4
			2	c	0	1	0	1	3		2
			3	d	1	0	1	1	4		0
			4	e	0	0	1	0	0		3
 Bây giờ, để tính ma trận signatures có 4 hàng 2 cột, trước tiên chúng ta đặt tất cả các mục của nó bằng ∞ và sau đó thực hiện thuật toán trên ma trận đặc trưng đã cho các giá trị tính được trước đó. Sau khi thực hiện thuật toán và xét tất cả các dòng của ma trận đặc trưng, ma trận minhash signatures sẽ có kết quả như sau:


			Functions	S1	S2	S3	S4
			h1		1	3	0	1
			h2		0	2	0	0
 Theo như đã trình bày ở trên, sự giống nhau của các tập có thể được ước lượng bằng cách nhìn vào ma trận minhash signatures này. Tuy nhiên sự ước lượng này sẽ chính xác khi ma trận minhash signatures quá lớn, ngược lại ước tính sẽ không đủ chính xác cho các ma trận minhash signatures có kích thước rất nhỏ.
<a name="chD_IV"></a>
## IV. Phương pháp Locality Sensitive Hashing
<a name="chD_IV_1"></a>
### 4.1 Phương pháp LSH trong tập tài liệu văn bản
 Khi chúng ta tìm kiếm các thành tương tự, chúng ta không chỉ đơn giản là cố gắng tính toán độ giống nhau của một cặp tài liệu mà là chúng ta so sánh tất cả các tài liệu với nhau sau đó xác định các tài liệu nào tương tự tài liệu nào. Việc xét toàn bộ các cặp tài liệu tốn khá nhiều thời gian. Để giải quyết vấn đề này, chúng ta chỉ nên tập trung xét các cặp có khả nănng giống nhau nhất và hạn chế việc điều tra từng cặp tài liệu với nhau. Giải pháp chung cho tác vụ này là phương pháp LSH (Locality Sensitive Hashing).
 Lý thuyết chung cho phương pháp LSH là chúng ta băm các thành phần nhiều lần sao cho các thành gần giống nhau sẽ băm vào một mảng chung. Sau đó, trong mỗi phép băm, mỗi cặp được kết nối với một nhóm sẽ được coi như một cặp ứng cử viên. Để tìm các tài liệu văn bản tương tự nhau trong tập dữ liệu văn bản, ta chỉ cần xét các cặp ứng cử viên. Trong bài toán phát hiện văn bản giống nhau mà ta đang giải quyết, tất cả các tài liệu văn bản trong tập dữ liệu sẽ không được xét, những tài liệu nào không liên quan sẽ được bỏ qua, từ đó hạn chế được nhiều chi phí cũng như tối ưu hóa được phương pháp giải bài toán.
<a name="chD_IV_2"></a>
### 4.2 Phương pháp LSH cho Minhash Signatures
 Nếu ta có một minhash signatures của các items trong tập dữ liệu, ta có thể áp dụng phương pháp LSH bằng cách chia ma trận signatures thành b dải sao cho mỗi dải bao gồm r hàng (n = br). Sau đó đối với mỗi dải ta sẽ chọn một hàm băm và hàm vectơ này sẽ cung cấp nhiều nhóm sử dụng r số nguyên cùng với một số lượng lớn các hàm băm cho chúng. Chúng ta có thể sử dụng cùng một hàm băm cho tất cả các dải, nhưng chúng ta sử dụng một mảng riêng để băm trong mỗi dải để các cột có vectơ tương tự không được kết nối với cùng một nhóm trong các dải khác nhau. Ví dụ bên dưới cho thấy các thành phần của ma trận signatures bao gồm 12 dòng được chia thành 4 dải và mỗi dải có 3 hàng:

![](LSH-for-Finding-Similar-Items/img/band14.png)
 
 Trong hình trên, cột thứ hai và thứ tư trong dải đầu tiên hiển thị vectơ [0, 2, 1] và chúng được kết nối với cùng một nhóm trong phép băm của dải đầu tiên. Do đó, bất kể thực tế là các cột này có giống nhau ở ba dải khác hay không, cặp cột này có thể được coi là một cặp ứng cử viên. Hơn nữa, hai cột không phù hợp trong dải đầu tiên có cơ hội trở thành một cặp ứng cử viên trong ba dải khác. Trên thực tế, có thể chúng giống nhau ở mỗi nhóm khác. Ở bất kỳ mức độ nào, hai cột cực kỳ giống nhau sẽ giống nhau nhất ở một số dải. Do đó, phương pháp này trực tiếp “match” các cột tương tự lại với nhau với xác suất cao hơn so với các cặp khác nhau.
<a name="chD_IV_3"></a>
### 4.3 Phân tích phương pháp LSH
 Giả sử ta sử dụng b dải, mỗi dải chứa r hàng và giả sử một cặp tài liệu văn bản nào đó có Jaccard Similarity với giá trị là s. Ta biết rằng khả năng các minhash signatures cho các tài liệu văn bản này trong mỗi dòng nhất định của ma trận signatures phù hợp với nhau bằng s. Khả năng các tài liệu này (hoặc tốt nhất là signatures của chúng) là một cặp ứng cử viên có thể được tính toán như sau:

 ![](LSH-for-Finding-Similar-Items/img/text.png)

 ![](LSH-for-Finding-Similar-Items/img/cruve.png)

 Ta có một khái niện mới là “giới hạn ngưỡng” (threshold limit), là số lượng cặp văn bản tương tự trong s khi quá trình tăng dần của biểu đồ đạt đến giới hạn cao nhất. Số lượng giới hạn ngưỡng giúp ta xác định chất lượng của sự phát hiện tương đồng. Chất lượng của sự tương đồng này phụ thuộc vào hai giá trị false positive và false negative. Giới hạn ngưỡng này là một hàm số của b và r và giá trị được tính bằng xấp xỉ t = 〖1/b〗^(1/r) . Ví dụ: nếu b = 16 và r = 4, thì ngưỡng xấp xỉ tại s = 1/2 , vì căn mũ 4 của 1/16 là 1/2.

 Trong phương pháp LSH, có hai khả năng xảy ra là false positive và false negative. Các kết quả false positive là các cặp khác nhau được băm vào cùng một nhóm và false negative là các cặp tương tự không được gửi đến cùng một nhóm. Có nghĩa là các kết quả false positive là các cặp bị nhầm lẫn là một cặp ứng cử viên và các kết quả false negative là các cặp bị nhầm lẫn không được coi là một cặp ứng viên. 
Như đã trình bày ở trên, khả năng hai cặp có Jaccard Similarity của s có thể trở thành một cặp ứng cử viên bằng 1 -〖1- s^r〗^b.  Bản chất bên trong của đồ thị hàm số f(s) = 1 -〖1- s^r〗^b này cho ta biết nếu tính từ 0 đến giới hạn ngưỡng (t) sẽ được kết quả bằng số lượng false positive vì các cặp có Jaccard Similarity nhỏ hơn t sẽ không được coi là cặp ứng viên. Trong hình dưới đây biểu diễn đồ thị của hàm f(s) cùng với các khu vực liên quan đến số lượng false positive và false negative.
<a name="chD_V"></a>
## V. Crawling data từ các đầu báo Việt Nam
 Đây là phần chiếm 2 điểm cộng trong đề tài này, yêu cầu là xây dựng một hệ thống để crawling dữ liệu từ các trang tin tức, lọc bỏ bản tin trùng để xây dựng một trang tin tức tổng hợp.
 Do vấn đề crawling data từ các trang tin tức ở Việt Nam sẽ rất cần thiết cho các bạn học sinh, sinh viên đang nghiên cứu về lĩnh vực Big Data, ngoài ra cấu trúc website của các trang báo thay đổi liên tục đòi hỏi việc update source code mới có crawl dữ liệu nên mình tạo một repo [crawling-VietNam-News](https://github.com/smoothkt4951/crawling-VietNam-News) riêng chứa source và mô tả, mời các bạn ghé thăm, nếu thấy hữu ích hãy cho mình một star để ửng hộ mình nha <3.
 <a name="chD_VI"></a>
## VI. Bài toán Finding Similar Documents 
 Với scripts [crawling-data](https://github.com/smoothkt4951/crawling-VietNam-News) ở trên, bạn có thể crawl bao nhiêu dữ liệu tùy vào độ kiên trì, nhẫn nại của bạn. Để tiết kiệm thời gian, bạn có thể mở cùng lúc nhiều terminal để chạy script đó hoặc chạy song song trên google colab. Mình mới crawl sơ sơ được tầm 22k documents và mục tiêu là tận 100k documents lận, nhưng khi bỏ vào để test với project này thì chỉ dùng tầm 500 documents để tiết kiệm thời gian. Đây là link download dataset để test source code trong projects này: [test_dataset]( https://drive.google.com/file/d/17LrjoXqRtXsK9P9McO1iuXbX0usuRwY9/view?usp=sharing). 
 
 Sau khi đã crawl dữ liệu từ các trang báo, bạn có thể đưa vào một đoạn text trong một bài báo bất kỳ nào đó, chương trình sẽ trả về cho bạn một bài báo có contents tương tự với đoạn text của bạn. Mục đích của việc này là tránh sự trùng lắp, đạo văn giữa các bài báo với nhau, từ đó tổng hợp thành một trang báo tương tự trang baomoi.com tổng hợp tất cả các bài báo từ nhiều nguồn báo trong nước.
 
 Đây là file ipynb chứa source code giải bài toán tìm kiếm văn bản tương tự trong tập văn bản lớn: [colab-not_using_pyspark_mini.ipynb](LSH-for-Finding-Similar-Items/main/colab-not_using_pyspark_mini.ipynb). Code này giải quyết bài toán theo 3 bước như đã trình bày trong phần lý thuyết, tuy nhiên để cải thiện tốc độ xử lý thì nhóm mình lập trình song song trên môi trường Pyspark, kết quả được lưu trong file [colab-not_using_pyspark_mini.ipynb](LSH-for-Finding-Similar-Items/main/colab-using_pyspark.ipynb). Trong các file ipynb này trình bày được các kết quả qua từng bước chạy thuật toán nên mình sẽ không trình bày dài dòng ở đây, mời bạn đọc xem qua thành quả của nhóm mình nha.
