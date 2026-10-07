---
name: refund-policy
description: Đánh giá yêu cầu hoàn tiền dựa trên ngày mua, ngày yêu cầu hoàn, trạng thái kích hoạt và tài liệu chính sách trong data/policies/. Dùng khi người dùng hỏi về điều kiện hoặc phí hoàn tiền.
---

# Refund policy

## Tìm và đọc tài liệu

1. Ngay sau khi đọc skill này, dùng `read_file` đọc `skills/refund-policy/references/answer-template.md`, trước khi gọi tool nghiệp vụ khác.
2. Dùng `list_files` với `data/policies` để tìm các tài liệu chính sách hiện có. Không giả định tên file cố định; file có thể được đổi tên.
3. Dùng `read_file` để đọc các tài liệu chính sách phù hợp. Nếu không tìm thấy hoặc không đọc được tài liệu cần thiết, báo giới hạn và không tự suy đoán chính sách.
4. Đọc phạm vi ngày hiệu lực trong nội dung tài liệu. Chọn chính sách theo **ngày mua**, không theo ngày yêu cầu hoàn hoặc ngày hiện tại. Ngày bắt đầu ghi “từ” là bao gồm ngày đó nếu tài liệu nói rõ.

## Đánh giá điều kiện

1. Cần có ngày mua, ngày yêu cầu hoàn và trạng thái sản phẩm đã kích hoạt hay chưa. Nếu thiếu bất kỳ thông tin nào, hỏi lại thông tin đó trước khi kết luận; không tự giả định sản phẩm chưa kích hoạt.
2. Tính số ngày đã qua bằng chênh lệch ngày lịch giữa ngày yêu cầu hoàn và ngày mua. Không dùng ngày hiện tại của máy. Bằng đúng giới hạn trong chính sách vẫn đủ điều kiện về thời gian.
3. Nếu sản phẩm đã kích hoạt, áp dụng điều khoản không hoàn tiền của chính sách được chọn. Nếu chưa kích hoạt, so sánh số ngày với giới hạn của chính sách.
4. Chỉ nêu phí khi yêu cầu đủ điều kiện hoàn tiền. Dẫn đường dẫn workspace tương đối của tài liệu đã đọc làm căn cứ.
5. Không kết luận dựa trên tên file; kiểm tra nội dung và phạm vi ngày của tài liệu.

## Định dạng câu trả lời

Đưa đủ các trường của reference đã đọc. Nếu thiếu thông tin, chỉ nêu thông tin còn thiếu và câu hỏi cần làm rõ, chưa điền kết luận.
