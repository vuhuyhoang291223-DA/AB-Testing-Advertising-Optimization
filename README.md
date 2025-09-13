# AB-Testing-Advertising-Optimization

# **1. Bối cảnh dự án**

Một công ty đang muốn xác định hiệu quả của các chiến dịch quảng cáo đã tiến hành A/B Testing. Trong đó, người dùng được chia làm 2 nhóm:

- **Nhóm thử nghiệm (AD)**: Phần lớn người dùng được tiếp xúc với quảng cáo.
- **Nhóm đối chứng (PSA)**: Một nhóm nhỏ hơn chỉ thấy Quảng Cáo Dịch Vụ Công Cộng hoặc không thấy nội dung gì ở vị trí quảng cáo.

**Mục tiêu phân tích:**

- Xác định xem quảng cáo có thực sự hiệu quả không bằng cách đo lường và so sánh Tỷ lệ chuyển đổi (conversion rate) giữa hai nhóm.
- Xem xét sự khác biệt thống kê giữa hai nhóm, để đảm bảo rằng bất kỳ khác biệt nào được phát hiện không phải là ngẫu nhiên.

> File Python xử lý dữ liệu và thực hiện Phân tích và Kiểm định Giả thuyết:
> 

https://colab.research.google.com/drive/13kk5ibru-gfKTmaTM_3Yh1EW7gA3T2uw#scrollTo=12byKhKc_FtF

# 2. Cấu trúc Dữ liệu & Kiểm tra và làm sạch dữ liệu

Cấu trúc dữ liệu chỉ bao gồm 1 bảng, với tổng số 58,101 dòng dữ liệu, bao gồm các cột sau:

- **Index:** số dòng
- **User ID:** Mã định danh người dùng.
- **Test group:** Nhóm người dùng [xem quảng cáo - ad] hoặc [quảng cáo cộng đồng - psa]
- **Converted:** Người dùng có mua sản phẩm hay không.
- **Total ads:** Tổng số quảng cáo đã xem.
- **Most ads day:** Ngày xem nhiều quảng cáo nhất.
- **Most ads hour:** Giờ xem nhiều quảng cáo nhất.

<img width="418" height="321" alt="Image" src="https://github.com/user-attachments/assets/39753b08-bf07-4762-a1f7-ed4eec8959e7" />

# **3. Cấu trúc / Hướng phân tích**

<aside>
1️⃣ Xác định cỡ mẫu tối thiểu cho việc kiểm định giả thuyết 

</aside>

**Mục đích:** đảm bảo rằng kiểm định được thiết kế có đủ khả năng (statistical power) để phát hiện ra sự khác biệt có ý nghĩa thống kê giữa hai nhóm (nếu tồn tại). Tránh việc cỡ mẫu quá nhỏ hoặc lớn 

**Phương pháp: Power Analysis**

<aside>
2️⃣ Kiểm tra mối liên hệ giữa việc chạy quảng cáo và tỷ lệ chuyển đổi

</aside>

**Mục đích:** Xác định liệu việc hiển thị quảng cáo có mối liên hệ thống kê với hành vi chuyển đổi (converted) hay không.

**Phương pháp kiểm định:**

- **Chi-square test for independence:** Đánh giá mối quan hệ giữa:
    - `most day ads` và `converted`
    - `most hour ads` và `converted`
- **Welch's t-test:** So sánh trung bình số quảng cáo đã xem (`total ads`) giữa hai nhóm để xem liệu nhóm “Converted” tiếp xúc với quảng cáo nhiều hơn nhóm “NOT Converted” hay không.

<aside>
3️⃣ So sánh tỷ lệ chuyển đổi giữa nhóm thử nghiệm (AD) và nhóm đối chứng (PSA) 

</aside>

**Mục đích:** Đánh giá xem quảng cáo có thực sự làm tăng tỷ lệ chuyển đổi của nhóm thử nghiệm (AD) so với nhóm đối chứng (PSA) hay không.

**Phương pháp kiểm định:**

- **Hypothesis Testing With Two Proportions:** So sánh tỷ lệ chuyển đổi giữa nhóm AD và PSA.

# 4. Phân tích

<aside>
💡 Chi tiết về code phân tích và kiểm định giả thuyết đã được trình bày trong link Google Colab bên dưới, kèm theo giải thích chi tiết từng bước trong quá trình thực hiện. 
⇒ Tại đây, em tập trung giải thích thêm về lý thuyết, hướng tiếp cận của mình, và tổng hợp các kết quả chính để làm rõ hơn nội dung phân tích.

</aside>

https://colab.research.google.com/drive/13kk5ibru-gfKTmaTM_3Yh1EW7gA3T2uw#scrollTo=12byKhKc_FtF

### **NOTES:**

Trong bài phân tích, các giá trị cố định sẽ được sử dụng xuyên suốt như sau:

- **α = 0.05** (ý nghĩa thống kê)
- **β = 0.02** (xác suất xảy ra lỗi loại II)

<img width="874" height="319" alt="Image" src="https://github.com/user-attachments/assets/c9016ca1-2f06-47dd-874a-857b2d264af2" />

## 4.1. Power Analysis

> **Power Analysis** là một phương pháp thống kê được sử dụng để xác định **Kích thước mẫu tối thiểu** cần thiết cho kiểm định để đảm bảo kết quả có đủ độ mạnh thống kê (statistical power).
> 

**Trước khi chạy A/B Testing, người ta thường thực hiện phân tích Power Analysis.**

Power Analysis giúp đảm bảo rằng kiểm định được thiết kế có đủ khả năng (statistical power) để phát hiện ra sự khác biệt có ý nghĩa thống kê giữa hai nhóm (nếu tồn tại). Điều này rất quan trọng vì nếu thiết kế không đủ mạnh, có thể xảy ra hai vấn đề sau:

- **Kích thước mẫu không đủ:**
    - Dẫn đến nguy cơ không phát hiện được sự khác biệt (Type II error)
    - Kết quả có thể cho rằng không có ý nghĩa, trong khi thực tế có.
- **Kích thước mẫu quá lớn:** gây tốn kém tài nguyên

<aside>
💡 Trong trường hợp này, công ty đã chạy xong quảng cáo, Power Analysis sẽ được dùng để đánh giá liệu cỡ mẫu hiện tại có đủ lớn để phân tích kết quả chính xác hay không.

</aside>

### Kết quả:

```python
alpha = 0.2
power = 0.8
effect_size = 0.04904561593068279 #Tính bởi Cohen’s d Formula

Cỡ mẫu tối thiểu cho bài phân tích là: 6526
```

- Cỡ mẫu cần thiết tối thiểu là 6526
- Do ở đây đã thu thập số liệu rồi nên để tránh lãng phí tài nguyên (do công ty đã thu thập được 23,524 mẫu với nhóm psa)

**⇒ Chọn cỡ mẫu là 23,000 cho cả 2 nhóm**

Em tiến hành random từ số liệu đã thu thập được sẵn và đạt được kết quả 

<img width="685" height="441" alt="Image" src="https://github.com/user-attachments/assets/30988612-be4f-454c-b0f7-6e87adb7b706" />

## **4.2. Chi-Square Test for Independence**

<aside>
💡 Em sẽ sử dụng P-value để kiểm định giả thuyết:

- **P-value > α**: Bác bỏ giả thuyết Ho, chấp Ha (kết quả có ý nghĩa thống kê)
- **P-Value < α**:  Không đủ bằng chứng để bác bỏ Ho (không có ý nghĩa thống kê)
</aside>

> + **P-Value:** xác suất để quan sát được kết quả dữ liệu (hoặc cực đoan hơn) giả sử Ho đúng. Có thể hiểu Ho là mức **α nhỏ nhất** để bác bỏ Ho.
+ **α (alpha):** mức ý nghĩa thống kê (significance level). Là xác suất chấp nhận **sai lầm loại I (Type I Error)**, tức là bác bỏ giả thuyết Ho trong khi Ho đúng.
> 

### 4.2.1. Đánh giá mối quan hệ giữa `most day ads` và `converted`

**Mục đích:** Muốn kiểm tra xem ngày chạy nhiều quảng cáo nhất trong tuần có tác động đến tỷ lệ chuyển đổi không?

- H0: Cột "converted" và "most ads day" phụ thuộc nhau
- HA: Cột "converted" và "most ads day" không phụ thuộc nhau

### Kết quả:

<img width="895" height="448" alt="Image" src="https://github.com/user-attachments/assets/4a8dce89-4ee7-4abe-83f4-903c8166aa6c" />

```python
Chi-squared test statistic: 22.07895637710061
P-value: 0.0011716199872981233
Degrees of freedom: 6
```

=> P-value < 0.05 cho thấy rằng **có mối liên hệ về mặt thống kê** giữa ngày người dùng xem nhiều quảng cáo nhất (most ads day) và trạng thái chuyển đổi của họ (converted)

- Tỉ lệ chuyển đổi cao nhất vào đầu tuần (Thứ Hai, Ba) - và giảm dần theo các ngày trong tuần và tăng lại vào Chủ nhật.

<img width="880" height="529" alt="Image" src="https://github.com/user-attachments/assets/31607b02-f0b8-4607-b49c-79393dd38bc5" />

### 4.2.2. Đánh giá mối quan hệ giữa `most hour ads` và `converted`

**Mục đích:** Muốn kiểm tra xem giờ chạy nhiều quảng cáo nhất trong ngày có tác động đến tỷ lệ chuyển đổi không?

- H0: Cột "converted" và "most ads hour" phụ thuộc nhau
- HA: Cột "converted" và "most ads hour" không phụ thuộc nhau

### Kết quả:

<img width="1443" height="388" alt="Image" src="https://github.com/user-attachments/assets/e11fcf99-5372-47c3-89dc-c9d065d3f012" />

```python
Chi-squared test statistic: 56.12570293427687
P-value: 0.00013592856482938108
Degrees of freedom: 23
```

=> P-value < 0.05 cho thấy rằng **có mối liên hệ về mặt thống kê** giữa giờ trong ngày người dùng xem nhiều quảng cáo nhất (most ads hour) và trạng thái chuyển đổi của họ (converted).

- Tỉ lệ chuyển đổi tăng dần từ buổi trưa đến chiều vào cao nhất vào 15h-16h

<img width="889" height="530" alt="Image" src="https://github.com/user-attachments/assets/2bddc49d-3598-4729-897e-119be9e4d847" />

## **4.3. T-Test: so sánh trung bình 2 tổng thể**

Để kiểm tra giả thuyết so sánh trung bình của hai tổng thể, có hai loại kiểm định t-test phổ biến:

- **Independent T-Test** (giả định rằng phương sai của hai tổng thể bằng nhau)
- **Welch’s T-Test** (giả định rằng phương sai của hai tổng thể khác nhau)

Để xác định phương sai của hai tổng thể có bằng nhau hay không, ta sử dụng Levene's Test.

- Nếu Levene's Test cho kết quả không có sự khác biệt phương sai (p-value > 0.05): Chọn Independent T-Test.
- Nếu Levene's Test cho thấy phương sai khác nhau (p-value ≤ 0.05): Chọn Welch's T-Test.

### 4.3.1. Levene’s Test

**Mục đích:** Muốn kiểm tra xem phương sai tổng thể có khác nhau hay không?

$$
H_0: \sigma_1^2 = \sigma_2^2 \\

H_A: \sigma_1^2 \neq \sigma_2^2 \\
$$

- Phương sai 1 = Phương sai nhóm converted (tổng thể)
- Phương sai 2 = Phương sai nhóm not-converted (tổng thể)

### Kết quả:

```python
Levene's test statistic: 780.3031707448639
p-value: 2.750154486801534e-170
Bác bỏ giả thuyết H0: Phương sai giữa các nhóm KHÔNG bằng nhau.
```

⇒ Kết luận: Chọn Welch's T-Test vì phương sai khác nhau (p-value ≤ 0.05)

### 4.3.2. **Welch’s T-Test**

**Mục đích:** Kiểm tra xem chạy ads nhiều có đồng nghĩa là tỉ lệ chuyển đổi cao không 

⇒ So sánh lượng ads chạy trung bình giữa 2 nhóm “converted” và “not converted”

$$
H_0: \mu_{converted} = \mu_{not-converted} \\

H_A: \mu_{converted} \neq \mu_{not-converted}
$$

**Giả thuyết**

- **Mẫu độc lập với nhau**
    - Phân bổ vào hai nhóm là ngẫu nhiên.
    - Không có thành viên nào xuất hiện trong cả hai nhóm (nhóm converted và nhóm notconverted).
- **Phân phối mẫu là phân phối chuẩn**
    - Do số lượng mẫu cả hai nhóm đều > 30. Áp dụng **Định lý giới hạn trung tâm**, không cần kiểm tra phân phối chuẩn.
    - *Nếu số lượng mẫu nhỏ hơn 30 thì thực hiện Shapiro–Wilk test.*
- **Phương sai giữa 2 nhóm tổng thể là khác nhau**

> **Định lý giới hạn trung tâm (Central Limit Theorem):** khi cỡ mẫu đủ lớn (≥30), phân phối mẫu của trung bình mẫu sẽ xấp xỉ phân phối chuẩn, bất kể phân phối gốc của dữ liệu là gì.
> 

### **Kết quả:**

```python
t-test statistic: 22.376587661949856
p-value: 6.580117929321791e-91
```

⇒ P-value < 0.05 cho thấy rằng **có sự khác biệt về mặt thống kê** giữa lượng quảng cáo (ads) mà người có trạng thái chuyển đổi (converted) so với người không

⇒ T test statistic > 0. Nên có thể nhận định rằng nhóm (mẫu) người có trạng thái chuyển đổi xem nhiều quảng cáo hơn (về mặt thống kê) so với nhóm (mẫu) không chuyển đổi và Sự khác biệt này không phải do ngẫu nhiên mà có.

## 4.4. A/B Testing: **so sánh tỷ trọng 2 tổng thể**

**Mục đích:** Kiểm tra xem tỉ lệ chuyển đổi của 2 nhóm tổng thể có bằng nhau không?

$$
H_0: p_{ad} = p_{psa} \\
H_A: p_{ad} \neq p_{psa}
$$

- **p**ad = tỉ lệ chuyển đổi nhóm ad (experimental group)
- **p**psa = tỉ lệ chuyển đổi nhóm psa (control group)

**Giả thuyết**

- Mẫu được chọn ngẫu nhiên từ tổng thể
- Mẫu độc lập với nhau
- Có ít nhất 5 thành công và ít nhất 5 thất bại trong mỗi nhóm
    - **Thành công (Success):** là những người trong mỗi nhóm (ad và psa) đã thực hiện hành động chuyển đổi.
    - **Thất bại (Failure):** Là những người không thực hiện hành động chuyển đổi đó.

### Kết quả

```python
Z-statistic: 6.633670521700936
P-value: 3.274402945588342e-11
```

=> P-value < 0.05 cho thấy rằng có sự khác biệt về mặt thống kê về tỉ lệ chuyển đổi giữa nhóm xem quảng cáo (nhóm thử nghiệm) và nhóm xem Quảng Cáo Dịch Vụ Công Cộng (nhóm đối chứng).

=> Z test statistic > 0. Nên có thể nhận định rằng tỉ lệ chuyển đổi giữa nhóm (mẫu) xem quảng cáo LỚN HƠN nhóm (mẫu) xem Quảng Cáo Dịch Vụ Công Cộng. **Sự khác biệt này không phải do ngẫu nhiên mà có, mà có thể là kết quả thực sự của việc chạy quảng cáo.**
