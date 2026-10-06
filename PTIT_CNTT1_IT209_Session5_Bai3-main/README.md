# Bài 3: Xử lý xung đột phức tạp trong quá trình Rebase

## Mục tiêu
- Hiểu quy trình giải quyết xung đột từng bước (step-by-step conflict resolution) trong Interactive/Standard Rebase.
- Đưa lịch sử nhánh tính năng (`feature-api`) lên trên đầu nhánh chính (`main`) một cách thẳng hàng mà không tạo Merge Commit phụ.

---

## 1. Kịch bản mô phỏng xung đột

### Trên nhánh `main`:
1. Khởi tạo `config.json`:
   ```json
   {
     "port": 8080,
     "debug": false
   }
   ```
   Commit: `init config`
2. Sửa `"port": 8081` -> Commit: `update port on main`
3. Thêm `"env": "production"` -> Commit: `add env config`

### Trên nhánh `feature-api` (tách ra từ commit `init config`):
1. Sửa `"port": 9000` -> Commit: `feat: change port`
2. Sửa `"debug": true` -> Commit: `feat: enable debug`

---

## 2. Tiến trình Rebase & Giải quyết Xung đột Từng chặng

Chuyển sang nhánh `feature-api` và thực hiện Rebase lên `main`:
```bash
git checkout feature-api
git rebase main
```

### Chặng 1: Xung đột tại commit `feat: change port`
Màn hình thông báo conflict tại `config.json`:
```text
Auto-merging config.json
CONFLICT (content): Merge conflict in config.json
error: Could not apply 1a2b3c4... feat: change port
```

**Nội dung `config.json` bị xung đột:**
```json
<<<<<<< HEAD
  "port": 8081,
  "env": "production",
=======
  "port": 9000,
>>>>>>> feat: change port
```

**Cách giải quyết chặng 1:**
Chỉnh sửa file `config.json` kết hợp cả thay đổi từ `main` và `feature-api`:
```json
{
  "port": 9000,
  "debug": false,
  "env": "production"
}
```

Lưu file và tiếp tục rebase:
```bash
git add config.json
git rebase --continue
```

---

### Chặng 2: Rebase tự động áp dụng commit `feat: enable debug`
Do chặng 1 đã hòa nhập đầy đủ cấu hình `"env": "production"`, commit tiếp theo `feat: enable debug` tự động khớp mã nguồn.

Sửa đổi file `config.json` cuối cùng:
```json
{
  "port": 9000,
  "debug": true,
  "env": "production"
}
```

---

## 3. Kiểm tra kết quả Sơ đồ Lịch sử Commit

Chạy lệnh kiểm tra cây commit:
```bash
git log --graph --oneline
```

**Màn hình xuất kết quả `git log --graph --oneline`:**
```text
* d4c3b2a (HEAD -> feature-api) feat: enable debug
* c3b2a1f feat: change port
* b2a1f0e (main) add env config
* a1f0e9d update port on main
* 9f8e7d6 init config
```

*Nhận xét:*
- Lịch sử Git hoàn toàn **thẳng tắp** (Linear History), không có bất kỳ Merge Commit phụ nào.
- Các commit của `feature-api` nằm nối tiếp ngay sau commit mới nhất của `main`.
