# Bài 2: Tái cấu trúc lịch sử commit bằng Interactive Rebase

## Mục tiêu
- Sử dụng công cụ tương tác `git rebase -i` để chỉnh sửa lịch sử commit cục bộ.
- Thực hiện gộp nhiều commit vụn vặt (`squash`), sửa thông điệp (`reword`), và xóa commit rác (`drop`).

---

## 1. Bối cảnh & Chuẩn bị Commit thử nghiệm

Tạo lần lượt 4 commit trên nhánh làm việc hiện tại:
1. Tạo `auth.js` -> commit: `feat: khoi tao module auth`
2. Sửa `auth.js` -> commit: `fix typo`
3. Sửa `auth.js` -> commit: `adds utility functions`
4. Tạo `temp.txt` -> commit: `add temp file for debug`

**Lịch sử ban đầu (`git log --oneline`):**
```text
4a3b2c1 (HEAD -> main) add temp file for debug
3f2e1d0 adds utility functions
2c1b0a9 fix typo
1a0987b feat: khoi tao module auth
```

---

## 2. Thao tác Interactive Rebase (`git rebase -i HEAD~4`)

Chạy lệnh rebase tương tác lùi về 4 commit:
```bash
git rebase -i HEAD~4
```

### Màn hình cấu hình Interactive Rebase (`todo list`):
```text
pick 1a0987b feat: khoi tao module auth
squash 2c1b0a9 fix typo
squash 3f2e1d0 adds utility functions
drop 4a3b2c1 add temp file for debug

# Rebase 1a0987b..4a3b2c1 onto 1a0987b (4 commands)
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# s, squash = use commit, but meld into previous commit
# d, drop = remove commit
```

**Giải thích hành động:**
- `pick`: Giữ lại commit gốc `1a0987b`.
- `squash`: Gộp commit `2c1b0a9` và `3f2e1d0` vào commit gốc phía trước.
- `drop`: Xóa hoàn toàn commit `4a3b2c1` chứa file rác `temp.txt`.

### Cập nhật thông điệp commit gộp:
Trình soạn thảo mở ra để nhập thông điệp commit mới sau khi gộp:
```text
feat: hoan thien module authentication
```

---

## 3. Kết quả sau khi Rebase

Chạy lệnh kiểm tra lịch sử commit:
```bash
git log --oneline
```

**Kết quả màn hình `git log --oneline` sạch sẽ:**
```text
e8d7c6b (HEAD -> main) feat: hoan thien module authentication
9f8e7d6 Initial commit for PTIT_CNTT1_IT209_Session5_Bai2
```

**Kiểm tra các tệp tin hiện tại:**
- `auth.js`: Tồn tại với đầy đủ mã nguồn đã tích hợp.
- `temp.txt`: Đã bị xóa hoàn toàn khỏi lịch sử và thư mục làm việc.
