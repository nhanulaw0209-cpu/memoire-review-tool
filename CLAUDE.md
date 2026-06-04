# CLAUDE.md — Memoire Saigon Review Tool

> Claude tự đọc file này khi mở cửa sổ trong thư mục này.

---

## 1. Project là gì

`review-reply-tool.html` — công cụ generate reply cho Google Reviews của nhà hàng **Memoire Saigon**.

- Chạy hoàn toàn trên browser (không cần server)
- Dùng **Groq API** (llama-3.3-70b) để generate
- Lưu lịch sử reply vào **localStorage**
- Sync 2 chiều với **Google Sheets** qua Apps Script Web App

---

## 2. Thông tin nhà hàng

| | |
|---|---|
| **Tên** | Memoire Saigon |
| **Địa chỉ** | 2 Brewery Terrace, Saundersfoot SA69 9HG, Wales, UK |
| **Vị trí** | Harbour village ven biển xứ Wales — nhỏ, yên bình, indoor |
| **SĐT** | +44 7415 726148 |
| **Giờ** | Dine-in từ 12 PM · Takeout 4–9 PM |
| **Giá** | £20–30/người |
| **Rating** | ⭐ 4.9 · 147 Google reviews |

---

## 3. Nhân vật chủ quán (AI persona)

- **Tự do, phóng khoáng, cá tính mạnh** — không nói kiểu corporate/PR
- **Chân thành, trân trọng khách** — người đọc phải cảm thấy được chào đón
- **Lịch sự, có thiện cảm** — không lạnh, không grovelling
- Sign-off cố định: `Best,` / `The Memoire Saigon Team`
- Viết **British English** tự nhiên, everyday

---

## 4. Cấu trúc tool

### Training Examples
- 36 cặp review → reply (6 Memoire-voice gốc + 15 dine-in + 15 takeaway)
- Collapse mặc định, click header để mở/edit từng cái
- Storage key: `memoiresaigon_examples_v7` (bump version khi reset defaults)

### Review History
- Auto-save mỗi reply được generate
- Filter: All / Draft / Pass
- Export/Import JSON
- Status toggle: Draft ↔ Pass

### Google Sheets Sync
- URL lưu ở localStorage key: `memoiresaigon_sheets_url`
- Auto-push khi: generate mới, delete, toggle status
- Edit reply debounce 2s rồi push
- Nút "Sync từ Sheets" để pull về, "Push tất cả" để đẩy lên

---

## 5. Deploy

- Repo: `https://github.com/nhanulaw0209-cpu/memoire-review-tool`
- Branch: `main`
- Chỉ có 1 file cần commit: `review-reply-tool.html`

```bash
git add review-reply-tool.html
git commit -m "mô tả thay đổi"
git push origin main
```

---

## 6. Apps Script (Google Sheets backend)

Script đã deploy tại Google — xử lý các actions:
- `save` — thêm row mới
- `update` — sửa reply hoặc status theo ID
- `delete` — xoá row theo ID
- `bulkSave` — push nhiều items, bỏ qua ID đã tồn tại
- `GET` — trả về toàn bộ rows

Sheet name: `Reviews` — tự tạo nếu chưa có, với header row màu đỏ.

---

## 7. Khi được giao việc

- Hỏi xem muốn thay đổi **prompt/persona**, **UI**, hay **tính năng mới**
- Sau khi sửa HTML: bump `STORAGE_KEY` version nếu thay đổi DEFAULT_EXAMPLES
- Luôn test bằng cách `open review-reply-tool.html` trước khi commit
- Commit và push lên `main` khi chị confirm xong
