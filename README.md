# Bộ Skill `/claude-argon` & `/argon-test` cho Antigravity Web / Điện thoại

Thư mục này chứa trọn bộ cấu hình chuẩn `.agents/` để bạn dùng trên **antigravity.google.com** từ điện thoại mà **không cần mang theo máy tính**:

- `GEMINI.md`: Quy tắc an toàn toàn cục (Cấm xóa file tuyệt đối; nếu cần phải hỏi xác nhận 3 lần).
- `.agents/skills/claude-argon/SKILL.md`: Skill `/claude-argon` (Claude làm bộ não lập dàn ý & kiểm duyệt, Argon `flash` thực thi/soạn bài).
- `.agents/skills/argon-test/SKILL.md`: Skill `/argon-test` (Argon `flash` tự kiểm chứng thông tin/chạy test & tạo bài kiểm tra kiến thức).

## Cách đưa lên Antigravity Web (Cloud) để tắt máy tính vẫn dùng được trên điện thoại
1. Tạo 1 repository riêng tư (Private repo) trên GitHub (ví dụ: `my-study-space`) qua trình duyệt web `github.com`.
2. Kéo thả toàn bộ các file trong thư mục này (`GEMINI.md` và thư mục `.agents/`) lên repo GitHub đó.
3. Trên điện thoại, mở **https://antigravity.google.com** -> chọn **Remote Agents (Cloud)** -> kết nối vào repo `my-study-space`.
4. Từ lúc đó, bất kỳ khi nào mở chat trên điện thoại (kể cả khi máy tính ở nhà đã tắt), Antigravity Cloud sẽ tự động nạp `GEMINI.md`, `/claude-argon` và `/argon-test`!
