# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Phương tiện chở người cỡ nhỏ/trung bình, gồm sedan, hatchback, SUV, CUV, taxi và xe bán tải (pickup). | Phân biệt với `truck`: xe bán tải (pickup) có thùng sau vẫn tính là `car`; phân biệt với `van`: các dòng xe con 5-7 chỗ thông thường không có dáng hộp chở hàng lớn. |
| `truck` | Xe tải chở hàng hạng trung và hạng nặng, xe ben, xe bồn, xe đầu kéo container. | Có phần đầu kéo tách biệt hoặc thùng chở hàng tải trọng lớn; loại trừ xe bán tải cabin nhỏ (thuộc `car`). |
| `bus` | Xe buýt công cộng, xe khách liên tỉnh, minibus chở khách theo tuyến. | Xe có khoang hành khách lớn, nhiều hàng ghế/cửa sổ đón trả khách; loại trừ xe van chở hàng hoặc minivan gia đình (thuộc `van`). |
| `van` | Xe van, minivan, xe dạng thùng hộp (box-shaped) liền khối dùng chở hàng hoặc chở người chuyên dụng. | Dáng xe hình hộp vuông vức liền khối từ khoang lái ra khoang sau; không lớn như xe buýt (`bus`) và không phải sedan/SUV thông thường (`car`). |

## Quy tắc chung

- **Box bám sát vật thể nghĩa là:** Bounding box phải ôm sát đường viền các điểm cực (mép ngoài cùng) của phần thân xe nhìn thấy được (tight bounding box), không lấy dư mặt đường, bóng đổ của xe, bầu trời hay các xe lân cận.
- **Vật thể bị che khuất:** Vẽ box bao quanh toàn bộ phần nhìn thấy được của xe nếu phần lộ ra đủ đặc trưng để nhận diện chắc chắn lớp đối tượng. Khi hai xe chồng lấn lên nhau, vẽ hai bounding box độc lập tương ứng với phần nhìn thấy của từng xe.
- **Vật thể bị cắt bởi rìa ảnh:** Vẽ bounding box ôm sát phần thân xe nằm trong ảnh và cho cạnh box chạm đúng đường biên ảnh ($x=0, 1$ hoặc $y=0, 1$); tuyệt đối không ngoại suy hoặc tự phóng đại box ra ngoài biên ảnh.
- **Vật thể rất nhỏ/mơ hồ:** Chỉ gán box khi kích thước và độ nét của vật thể đủ để khẳng định thuộc 1 trong 4 lớp của schema. Nếu chỉ là chấm sáng hoặc vệt mờ ở đường chân trời xa không rõ cấu trúc, bỏ qua để tránh gây False Positive. Tuyệt đối không gán người đi bộ, xe máy, xe đạp, cột đèn hay biển báo.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | Nhiều xe con ở hậu cảnh xa phía ngã rẽ và các xe đỗ ven đường bị che khuất một phần bởi dải cây/phân cách. | Gán `car` (ID 0) cho các xe nhận diện rõ cấu trúc thân vỏ/đèn/bánh xe; bỏ qua các vệt mờ không rõ chi tiết ở quá xa. | Schema yêu cầu chỉ gán khi nhận diện được lớp; box ôm phần nhìn thấy của xe đỗ/xe bị che. |
| `drive_012` | Xe thân hộp (box-shaped) cỡ vừa di chuyển trên đường, dễ phân vân giữa xe khách nhỏ (`bus`) và xe tải nhỏ (`truck`). | Gán `van` (ID 3). | Thân xe liền khối dạng hộp chuyên chở hàng/khách cỡ trung, kích thước không đạt chuẩn xe buýt lớn và không có thùng hàng rời của xe tải. |
| `drive_026` | Xe bán tải (pickup) có cabin kép và thùng chở hàng nhỏ phía sau. | Gán `car` (ID 0). | Schema quy định rõ: xe bán tải / cabin nhỏ thuộc lớp `car`, chỉ xe tải chở hàng lớn/đầu kéo mới thuộc `truck`. |
| `drive_045` | Xe ô tô đang di chuyển ra khỏi khung hình, chỉ nhìn thấy nửa thân sau sát mép trái ảnh. | Gán `car` (ID 0) với cạnh trái chạm mép ảnh ($x_{min}=0$). | Quy tắc xử lý cắt biên: chỉ bao phần thân nhìn thấy trong ảnh và chạm mép rìa ảnh, không tự đoán nửa đầu xe nằm ngoài. |
