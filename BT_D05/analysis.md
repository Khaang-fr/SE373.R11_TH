# Phân tích kết quả lab

## Kiểm tra tool `list_files`

Đã chạy `pytest stage-02-skills/tests -q`: **44 passed**. Stage 01 trước đó có **37 passed**.

| Đầu vào | Kết quả |
|---|---|
| Thư mục hợp lệ `data/` | Thành công; mỗi mục có `name`, `path` tương đối workspace và `type`; sắp xếp theo tên; không duyệt thư mục con. |
| Đường dẫn không tồn tại | Lỗi `PATH_NOT_FOUND`. |
| Đường dẫn là file | Lỗi `NOT_A_DIRECTORY`. |
| Đường dẫn vượt workspace | Lỗi `PATH_OUTSIDE_WORKSPACE`; trường hợp symlink trỏ ra ngoài cũng bị chặn. |

Bằng chứng tests: `stage-01-files/tests/test_files.py` và `stage-02-skills/tests/test_files.py`, các test `test_list_direct_entries_sorted_with_workspace_relative_paths`, `test_list_rejects_missing_file_and_outside_symlink`, và `test_tools_return_json_against_project_workspace`. Schema tool được kiểm tra trong `tests/test_agent.py`.

## Kết quả các trường hợp chính sách

### Stage 00

Trace `stage-00-chat/traces/20261007-203027_54be990c_turn01_f8613adb.jsonl` ghi nhận câu hỏi ở event 1, request model ở event 2, phản hồi ở event 3 và hoàn tất ở event 4; không có event gọi tool. `stage-00-chat/agent.py` cũng cấu hình `TOOLS = []`. Agent không thể tìm hoặc đọc tài liệu trong workspace, nên câu trả lời của stage này không phải bằng chứng đã tra chính sách.

| Trường hợp | Tính toán theo dữ liệu chính sách | Kết quả |
|---|---|---|
| A: mua 28/09/2026, yêu cầu 06/10/2026, chưa kích hoạt | Chọn chính sách áp dụng trước 01/10 theo ngày mua; 8 ngày đã qua, quá giới hạn 7 ngày. | Không đủ điều kiện. Nếu đủ điều kiện, chính sách quy định phí 10%; trường hợp này phí không áp dụng. |
| B: mua 02/10/2026, yêu cầu 12/10/2026, chưa kích hoạt | Chọn chính sách từ 01/10 theo ngày mua; 10 ngày đã qua, trong giới hạn 14 ngày. | Đủ điều kiện, không thu phí. |
| Thiếu trạng thái kích hoạt | Không đủ thông tin để áp dụng điều khoản kích hoạt. | Hỏi người dùng trạng thái kích hoạt, chưa kết luận. |

Tài liệu gốc được đặt trong `stage-01-files/workspace/data/policies/` và `stage-02-skills/workspace/data/policies/`. Sau kiểm tra đổi tên, stage 02 hiện dùng `renamed-before-oct-policy.md` và `renamed-from-oct-policy.md`, nội dung không đổi. Skill và mẫu trả lời ở `stage-02-skills/workspace/skills/refund-policy/`.

## Trace hội thoại

Các lượt A, B và thiếu thông tin chạy trong ba cuộc trò chuyện stage 02 độc lập; trace chứa đúng câu hỏi, nội dung skill/reference được nạp, và các thao tác công cụ tương ứng.

| Trường hợp | Trace | Bằng chứng trong trace | Kết quả quan sát |
|---|---|---|---|
| A, sau khi đổi tên file | `stage-02-skills/traces/20261007-204354_5709c501_turn01_5401758b.jsonl` | Event 1 là câu hỏi; events 4–5 đọc skill; 8–9 đọc reference; 12–13 liệt kê `data/policies/` và tìm thấy cả hai tên đã đổi; events 16–19 đọc chính sách; event 21 là câu trả lời; event 22 hoàn tất. | Chọn chính sách trước tháng 10 theo ngày mua; 8 ngày, không đủ điều kiện; câu trả lời dẫn `data/policies/renamed-before-oct-policy.md`. |
| B, sau khi đổi tên file | `stage-02-skills/traces/20261007-204237_29a8f5d6_turn01_b4782900.jsonl` | Events 4–5 đọc skill; 8–9 đọc reference; 12–13 liệt kê các file đã đổi tên; events 16–19 đọc tài liệu chính sách; event 21 là câu trả lời; event 22 hoàn tất. | Chọn chính sách từ tháng 10 theo ngày mua; 10 ngày, đủ điều kiện và không phí; câu trả lời dẫn `data/policies/renamed-from-oct-policy.md`. |
| Thiếu trạng thái kích hoạt | `stage-02-skills/traces/20261007-204424_3f47ef03_turn01_c2d13397.jsonl` | Event 1 là câu hỏi thiếu trạng thái; events 4–5 đọc skill và 8–9 đọc reference; event 11 là câu trả lời; event 12 hoàn tất. Không có thao tác đọc chính sách để kết luận. | Agent hỏi người dùng sản phẩm đã kích hoạt chưa; không tự giả định và không kết luận đủ/không đủ điều kiện. |
| Stage 00 | `stage-00-chat/traces/20261007-203027_54be990c_turn01_f8613adb.jsonl` | Events 1–4 là câu hỏi, request, response và hoàn tất; không có event gọi tool. | Agent không có khả năng truy cập tài liệu để kiểm chứng câu trả lời. |

## Câu hỏi cuối bài

Tool `list_files` cung cấp khả năng truy cập thật vào workspace và trả về các tên file hiện có; nhờ đó agent vẫn tìm được tài liệu khi tên file thay đổi. Skill cung cấp quy trình nghiệp vụ để đọc phạm vi hiệu lực, chọn chính sách theo ngày mua, tính ngày lịch và hỏi lại dữ kiện còn thiếu. Không có tool tìm file thì chỉ sửa prompt không thể khám phá đáng tin cậy tên mới của file; prompt chỉ hướng dẫn cách suy luận, không tạo quyền truy cập hay cung cấp nội dung file.
