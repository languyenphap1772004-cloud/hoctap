# 💎 Bộ Gem học tập cho Gemini

Bộ này chuyển thể từ 2 skill Antigravity `/claude-argon` và `/argon-test` sang dạng **Gem** để dùng trong Gemini (web hoặc app).

| Gem | Chuyển thể từ | Dùng khi |
|---|---|---|
| [🧠 Gia Sư Thông Minh](01-gia-su-claude-argon.md) | `/claude-argon` | Học chủ đề mới, giải bài, học code |
| [✅ Kiểm Tra Kiến Thức](02-kiem-tra-argon-test.md) | `/argon-test` | Kiểm chứng tài liệu, làm quiz, chấm điểm, test code |

## Cách tạo Gem (2 phút)
1. Mở https://gemini.google.com → menu trái → **Gems** (hoặc **Explore Gems**) → **New Gem**.
2. **Name:** đặt tên Gem.
3. **Instructions:** mở file tương ứng, copy toàn bộ nội dung trong khung code ở mục "INSTRUCTIONS" rồi dán vào.
4. **Knowledge** (tuỳ chọn): tải giáo trình, slide, ghi chú (PDF/Docs). Gem sẽ ưu tiên dựa vào đó.
5. Chọn model **Pro** (hoặc bật **Thinking**) để suy luận tốt nhất → **Save**.

## Quy trình học khuyên dùng
```
Gia Sư Thông Minh → học bài
        ↓  copy bài học
Kiểm Tra Kiến Thức → kiểm chứng + làm quiz → chấm điểm
        ↓  "chủ đề yếu"
Gia Sư Thông Minh → "giải thích kỹ hơn phần …"
```

## Khác biệt so với bản Antigravity
- Gemini không có subagent, nên vai trò "Brain → Argon" được gộp thành quy trình **Lập dàn ý → Soạn → Tự rà soát** bên trong một Gem.
- Bước tìm web dùng **Google Search** có sẵn của Gemini.
- Gem không có quyền xoá/sửa file của bạn, nên bỏ qua các quy tắc về file.
