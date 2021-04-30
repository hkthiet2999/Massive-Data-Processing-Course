<a name="chC"></a>

# C. Thuật toán A_priori trên môi trường PySpark và bài toán Association Rule

<a name="chC_I"></a>

## I. Tổng quan 

 Thuật toán Apriori được công bố bởi R. Agrawal và R. Srikant vào năm 1994 vì để tìm các tập phổ biến trong một bộ dữ liệu lớn. Tên của thuật toán là Apriori vì nó sử dụng kiến thức đã có từ trước (prior) về các thuộc tính, vật phẩm thường xuyên xuất hiện trong cơ sở dữ liệu. Để cải thiện hiệu quả của việc lọc các mục thường xuyên theo cấp độ, một thuộc tính quan trọng được sử dụng gọi là thuộc tính Apriori giúp giảm phạm vi tìm kiếm của thuật toán.
 Để minh họa cho các khái niệm, ta lấy ví dụ CSDL với các giao dịch sau. | TID (mã giao dịch) | Itemset (tập các hạng mục) | 1 | A, B, E 2 | B, D 3 | B, C 4 | A, B, D 5 | A, C 6 | B, C 7 | A, C 8 | A, B, C, E 9 | A, B, C

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
- Độ tin cậy (confidence): được tính bằng conf(X) = \frac{supp(X+Y)}{supp(X)}.
<a name="chC_II"></a>
## II. Bài toán Association Rules với cơ sở dữ liệu store_data
Trong repo này của mình có code minh họa bài toán tìm Association Rule sử dụng thuật toán A-priori trên môi trường PySpark với cơ sở dữ liệu [store_data.csv](https://drive.google.com/file/d/1y5DYn0dGoSbC22xowBq2d4po6h1JxcTQ/view?usp=sharing). Đây là link file [Jupyter Notebook](https://github.com/smoothkt4951/Massive-Data-Processing-Course/blob/main/A-priori/A_priori.ipynb) và link [Google Colab](https://colab.research.google.com/drive/1WTDOtKL3DR66tBmM_AtKvi_vtJWtCb--?usp=sharing) của mình, trong đó có giải thích rất rõ từng bước thực hiện và kết quả cùng với công dụng của từng hàm nên mình tránh trình bày dài dòng ở đây, mời bạn đọc ghé qua để xem mình code những gì trong đó nha.
<a name="chD"></a>