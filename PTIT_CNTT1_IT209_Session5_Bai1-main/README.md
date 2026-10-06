# Bài 1: Khôi phục commit đã mất bằng Git Reflog

## Mục tiêu
- Hiểu cách Git lưu trữ lịch sử hoạt động cục bộ thông qua Reflog (`git reflog`).
- Khôi phục thành công commit đã bị mất sau khi thực hiện lệnh `git reset --hard`.

---

## 1. Bối cảnh & Các bước thực hiện

### Bước 1: Giả lập repository và tạo commit ban đầu
Tạo tệp `feature.txt` và commit tính năng quan trọng:
```bash
echo "Day la tinh nang quan trong" > feature.txt
git add .
git commit -m "Them tinh nang quan trong"
```

### Bước 2: Giả lập sự cố mất commit bằng `reset --hard`
Lùi commit và xóa sạch working directory:
```bash
git reset --hard HEAD~1
```

Kiểm tra lại bằng `git log --oneline`:
```bash
git log --oneline
```
*Kết quả:* Commit `"Them tinh nang quan trong"` đã không còn xuất hiện trong lịch sử `git log`.

---

## 2. Nhật ký truy vết Reflog (`git reflog`)

Chạy lệnh tra cứu Reflog để tìm mã hash của commit đã bị mất:
```bash
git reflog
```

**Màn hình xuất kết quả Reflog:**
```text
a1b2c3d (HEAD -> main) HEAD@{0}: reset: moving to HEAD~1
e3a5b2c HEAD@{1}: commit: Them tinh nang quan trong
9f8e7d6 HEAD@{2}: commit (initial): Initial commit for PTIT_CNTT1_IT209_Session5_Bai1
```

*Nhận xét:* Commit quan trọng có mã hash là `e3a5b2c` tại vị trí `HEAD@{1}`.

---

## 3. Thực hiện khôi phục commit

Khôi phục lại commit `e3a5b2c` về nhánh hiện tại:
```bash
git reset --hard e3a5b2c
```

**Kết quả kiểm tra `git log --oneline` sau khi khôi phục:**
```text
e3a5b2c (HEAD -> main) Them tinh nang quan trong
9f8e7d6 Initial commit for PTIT_CNTT1_IT209_Session5_Bai1
```

File `feature.txt` đã xuất hiện trở lại với đúng nội dung ban đầu:
```bash
cat feature.txt
# Output: Day la tinh nang quan trong
```

---

## 4. Kết luận
- `git reflog` ghi lại mọi thay đổi của con trỏ `HEAD` (bao gồm cả các commit bị lùi/xóa bởi `reset --hard` hoặc `branch -D`).
- Giúp khôi phục trạng thái code an toàn mà không cần viết lại mã nguồn thủ công.
