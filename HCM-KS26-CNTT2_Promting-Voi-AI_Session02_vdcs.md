# Vận dụng chuyên sâu Thiết kế giải pháp Prompting ứng dụng thực tế cho Cấu trúc Prompt Tiêu chuẩn & Định dạng

**Bước 1: Thiết kế giải pháp (I/O)**
Input (Đầu vào):

- **Thông tin đơn hàng:** Mã vận đơn, tên khách hàng, ngày dự kiến giao, số ngày giao trễ thực tế.

- **Nội dung tin nhắn,phản hồi của khách hàng:** Tin nhắn phàn nàn gốc gửi về hệ thống CSKH

**Chính sách bồi thường của cửa hàng:**

- Trễ từ 1 - 2 ngày: Xin lỗi khách hàng kèm mã giảm giá 10% cho đơn sau.
 
- Trễ từ 3 - 5 ngày: Xin lỗi  khách hàng kèm miễn phí vận chuyển và mã giảm giá 15%.

Output (Đầu ra):

- **Dạng JSON:** Để hệ thống phần mềm (API/Database) để AI có thể tự động đọc và lưu trữ dữ liệu. 

- Các trường dữ liệu :

status: Trạng thái xử lý (success hoặc fail).

customer_name: Tên khách hàng

tracking_number: Mã đơn hàng

delay_days: Số ngày trễ

compensation_applied: Chính sách đền bù được áp dụng

draft_message: Nội dung tin nhắn xin lỗi khách hàng được soạn thảo

note_for_staff: Ghi chú thêm cho nhân viên CSKH

expected_date: Ngày dự kiến

actual_date: Ngày giao mới

**Bước 2: Viết prompt và chạy thử**

### Context:

Hãy đóng vai là nhân viên soạn thảo tin nhắn xin lỗi và ép sang định dạng Json cho cửa hàng Techshop. 

### Request:

Nhiệm vụ của bạn là tiếp nhận thông tin phản hồi của khách hàng về việc giao hàng chậm, xử lý vấn đề và soạn một bản mẫu tin nhắn email xin lỗi chuyên nghiệp. Và tiếp là ép sang định dạng Json cho nhân viên Techshop đọc

### Explanation:

- Nếu trường [Tên khách hàng] hoặc [Mã đơn hàng] bị trống hoặc không rõ ràng, hãy điền giá trị "Khách hàng" vào Output và thêm cảnh báo vào note_for_staff.

- Nếu [Ngày dự kiến] hoặc [Ngày giao mới] bị sai lệch định dạng hoặc thiếu, hãy tự động tính toán dựa trên nội dung tin nhắn hoặc mặc định ghi chú cần kiểm tra lại kho vận.

### Action:

1. Đọc kỹ thông tin đơn hàng và phản hồi của khách hàng được cung cấp ở phần [INPUT]. 
2. Xác định số ngày trễ và đối chiếu với chính sách bồi thường. 
3. Trả lời tin nhắn xin lỗi khách hàng phù hợp. 
4. Rồi trả kết quả theo định dạng JSON cho nhân viên Techshop kiểm thử

### Tone:

- Giọng văn chân thành, lịch sự, chuyên nghiệp ,thấu hiểu

### Extras:


Trả lời tin nhắn xong xuất  kết quả theo định dạng JSON cho cửa hàng Techshop


### FEW-SHOT EXAMPLES (VÍ DỤ MẪU):

Ví dụ 1:
Input: {
   "customer_name": "Nguyễn Văn A",
   "tracking_number": "MH1234",
   "expected_date": "2026-09-10",
   "actual_date": "2026-09-12",
   "customer_message": "Sao hàng của tôi đặt từ 2 ngày trước mà giờ mới nhận được , app quá tệ"
}

Output: {
   "status": "success",
   "customer_name": "Nguyễn Văn A",
   "delay_days": 2,
   "tracking_number": "MH1234",
   "compensation_applied": "Mã giảm giá 10% cho đơn hàng tiếp theo",
   "draft_message": "Xin lỗi quý khách về sự bất tiện này,Do cửa hàng chúng tôi gặp một số vấn đề ký thuật. Để đền bù do quý khách đã có trải nghiệm không tốt , cửa hàng chúng tôi gửi tặng quý khách mã giảm giá 10% cho đơn hàng tiếp theo. Mong quý khách thông cảm cho cửa hàng!", 
   "note_for_staff": "Đã xử lý tự động theo chính sách trễ 2 ngày." 
}

ACTUAL INPUT (DỮ LIỆU THỰC TẾ CẦN XỬ LÝ) 

Input: { 
  "customer_name": "Trần Thị B", 
  "tracking_number": "MH56789", 
  "expected_date": "2026-10-18", 
  "actual_date": "2026-10-21", 
  "customer_message": "Tôi đã đặt đơn hàng này từ ngày 20 mà qua nhiều ngày vẫn không có hàng, gọi điện thoại cho cửa hàng vẫn bảo chị cứ chờ đợi"
  }

OUTPUT FORMAT (JSON ONLY)

Kết quả phản hồi mẫu từ AI:
Output: { 
  "status": "success", 
  "customer_name": "Trần Thị B", 
  "tracking_number": "MH56789", 
  "delay_days": 3, 
  "compensation_applied": "Miễn phí vận chuyển + Mã giảm giá 15%", 
  "draft_message": "Xin lỗi quý khách về sự bất tiện này, cửa hàng thành thật xin lỗi quý khách vì có trải nghiệm chưa tốt từ cửa hàng cứ bảo chờ đợi hàng gửi đến. Để đền bù cửa hàng chúng tôi xin gửi tặng chị ưu đãi miễn phí vận chuyển kèm mã giảm giá 15% cho đơn hàng kế tiếp để đền bù cho sự bất tiện này. Mong quý khách thông cảm.", 
  "note_for_staff": "Đơn hàng trễ 5 ngày, áp dụng mức đền bù cao" 
  }