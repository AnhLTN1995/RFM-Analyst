# 🎯 RFM Customer Segmentation with SQL

## 🧩 Mô tả

Phân tích RFM (**Recency - Frequency - Monetary**) là một phương pháp phổ biến trong marketing để phân loại và đánh giá **giá trị khách hàng** dựa trên hành vi mua hàng.

Script này được viết bằng **T-SQL (SQL Server)** và thực hiện:

- Tính **Recency**: Số ngày kể từ lần mua hàng cuối cùng
- Tính **Frequency**: Số đơn hàng mà khách hàng đã thực hiện
- Tính **Monetary**: Tổng doanh thu từ khách hàng
- Chuẩn hóa các chỉ số thành điểm (scale 1–5)
- Ghép phân khúc tương ứng từ bảng `Segment_Scores`

## 🗂️ Cấu trúc logic

1. **`master_customer` CTE**:
   - Tính `Date_Diff` giữa ngày mua và ngày mua gần nhất trong toàn bộ dữ liệu
   - Lấy `SalesOrderID`, `OrderDate`, `CustomerID`, `TotalDue`, v.v.

2. **`RFM_Score` CTE**:
   - Tính các chỉ số RFM theo khách hàng:
     - `Recency` = ngày gần nhất mua hàng
     - `Frequency` = số đơn hàng
     - `Monetary` = tổng số tiền chi tiêu
   - Chuẩn hóa thành `R_Score`, `F_Score`, `M_Score` từ 1 đến 5 (qua `NTILE(5)`)

3. **Tổng hợp kết quả**:
   - Nối các điểm thành `RFM_Score` (VD: 535)
   - Ghép với bảng `Segment_Scores` để phân loại (VD: VIP, Hạng trung, Nguy cơ rời bỏ,...)
   - Lưu tạm vào bảng `#Segment_Temp`

## 📊 Output mẫu

| CustomerID | R_Score | F_Score | M_Score | RFM_Score | Segment        |
|------------|---------|---------|---------|-----------|----------------|
| 11000      | 5       | 5       | 5       | 555       | Champion       |
| 12045      | 1       | 2       | 2       | 122       | At Risk        |
| 10324      | 3       | 4       | 5       | 345       | Loyal Customer |

_(Bảng phân khúc mẫu lưu ở `dbo.Segment_Scores`)_

## 🛠️ Công nghệ sử dụng

- Microsoft SQL Server
- T-SQL
- CTE (Common Table Expressions)
- NTILE window function

## 📥 Cách chạy

```sql
-- Đảm bảo đã có bảng Segment_Scores với các mapping Scores -> Segment
SELECT * FROM dbo.Segment_Scores;

-- Chạy truy vấn
-- (hoặc tạo stored procedure nếu cần tái sử dụng)

-- Kết quả được lưu vào bảng tạm:
SELECT * FROM #Segment_Temp;

🧠 Mục đích ứng dụng
- Phân loại khách hàng theo hành vi

- Nhắm mục tiêu marketing: giữ chân khách hàng giá trị cao, kích thích nhóm mua ít

- Nâng cao hiểu biết dữ liệu bán hàng nội bộ
