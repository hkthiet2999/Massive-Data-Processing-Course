# Xử Lý Dữ Liệu Lớn
## Giới thiệu
  Bài viết này được tạo ra với mục đích ghi lại toàn bộ quá trình học tập, nghiên cứu về Xử Lý Dữ Liệu Lớn của mình, qua đó tổng hợp lại các kiến thức từ tổng quan đến chi tiết những gì mình học tập, nghiên cứu và thực hành được. Trong mỗi phần mình trình bày về lý thuyết sẽ có các phần code demo, phần demo sẽ được code bằng ngôn ngữ Python3, lưu file với đuôi là .ipynb. 

## Mục lục
- [A.Trình bày tổng quan về Apache Spark và Mapreduce](#chA)
  - [I. Tổng quan về Apache Spark](#I.-tong-quan-ve-apache-spark)
    - [ 1. Giới thiệu](#1.-gioi-thieu)
    - [ 2. Các thành phần của Apache Spark](#2.-cac-thanh-phan-cua-apache-spark)
    - [ 3. Những tính năng nổi bật](#3.-nhung-tinh-nang-noi-bat)
    - [ 4. Quản lý bộ nhớ của Apache Spark](#4.-quan-ly-bo-nho-cua-apache-spark)
  - [II. Tổng quan về Mapreduce](#II.-tong-quan-ve-mapreduce)
    - [ 1. Hadoop và Mapreduce](#1.-hadoop-mapreduce)
    - [ 2. Mapreduce và các thủ tục chính](#2.-mapreduce-va-cac-thu-tuc-chinh)
    - [ 3. Hàm Map và Reduce](#3.-ham-map-va-mapreduce)
    - [ 4. Nguyên tắc hoạt động ](#4.-nguyen-tac-hoat-dong)
    - [ 5. Các bước hoạt động của MapReduce](#5.-cac-buoc-hoat-dong)
    - [ 6. Luồng dữ liệu nền tảng của Mapreduce](#6.-luong-du-lieu)
    - [ 7. Ứng dụng của Mapreduce](#7.-ung-dung)
  - [III. Ví dụ minh họa](#vd)
- [B.Trình bày tổng quan về Spark Properties, Spark RDDs và Spark Dataframes](#chB)
  - [I. Spark properties](#chB_I)
    - [ 1. Tổng quan](#chB_I_1)
    - [ 2. Tải động đối với các thuộc tính của Spark](#chB_I_2)
    - [ 3. Tổng hợp và phân loại các thuộc tính trong Spark](#chB_I_3)
  - [II. Spark RDD](#chB_II)
     - [1. Tổng quan](#chB_II_1)
     - [2. Cài đặt Spark](#chB_II_2)
     - [3. Khởi tạo Spark](#chB_II_3)
     - [4. Resilient Distributed Datasets (RDDs)](#chB_II_4)
   - [III. Spark DataFrames](#chB_III)
     - [1. Tổng quan](#chB_III_1)
     - [2. Sử dụng DataFrames bổ sung cho RDD trong Spark](#chB_III_2)
     - [3. Tính năng chính của DataFrames](#chB_III_3)
     - [4. Khởi tạo DataFrames](#chB_III_4)
     - [5. Làm việc với DataFrames](#chB_III_5)
- [Tham khảo](#refer)
<a name="chA"></a>
# A.Trình bày tổng quan về Apache Spark và Mapreduce
<a name="I.-tong-quan-ve-apache-spark"></a>
## I. Tổng quan về Apache Spark
<a name="1.-gioi-thieu"></a>
### 1. Giới thiệu
  Apache Spark là một open source cluster computing framework được phát triển vào năm 2009 bởi AMPLab tại đại học California. Sau này, Spark đã được truyền lại cho Apache Software Foundation vào năm 2013 và được phát triển cho đến nay.

  Apache Spark  cho phép xây dựng các predition model ( mô hình dự đoán) nhanh chóng với việc tính toán được thực hiện trên một nhóm các máy tính, nó có thể tính toán cùng lúc trên toàn bộ tập dữ liệu mà không cần phải trích xuất mẫu tính toán thử nghiệm. Tốc độ xử lý của Spark dựa trên việc tính toán được thực hiện cùng lúc trên nhiều máy khác nhau. Đồng thời việc tính toán được thực hiện ở bộ nhớ trong (in-memories) hay thực hiện hoàn toàn trên RAM.
 
 <a name="2.-cac-thanh-phan-cua-apache-spark"></a>
 ### 2. Các thành phần của Apache Spark
  Matei Zaharia là cha đẻ của Spark, ông này sử dụng Hadoop từ những ngày đầu phát triển spark. Đến năm 2009 ông viết Apache Spark để giải quyết những bài toán học máy ở đại học UC Berkely vì Hadoop MapReduce hoạt động không hiệu quả cho những bài toán này. Sau đó ông nhận ra rằng Spark không chỉ hữu ích cho học máy mà còn cho cả việc xử lý luồng dữ liệu hoàn chỉnh.
  
   ![](apache_spark_and_mapreduce/core.jpg)
   
  Thành phần cơ bản của Spark là Spark Core: cung cấp những chức năng cơ bản nhất của Spark như lập lịch cho các tác vụ, quản lý bộ nhớ, fault recovery, tương tác với các hệ thống lưu trữ…Đặc biệt, Spark Core cung cấp API để định nghĩa RDD (Resilient Distributed DataSet) là tập hợp của các item được phân tán trên các node của cluster và có thể được xử lý song song.
  
  Spark có thể chạy trên nhiều loại Cluster Managers như Hadoop YARN, Apache Mesos hoặc trên chính cluster manager được cung cấp bởi Spark được gọi là Standalone Scheduler.
  
  Spark SQL cho phép truy vấn dữ liệu cấu trúc qua các câu lệnh SQL. Spark SQL có thể thao tác với nhiều nguồn dữ liệu như Hive tables, Parquet, và JSON.
  
  Spark Streaming cung cấp API để dễ dàng xử lý dữ liệu stream
MLlib Cung cấp rất nhiều thuật toán của học máy như: classification, regression, clustering, collaborative filtering… Còn GraphX là thư viện để xử lý đồ thị.
<a name="3.-nhung-tinh-nang-noi-bat"></a>
### 3. Những tính năng nổi bật
- “Spark as a Service”: Giao diện REST để quản lí (submit, start, stop, xem trạng thái) spark job, spark context
- Tăng tốc, giảm độ trễ thực thi job xuống mức chỉ tính bằng giây bằng cách tạo sẵn spark context cho các job dùng chung.
- Stop job đang chạy bằng cách stop spark context
- Bỏ bước upload gói jar lúc start job làm cho job được start nhanh hơn.
- Cung cấp hai cơ chế chạy job đồng bộ và bất đồng bộ
- Cho phép cache RDD theo tên , tăng tính chia sẻ và sử dụng lại RDD giữa các job
- Hỗ trợ viết spark job bằng cú pháp SQL
- Dễ dàng tích hợp với các công cụ báo cáo như: Business Intelligence, Analytics, Data Integration Tools
<a name="4.-quan-ly-bo-nho-cua-apache-spark"></a>
### 4. Quản lý bộ nhớ của Apache Spark
  Spark giải quyết các vấn đề vấn đề xung quanh định nghĩa Resilient Distributed Datasets (RDDs). RDDs hỗ trợ hai kiểu thao tác thao tác: transformations và action. Thao tác chuyển đổi(tranformation) tạo ra dataset từ dữ liệu có sẵn. Thao tác actions trả về giá trị cho chương trình điều khiển (driver program) sau khi thực hiện tính toán trên dataset.

  Spark thực hiện đưa các thao tác RDD chuyển đổi vào DAG (Directed Acyclic Graph) và bắt đầu thực hiện. Khi một action được gọi trên RDD, Spark sẽ tạo DAG và chuyển cho DAG scheduler. DAG scheduler chia các thao tác thành các nhóm (stage) khác nhau của các task. Mỗi Stage bao gồm các task dựa trên phân vùng của dữ liệu đầu vào có thể pipline với nhau và có thể thực hiện một cách độc lập trên một máy worker. DAG scheduler sắp xếp các thao tác phù hợp với quá trình thực hiện theo thời gian sao cho tối ưu nhất. Ví dụ: các thao tác map sẽ được đưa vào cùng một stage do không xảy ra shuffle dữ liệu giữa các stage. Kết quả cuối cùng của DAG scheduler là một tập các stage. Các Stages được chuyển cho Task Scheduler. Task Scheduler sẽ chạy các task thông qua cluster manager (Spark Standalone/Yarn/Mesos). Task scheduler không biết về sự phụ thuộc của các stages. Nó chỉ chịu trách nhiệm thực hiện sắp xếp các task một cách tối ưu nhất.
  
  Mỗi Worker bao gồm một hoặc nhiều Excuter. Các excuter chịu trách nhiệm thực hiện các task trên các luồng riêng biệt. Việc chia nhỏ các task giúp đem lại hiệu năng cao hơn, giảm thiểu ảnh hưởng của dữ liệu không đối xứng (kích thước các file không đồng đều).
Quá trình Spark xây dựng DAG: Có hai kiểu chuyển đổi có thể áp dụng trên các RDDs đó là chuyển đổi hẹp và chuyển đổi rộng:

– Chuyển đổi hẹp: không yêu cầu xáo trộn dữ liệu vượt qua các phân vùng (partition).Ví dụ như các thao tác map, filter,..

– Chuyển đổi rộng yêu cầu dữ liệu phải xáo trộn. Ví dụ: reduceByKey, sortByKey, groupByKey,…
  
  Spark truy cập dữ liệu được lưu trữ ở các nguồn khác nhau như: HDFS, Local Disk, RAM. Cache Manager sử dụng Block Manager để quản lý dữ liệu. Cache Manager quản lý dữ liệu nào được Cache trên RAM, thông thường là dữ liệu được sử dụng thường xuyên nhất, nó cũng có thể được xác định thủ công bằng tay sử dụng phương thức persit hoặc cache. Nếu kích thước RAM không đủ chứa dữ liệu thì dữ liệu sẽ được lưu trữ sang Tachyon và cuối cùng là lưu trữ lên đĩa. Khi dữ liệu(RDD) không được lưu trữ trên RAM, khi có nhu cầu sử dụng đến, chúng sẽ được recompute lại. Nó sử dụng một khái niệm là “storage level” để quản lý cấp độ của lưu trữ của dữ liệu.
  
<a name="II.-tong-quan-ve-mapreduce"></a>
## II. Tổng quan về Mapreduce
<a name="1.-hadoop-mapreduce"></a>
### 1. Hadoop và Mapreduce
  Hadoop là một framwork giúp lưu trữ và xử lý Big Data áp dụng MapReduce. Nói đơn giản cách khác nó sẽ giúp sắp xếp dữ liệu sao cho user có thể dễ dàng sử dụng nhất.

  MapReduce được Google tạo ra ban đầu để xử lý đống dữ liệu lớn của công ty họ. Ta còn có thể gọi phương pháp này là Phân tán dữ liệu vì nó tách hết tập hợp các dữ liệu ban đầu thành các dữ liệu nhỏ và sắp xếp lại chúng để dễ dàng tìm kiếm và truy xuất hơn, đặc biệt là việc truy xuất các dữ liệu tương đồng. Ví dụ thường thấy nhất là các đề xuất mà ta hay thấy ở Google tìm kiếm

  Như vậy mô hình lập trình Map Reduce là nền tảng ý tưởng của Hadoop. Bản thân Hadoop là một framework cho phép phát triển các ứng dụng phân tán phần cứng thông thường . Các phần cứng này thường có khả năng hỏng hóc cao. Khác với loại phần cứng chuyên dụng đắt tiền, khả năng xảy ra lỗi thấp như các supermicrocomputer chẳng hạn.

  Hadoop được viết bằng Java. Tuy nhiên, nhờ cơ chế streaming, Hadoop cho phép phát triển các ứng dụng phân tán bằng cả java lẫn một số ngôn ngữ lập trình khác như C++, Python, Pearl.
<a name="2.-mapreduce-va-cac-thu-tuc-chinh"></a>
### 2. Mapreduce và các thủ tục chính
  MapReduce là mô hình được thiết kế độc quyền bởi Google, nó có khả năng lập trình xử lý các tập dữ liệu lớn song song và phân tán thuật toán trên 1 cụm máy tính.
  
  MapReduce sẽ  bao gồm những thủ tục sau: thủ tục 1 Map() và 1 Reduce(). Thủ tục Map() bao gồm lọc (filter) và phân loại (sort) trên dữ liệu khi thủ tục khi thủ tục Reduce() thực hiện quá trình tổng hợp dữ liệu. Đây là mô hình dựa vào các khái niệm biển đối của bản đồ và reduce những chức năng lập trình theo hướng chức năng. Thư viện của thủ tục Map() và Reduce() sẽ được viết bằng nhiều loại ngôn ngữ khác nhau. Thủ tục được cài đặt miễn phí và được sử dụng phổ biến nhất là là Apache Hadoop.
<a name="3.-ham-map-va-mapreduce"></a>
### 3. Hàm Map và Reduce
  MapReduce có 2 hàm chính là Map() và Reduce(), đây là 2 hàm đã được định nghĩa bởi người dùng và nó cũng chính là 2 giai đoạn liên tiếp trong quá trình xử lý dữ liệu của MapReduce. Nhiệm vụ cụ thể của từng hàm như sau: 
  
    - Hàm Map(): Có nhiệm vụ nhận Input cho các cặp giá trị/  khóa và output chính là tập những cặp giá trị/khóa trung gian. Sau đó, chỉ cần ghi xuống đĩa cứng và tiến hành thông báo cho các hàm Reduce() để trực tiếp nhận dữ liệu. 
    
    - Hàm Reduce(): Có nhiệm vụ tiếp nhận từ khóa trung gian và những giá trị tương ứng với lượng từ khóa đó. Sau đó, tiến hành ghép chúng lại để có thể tạo thành một tập khóa khác nhau. Các cặp khóa/giá trị này thường sẽ thông qua một con trỏ vị trí để đưa vào các hàm reduce. Quá trình này sẽ giúp cho lập trình viên quản lý dễ dàng hơn một lượng danh sách cũng như  phân bổ giá trị sao cho  phù hợp nhất với bộ nhớ hệ thống. 
    
Ở giữa Map và Reduce thì còn 1 bước trung gian đó chính là Shuffle. Sau khi Map hoàn thành  xong công việc của mình thì Shuffle sẽ làm nhiệm vụ chính là thu thập cũng như tổng hợp từ khóa/giá trị trung gian đã được map sinh ra trước đó rồi chuyển qua cho Reduce tiếp tục xử lý.
<a name="4.-nguyen-tac-hoat-dong"></a>
### 4. Nguyên tắc hoạt động 
Mapreduce hoạt động dựa vào nguyên tắc chính là “Chia để trị”, như sau:
- Phân chia các dữ liệu cần xử lý thành nhiều phần nhỏ trước khi thực hiện. 
- Xử lý các vấn đề nhỏ theo phương thức song song trên các máy tính rồi phân tán hoạt động theo hướng độc lập.
Tiến hành tổng hợp những kết quả thu được để đề ra được kết quả sau cùng. 
<a name="5.-cac-buoc-hoat-dong"></a>
### 5. Các bước hoạt động của MapReduce
- Bước 1: Tiến hành chuẩn bị các dữ liệu đầu vào để cho Map() có thể xử lý.
- Bước 2: Lập trình viên thực thi các mã Map() để xử  lý. 
- Bước 3: Tiến hành trộn lẫn các dữ liệu được xuất ra bởi Map() vào trong Reduce Processor
- Bước 4: Tiến hành thực thi tiếp mã Reduce() để có thể xử lý tiếp các dữ liệu cần thiết.  
- Bước 5: Thực hiện tạo các dữ liệu xuất ra cuối cùng. 
<a name="6.-luong-du-lieu"></a>
### 6. Luồng dữ liệu nền tảng của Mapreduce
- Input Reader
- Map Function
- Partition Function
- Compare Function
- Reduce Function
- Output Writer
<a name="7.-ung-dung"></a>
### 7. Ứng dụng của Mapreduce
MapReduce được ứng dụng cho việc thống kê hàng loạt những số liệu cụ thể như sau: 
- Thực hiện thống kê cho các từ khóa được xuất hiện ở trong các tài liệu, bài viết, văn bản hoặc được cập nhật trên hệ thống fanpage, website,…
- Khi số lượng các bài viết đã được thống kê thì tài liệu sẽ có chứa các từ khóa đó. 
- Thống kê được những câu lệnh match, pattern bên trong các tài liệu đó
- Khi thống kê được số lượng các URLs có xuất hiện bên trong một webpages. 
- Thống kê được các lượt truy cập của khách hàng sao cho nó có thể tương ứng với các URLs.
- Thống kê được tất cả từ khóa có trên website, hostname,…
<a name="vd"></a>
## Ví dụ minh họa
Trong phần ví dụ minh họa này, mình đã tạo một Project (homework01) trên Google Colab dùng thư viện Spark đọc vào một file văn bản (input.txt) và đếm số từ trên file văn bản, sau đó lọc ra k từ có tần suất xuất hiện nhiều nhất. Đây là đường link đến project của mình : [homework01](https://colab.research.google.com/drive/1ooNYc_wXl-0V3JwhQ8F62oXW6ab-986v#scrollTo=Ny0esGJZpT4o)

Quá trình xử lý bài toán này bằng Map-Redue sử dụng thư viện Spark như sau:

![](apache_spark_and_mapreduce/wc.jpg)

Đối với hàm Map:
  - Input là 1 đoạn văn bản
  - Output là các cặp <word, 1>
Hàm Map được thực hiện song song để xử lý các tập dữ liệu khác nhau.

Đối Với hàm Reduce:

  - Input có dạng <word, [list]>, trong đó list là tập hợp các giá trị đếm được của mỗi từ
  - Output: <word, tổng số lần xuất hiện của từ>
Hàm Reduce cũng được chạy song song để xử lý các tập từ khoá khác nhau.

Giữa hàm Map và Reduce có một giai đoạn xử lý trung gian gọi là hàm Shuffle. Hàm này có nhiệm vụ sắp xếp các từ và tổng hợp dữ liệu đầu vào cho Reduce từ các kết quả đầu ra của hàm Map.

Để hiện thực bài toán, bước đầu tiên cần phải import các thư viện cần thiết và khởi tạo project của spark như sau:

![](apache_spark_and_mapreduce/contructor.png)

Bước tiếp theo trong project này, mình tiến hành đọc file input và đồng thời split các khoảng trắng bị thừa. Sau đó mình xử lý bớt các ký tự đặc biệt và chuyển các ký tự in hoa thành in thường. Ví dụ như có trường hợp chữ 'KIENTHIET' và 'kienthiet' có trong file text, sau khi xử lý qua hàm lower_clean_str bên dưới, cả hai ký tự trên đều sẽ được tính là 1 ký tự.

![](apache_spark_and_mapreduce/read_clean_input.png)

Đoạn code trên mình cũng thực hiện qua bước 'Map' như đã trình bày ở phần lý thuyết, chỉ cần gọi hàm map() có trong spark và truyền vào content của file text mình đã clean như trên.

Sau khi thực hiện bước map, mình thực hiện tiếp bước reduce. Ở đây mình sử dụng hàm reduce() để tính tổng các ký tự đã map. Hàm reduce này ngoài chức năng tính tổng ký tự còn có thể tìm max, min các ký tự trong map. Còn hàm reduceByKey() dùng để đếm tần số xuất hiện các từ có trong map.

![](apache_spark_and_mapreduce/reduce.png)

Kết quả dưới đây thể hiện tần suất xuất hiện của 20 ký tự đầu tiên có trong file input.txt:


![](apache_spark_and_mapreduce/output.png)

<a name="chB"></a>

# B. Trình bày tổng quan về Spark Properties, Spark RDDs và Spark Dataframes

<a name="chB_I"></a>

## I. Spark Properties

<a name="chB_I_1"></a>

### 1. Tổng quan
  Spark cung cấp ba cấu trúc để cấu hình hệ thống:
  
   - Spark properties: Kiểm soát hầu hết các tham số ứng dụng và có thể được đặt bằng cách sử dụng đối tượng SparkConf hoặc thông qua các thuộc tính hệ thống Java.
   - Environment variables: Các biến môi trường có thể được sử dụng để đặt cài đặt cho mỗi máy, chẳng hạn như địa chỉ IP, thông qua tập lệnh conf / spark-env.sh trên mỗi nút.
   - Logging: Có thể được định cấu hình thông qua log4j.properties.

  Trong phần đầu tiên của bài viết này, ta sẽ tìm hiểu về cấu trúc cấu hình hệ thống của Spark, được gọi là Spark Properties.

  Spark properties có nhiệm vụ kiểm soát hầu hết các cài đặt ứng dụng và được cấu hình riêng cho từng ứng dụng sử dụng Spark. Các thuộc tính này có thể được đặt trực tiếp trên SparkConf và được chuyển tới SparkContext. SparkConf cho phép người dùng định nghĩa cấu hình một số thuộc tính phổ biến (ví dụ: URL chính và appname - tên ứng dụng), cũng như các cặp key-values tùy ý thông qua phương thức set (). Lấy ví dụ đối với chương trình wordCount, chúng ta có thể khởi tạo một ứng dụng với đơn luồng như sau:

![](spark-properties_RDDs_DataFrames/vd1.png)

  Các properties chỉ định khoảng thời gian hay kích thước byte phải được cấu hình với một đơn vị thời gian hay đơn vị kích thước trong phạm vi cho phép sử dụng của Spark. Những định dạng về đơn vị kích thước và đơn vị thời gian sau được chấp nhận:

![](spark-properties_RDDs_DataFrames/donvi.png)
<a name="chB_I_2"></a>
### 2. Tải động đối với các thuộc tính của Spark
  Trong một số trường hợp, bạn có thể muốn tránh mã hóa cứng các cấu hình nhất định trong SparkConf. Ví dụ: Nếu bạn muốn chạy cùng một ứng dụng với các bản chính khác nhau hoặc số lượng bộ nhớ khác nhau. Spark cho phép bạn chỉ cần tạo một conf trống như sau:
  
  ![](spark-properties_RDDs_DataFrames/emptyConf.png)
  
  Khi đó bạn vẫn có thể cung cấp các giá trị cấu hình trong quá trình runtime bằng cách setting trực tiếp trên command line:
  
  ![](spark-properties_RDDs_DataFrames/cmLine.png)
  
   Spark shell và spark-submit tool hỗ trợ hai cách để tải cấu hình động. Đầu tiên là các tùy chọn dòng lệnh, chẳng hạn như --master, như hình trên. spark-submit có thể chấp nhận bất kỳ thuộc tính Spark nào sử dụng cờ --conf / -c, nhưng sử dụng cờ đặc biệt cho các thuộc tính đóng một vai trò trong việc khởi chạy ứng dụng Spark. Chạy ./bin/spark-submit --help sẽ hiển thị toàn bộ danh sách các tùy chọn này.

  Trong khi đó, bin / spark-submit cũng sẽ đọc các tùy chọn cấu hình từ conf / spark-defaults.conf, trong đó mỗi dòng bao gồm một khóa và một giá trị được phân tách bằng khoảng trắng. Ví dụ:
  
  ![](spark-properties_RDDs_DataFrames/example02.png)
  
  Mọi giá trị được chỉ định dưới dạng flags hoặc properties file sẽ được chuyển đến ứng dụng và được hợp nhất với những giá trị được chỉ định thông qua SparkConf. Các thuộc tính được đặt trực tiếp trên SparkConf được ưu tiên cao nhất, sau đó các flags được chuyển đến spark-submit hoặc spark-shell, sau đó sẽ là các tùy chọn trong tệp spark-defaults.conf. Một vài khóa cấu hình đã được đổi tên kể từ các phiên bản Spark trước đó; tron trường hợp đó, các tên khóa cũ hơn vẫn được chấp nhận nhưng với sự ưu tiên thấp hơn bất kỳ trường hợp nào của khóa mới hơn.

  Các thuộc tính của Spark chủ yếu có thể được chia thành hai loại: một là liên quan đến triển khai, như “spark.driver.memory”, “spark.executor.instances”, loại thuộc tính này có thể không bị ảnh hưởng khi thiết lập lập trình thông qua SparkConf trong thời gian chạy, hoặc hành vi tùy thuộc vào trình quản lý cụm và chế độ triển khai bạn chọn, vì vậy bạn nên đặt thông qua tệp cấu hình hoặc tùy chọn dòng lệnh spark-submit. Một loại khác chủ yếu liên quan đến kiểm soát thời gian chạy Spark, như “spark.task.maxFailures”, loại thuộc tính này có thể được đặt theo một trong hai cách.
  
<a name="chB_I_3"></a>
### 3. Tổng hợp và phân loại các thuộc tính trong Spark
  
  Trang web http://driver:4040 liệt kê các thuộc tính Spark trong tab "Environment". Đây là một nơi hữu ích để kiểm tra và đảm bảo rằng các thuộc tính của bạn đã được sử dụng chính xác. Lưu ý rằng chỉ các giá trị được chỉ định rõ ràng thông qua spark-defaults.conf, SparkConf hoặc dòng lệnh mới xuất hiện. Đối với tất cả các thuộc tính cấu hình khác, bạn có thể cho rằng giá trị mặc định đang được sử dụng.
  
  Có rất nhiều Spark Properties, tùy vào mục đích sử dụng riêng biệt mà các các Spar properties khác nhau. Các bạn có thể truy cập đường link sau để có thể tra cứu đầy đủ và chi tiết nhất từng loại thuộc tính cũng như cách sử dụng và các giá trị mặc định : https://spark.apache.org/docs/latest/configuration.html#compression-and-serialization. Trong bài viết này chỉ để cập đến một số thuộc tính phổ biến và phân loại các thuộc tính đó dựa trên mục đích sử dụng như sau:
  
   - Application Properties: Hầu hết các thuộc tính kiểm soát cài đặt nội bộ đều có giá trị mặc định sẵn. Một số tùy chọn phổ biến nhất có thể kể đến là spark.app.namem spark.driver.cores, spark.driver.maxResultSize, spark.driver.memory.
   - Runtime Environment: Các thuộc tính này thường được sử dụng trong quá trình runtime để xử lý các biến môi trường. Một số properties phổ biến như spark.driver.extraClassPath, spark.driver.defaultJavaOptions, spark.driver.userClassPathFirst, spark.executor.extraLibraryPath, spark.python.profile.
   - Shuffle Behavior: Các properties này thường ít được sử dụng hơn, nó dùng để cung cấp các thuộc tính về mặt behavior cho chương trình như spark.reducer.maxSizeInFlight, spark.shuffle.compress, spark.shuffle.file.buffer, spark.shuffle.io.maxRetries, spark.shuffle.io.backLog
   - Spark UI: Cung cấp các thuộc tính về giao diện người dùng như spark.eventLog.logBlockUpdates.enabled, spark.eventLog.longForm.enabled, spark.eventLog.compress, spark.eventLog.dir, spark.eventLog.overwrite, spark.ui.enabled, spark.ui.port
   - Compression and Serialization: Các thuộc tính về nén và tuần tự hóa có thể kể đến spark.broadcast.compress, spark.checkpoint.compress, spark.io.compression.codec.
   - Memory Management: Đây là các thuộc tính dùng để quản lý bộ nhớ như spark.memory.fraction, spark.memory.storageFraction, spark.memory.offHeap, spark.cleaner
   - Execution Behavior: Loại thuộc tính này dùng để thực thi lớp hành vi của chương trình, có thể kể đến spark.broadcast.blockSize, spark.broadcast.checksum, spark.default.parallelism.
   - Executor Metrics: Các thuộc tính này để hiện thực các chỉ số, bao gồm 3 thuộc tính sau: spark.eventLog.logStageExecutorMetrics, spark.executor.processTreeMetrics.enabled và spark.executor.metrics.pollingInterval
   - Networking: Dùng trong các trường hợp liên quan đến mạng như spark.rpc.message.maxSize, spark.blockManager.port, spark.driver.bindAddress, spark.driver.host
   - Scheduling: Xử lý các tiến trình, bao gồm các spark.cores.max, spark.locality.wait, spark.scheduler.mode, spark.scheduler.revive.interval
   - Barrier Execution Mode: Gồm 3 thuộc tính day nhất: spark.barrier.sync.timeout, spark.scheduler.barrier.maxConcurrentTasksCheck.interval và spark.scheduler.barrier.maxConcurrentTasksCheck.maxFailures
   - Dynamic Allocation: spark.dynamicAllocation.enabled, spark.dynamicAllocation.executorIdleTimeout, spark.dynamicAllocation.executorAllocationRatio
   - Thread Configurations: Tùy thuộc vào công việc và cấu hình cụm, chúng ta có thể đặt số lượng luồng ở một số vị trí trong Spark để sử dụng hiệu quả các tài nguyên có sẵn nhằm đạt được hiệu suất tốt hơn. Trước Spark 3.0, các cấu hình luồng này áp dụng cho tất cả các vai trò của Spark, chẳng hạn như trình điều khiển, người thực thi, công nhân và chủ. Từ Spark 3.0, chúng ta có thể định cấu hình các luồng ở mức độ chi tiết tốt hơn bắt đầu từ trình điều khiển và trình thực thi. Lấy mô-đun RPC làm ví dụ trong bảng dưới đây. Đối với các mô-đun khác, chẳng hạn như xáo trộn, chỉ cần thay thế “rpc” bằng “xáo trộn” trong tên thuộc tính ngoại trừ spark. {Driver | executive} .rpc.netty.dispatcher.numThreads, chỉ dành cho mô-đun RPC.
   - Security
   - Spark SQL: Đối với properties này, ta có thể chia làm hai loại như sau:
     + Runtime SQL Configuration: là cấu hình Spark SQL cho mỗi phiên, có thể thay đổi. Chúng có thể được đặt với các giá trị ban đầu bằng tệp cấu hình và các tùy chọn dòng lệnh có tiền tố --conf / -c hoặc bằng cách đặt SparkConf được sử dụng để tạo SparkSession. Ngoài ra, chúng có thể được đặt và truy vấn bằng lệnh SET và đặt chúng về giá trị ban đầu bằng lệnh RESET hoặc bằng các phương thức setter và getter của SparkSession.conf trong thời gian chạy.
     + Static SQL Configuration: là các cấu hình Spark SQL xuyên phiên, bất biến. Chúng có thể được đặt với các giá trị cuối cùng bằng tệp cấu hình và các tùy chọn dòng lệnh có tiền tố --conf / -c hoặc bằng cách đặt SparkConf được sử dụng để tạo SparkSession. Người dùng bên ngoài có thể truy vấn các giá trị cấu hình sql tĩnh qua SparkSession.conf hoặc thông qua lệnh set, ví dụ: Đặt spark.sql.extensions ;, nhưng không thể đặt / bỏ thiết lập chúng.
   - Spark Streaming
   - SparkR
   - GraphX
   - Deploy
   - Cluster Managers: Mỗi trình quản lý cụm trong Spark có các tùy chọn cấu hình bổ sung:
     + YARN
     + Mesos
     + Kubernetes
     + Standalone Mode

<a name="refer"></a>
### Tham khảo
Chương A:

https://viblo.asia/p/tong-quan-ve-apache-spark-cho-he-thong-big-data-RQqKLxR6K7z

https://viblo.asia/p/tim-hieu-ve-hadoop-bJzKmOBXl9N

https://sparkbyexamples.com/apache-spark-rdd/spark-reducebykey-usage-with-examples/
