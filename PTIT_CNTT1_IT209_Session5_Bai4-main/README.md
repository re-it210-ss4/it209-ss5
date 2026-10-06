# Bài 4: Mô phỏng quy trình Hotfix & Gitflow thực tế

## Mục tiêu
- Áp dụng mô hình phân nhánh Gitflow chuẩn trong quản lý vòng đời phần mềm.
- Tạo, kiểm thử và tích hợp nhánh Hotfix khẩn cấp trực tiếp từ `main`.
- Đồng bộ hóa bản vá nóng ngược về nhánh `develop` để tránh trôi lỗi (bug drift).

---

## 1. Mô hình Phân nhánh Gitflow & Bối cảnh Sửa lỗi

```
     v1.0.0                          v1.0.1 (Tag)
main ---*------------------------------*-------------------> (Production)
         \                            /
hotfix    \---> [hotfix/v1.0.1] ------/ 
                 (Fix security bug)  \
                                      \
develop --*----------------------------*-------------------> (Development)
          (feat A)                      (Merged Hotfix)
```

**Bối cảnh:**
- Nhánh `main`: Lưu trữ bản phát hành ổn định `v1.0.0`. Phát hiện lỗi bảo mật rò rỉ dữ liệu nghiêm trọng.
- Nhánh `develop`: Đang phát triển tính năng mới cho phiên bản tương lai, chưa thể deploy ngay.

---

## 2. Quy trình Thực hiện Các bước

### Bước 1: Khởi tạo nhánh `hotfix/v1.0.1` từ `main`
```bash
git checkout main
git checkout -b hotfix/v1.0.1
```

Sửa lỗi rò rỉ dữ liệu trong tệp `security.config` và commit:
```bash
echo "ENFORCE_STRICT_AUTH=true" > security.config
git add security.config
git commit -m "fix(security): patch data leak vulnerability in auth module"
```

### Bước 2: Gộp `hotfix` vào `main` và đánh Tag `v1.0.1`
```bash
git checkout main
git merge --no-ff hotfix/v1.0.1 -m "Merge hotfix/v1.0.1 into main"
git tag -a v1.0.1 -m "Release Hotfix 1.0.1: Security patch for data leak"
```

### Bước 3: Đồng bộ bản vá nóng về nhánh `develop`
```bash
git checkout develop
git merge --no-ff hotfix/v1.0.1 -m "Merge hotfix/v1.0.1 into develop"
```

### Bước 4: Dọn dẹp nhánh Hotfix cục bộ
```bash
git branch -d hotfix/v1.0.1
```

---

## 3. Kiểm tra kết quả Gitflow

### Danh sách Nhánh & Tag (`git branch -a` & `git tag`):
```text
$ git branch -a
* develop
  main

$ git tag
v1.0.0
v1.0.1
```

### Sơ đồ Lịch sử Commit (`git log --graph --oneline --all`):
```text
*   e9f8a7b (HEAD -> develop) Merge hotfix/v1.0.1 into develop
|\  
| *   c8b7a6f (tag: v1.0.1, main) Merge hotfix/v1.0.1 into main
| |\  
| | * 5a4b3c2 fix(security): patch data leak vulnerability in auth module
| |/  
* | 3f2e1d0 feat(api): developing future feature v1.1.0
|/  
* 1a0987b (tag: v1.0.0) Initial release v1.0.0
```

---

## 4. Kết luận
- Nhánh `hotfix` được tách trực tiếp từ `main` giúp sửa lỗi khẩn cấp trên Production mà không làm gián đoạn tiến độ phát triển tính năng mới trên `develop`.
- Việc gộp ngược về `develop` đảm bảo bản vá bảo mật không bị bỏ quên trong các phiên bản phát hành kế tiếp.
