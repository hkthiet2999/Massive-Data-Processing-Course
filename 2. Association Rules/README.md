# Mục lục
  - [I. Tổng quan](#chC_I)
      - [1. Các khái niệm cơ bản](#chC_I_1)
  - [II. Bài toán Association Rules với store datasets](#chC_II)
      - [1. Quy trình khai thác luật kết hợp](#chC_II_1)
      - [2. Ứng dụng minh họa với strore datasets](#chC_II_2)

# Thuật toán A-priori trên môi trường PySpark và bài toán Association Rule

<a name="chC_I"></a>

## I. Tổng quan 

 Thuật toán Apriori được công bố bởi R. Agrawal và R. Srikant vào năm 1994 vì để tìm các tập phổ biến trong một bộ dữ liệu lớn. Tên của thuật toán là Apriori vì nó sử dụng kiến thức đã có từ trước (prior) về các thuộc tính, vật phẩm thường xuyên xuất hiện trong cơ sở dữ liệu. Để cải thiện hiệu quả của việc lọc các mục thường xuyên theo cấp độ, một thuộc tính quan trọng được sử dụng gọi là thuộc tính Apriori giúp giảm phạm vi tìm kiếm của thuật toán.
 <a name="chC_I_1"></a>
### 1. Các khái niệm cơ bản
 Để minh họa cho các khái niệm, ta lấy ví dụ CSDL với các giao dịch sau. 
| TID (mã giao dịch) | Itemset (tập các hạng mục) |
|---|---|
| 1 | A, B, E |
| 2 | B, D |
| 3 | B, C |
| 4 | A, B, D |
| 5 | A, C |
| 6 | B, C |
| 7 | A, C |
|8 | A, B, C, E |
| 9 | A, B, C |

- Hạng mục (item): mặt hàng A = apple, B = bread, C = cereal, D = donuts, E = eggs.
- Tập các hạng mục (itemset): danh sách các hạng mục trong giỏ hàng như {A, B, C, D, E}.
- Giao dịch (transaction): tập các hạng mục được mua trong một giỏ hàng, lưu kèm với mã giao dịch (TID).
- Mẫu phổ biến (frequent item): là mẫu xuất hiện thường xuyên trong tập dữ liệu như {A, C} xuất hiện khá nhiều trong các giao dịch.
- Tập k-hạng mục (k-itemset): ví dụ danh sách sản phẩm (1-itemset) như {A, B, C}, danh sách cặp sản phẩm đi kèm (2-itemset) như {{A, B}, {A, C}}, danh sách 3 sản phẩm đi kèm (3-itemset) như {{A, B, C}, {B, C, E}}.
- Độ phổ biến (support): được tính bằng supp(X) = \frac{count(X)}{|D|}. X = {B, C} là tập các hạng mục, D là cơ sở dữ liệu (CSDL) giao dịch.
- Tập phổ biến (frequent itemset): là tập các hạng mục S (itemset) thỏa mãn độ phổ biến tối thiểu (minsupp – do người dùng xác định như 40% hoặc xuất hiện 5 lần). Nếu supp(S) \geq minsupp thì S là tập phổ biến.
- Tập phổ biến tối đại (max pattern) thỏa supp(X) \geq minsupp không tồn tại |X’| > |X|, với X’ cũng phổ biến
- Tập phổ biến đóng (closed pattern) thỏa supp(S) \geq minsupp không tồn tại |X’| > |X| mà supp(X’) = supp(X)
- Luật kết hợp (association rule): kí hiệu X \rightarrow Y, nghĩa là khi X có mặt thì Y cũng có mặt (với xác suất nào đó). Ví dụ, A \rightarrow B; A,B \rightarrow C; B,D \rightarrow E.
- Độ tin cậy (confidence): được tính bằng conf(X) = supp(X+Y)/supp(X)
Thông thường người ta sẽ biến đổi CSDL về dạng nhị phân để dễ tính toán.

<a name="chC_II"></a>
## II. Bài toán Association Rules với store datasets
<a name="chC_II_1"></a>
### 1. Quy trình khai thác luật kết hợp
Giả sử độ phổ biến tối thiểu (minsupp) và độ tin cậy tối thiểu (minconf) do người dùng xác định.

Cho tập các hạng mục I và CSDL giao dịch D. Bài toán khai thác luật kết hợp là bài toán tìm tất cả các luật dạng X -> Y ( với X, Y là tập con của I và X giao Y = {}) thỏa mãn độ phổ biến và độ tin cậy tối thiểu: supp(X -> Y) >= minsupp() và conf(X -> Y) >= minconf().

Dưới đây là quy trình khai thác luật kết hợp cơ bản nhất:
 - Bước 1: Tìm tất cả các tập phổ biến (theo ngưỡng minsupp).
 - Bước 2: Xây dựng luật từ các tập phổ biến

    + Đối với mỗi tập phổ biến S, tạo ra tất cả các tập con khác rỗng của S.
    
    + Đối với mỗi tập con khác rỗng A của S (|A| < |S|). Luật A -> (S - A) là luật kết hợp cần tìm nếu: conf (A -> (S - A)) = supp(S)/supp(A) >= minconf
    
Từ bài toán khai thác luật kết hợp chuyển thành bài toán khai thác tập phổ biến : độ phức tạp tính toán cao.
 <a name="chC_II_2"></a>
### 2. Ứng dụng minh họa với strore datasets
Trong repo này của mình có source code minh họa bài toán tìm Association Rule sử dụng thuật toán A-priori trên môi trường PySpark với cơ sở dữ liệu [store_data.csv](https://drive.google.com/file/d/1y5DYn0dGoSbC22xowBq2d4po6h1JxcTQ/view?usp=sharing). 
Bài toán này sử dụng thuật toán A-priori trên store datasets để khai thác và xây dựng các luật kết hợp.Đây là link file [Jupyter Notebook](https://github.com/smoothkt4951/Massive-Data-Processing-Course/blob/main/A-priori/A_priori.ipynb) và link [Google Colab](https://colab.research.google.com/drive/1WTDOtKL3DR66tBmM_AtKvi_vtJWtCb--?usp=sharing) của mình, trong đó có giải thích rất rõ từng bước thực hiện và kết quả cùng với chức năng của từng hàm nên mình sẽ không trình bày dài dòng ở đây, mời bạn đọc ghé qua để có cái nhìn cụ thể hơn những gì mình minh họa nhé.

## Tham khảo
1. [online] Available at:  http://www.kdnuggets.com/2016/04/association-rules-apriori-algorithm-tutorial.html [Accessed 21 February 2021]
