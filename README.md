# Báo cáo Bài 2: Tái cấu trúc lịch sử commit bằng Interactive Rebase

## 1. Thông tin bài tập
- **Bài thực hành**: Bài 2 - Tái cấu trúc lịch sử commit bằng Interactive Rebase
- **Đường dẫn nộp bài**: `homework/session_05/ex2/`
- **Môn học / Khóa học**: Quản lý phiên bản với Git (IT209 - Session 05)

---

## 2. Mục tiêu
- Nắm vững nguyên lý và phương pháp sử dụng công cụ **Interactive Rebase** (`git rebase -i`) để biên tập, dọn dẹp lịch sử commit cục bộ trước khi đẩy (*push*) lên nhánh chung hoặc remote repository.
- Thành thạo thao tác gộp nhiều commit nhỏ lẻ (*squash*) thành một commit có ý nghĩa logic duy nhất.
- Đổi tên thông điệp commit (*reword*) theo chuẩn quy ước (Conventional Commits).
- Loại bỏ hoàn toàn các commit chứa tệp rác thử nghiệm (*drop*), giữ cho cây lịch sử sạch sẽ và chuyên nghiệp.

---

## 3. Bối cảnh & Chuẩn bị môi trường

Trong quá trình phát triển tính năng xác thực người dùng (`module authentication`), lập trình viên thường tạo nhiều commit thử nghiệm vụn vặt và thông điệp chưa chuẩn:
1. `feat: khoi tao module auth` (Tạo `auth.js`)
2. `fix typo` (Sửa lỗi chính tả trong `auth.js`)
3. `adds utility functions` (Bổ sung hàm tiện ích vào `auth.js`)
4. `add temp file for debug` (Tạo tệp rác `temp.txt` để gỡ lỗi)

### 3.1. Các lệnh khởi tạo tình huống giả lập
```bash
# 1. Khởi tạo repository và commit gốc
git init
echo "# Authentication Service" > README.md
git add README.md
git commit -m "Initial commit"

# 2. Commit 1: Khởi tạo module auth
echo "// auth module" > auth.js
git add auth.js
git commit -m "feat: khoi tao module auth"

# 3. Commit 2: Sửa lỗi chính tả
echo "// auth module fixed" > auth.js
git add auth.js
git commit -m "fix typo"

# 4. Commit 3: Bổ sung tiện ích
echo "// auth module fixed + utils" > auth.js
git add auth.js
git commit -m "adds utility functions"

# 5. Commit 4: Tệp rác debug
echo "debug data" > temp.txt
git add temp.txt
git commit -m "add temp file for debug"
```

### 3.2. Lịch sử commit ban đầu (chưa dọn dẹp)
Chạy lệnh kiểm tra:
```bash
git log --oneline
```

**Kết quả:**
```text
4785302 (HEAD -> master) add temp file for debug
eaa38fb adds utility functions
7b36359 fix typo
98cae19 feat: khoi tao module auth
7d27823 Initial commit
```

---

## 4. Thực hiện Interactive Rebase

Để tái cấu trúc lại 4 commit gần nhất tính từ đỉnh `HEAD`, thực thi lệnh:
```bash
git rebase -i HEAD~4
```

### 4.1. Giao diện soạn thảo Git Interactive Rebase (Gốc)
Khi lệnh chạy, Git mở trình soạn thảo mặc định (Vim/Nano/VS Code) hiển thị danh sách các commit theo thứ tự từ cũ nhất đến mới nhất:

```text
pick 98cae19 feat: khoi tao module auth
pick 7b36359 fix typo
pick eaa38fb adds utility functions
pick 4785302 add temp file for debug

# Rebase 7d27823..4785302 onto 7d27823 (4 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but discard only this commit's log message
# d, drop <commit> = remove commit
# ...
```

---

### 4.2. Cấu hình lại hành động (Pick, Squash, Drop)
Theo yêu cầu đề bài:
1. **Gộp Commit 1, 2, 3 thành 1 commit duy nhất**:
   - Dòng 1 (`98cae19`): Giữ nguyên `pick` (hoặc đổi thành `reword`/`r` để sửa thông điệp).
   - Dòng 2 (`7b36359`): Đổi thành `squash` (hoặc `s`) để gộp vào commit trước.
   - Dòng 3 (`eaa38fb`): Đổi thành `squash` (hoặc `s`) để gộp tiếp vào commit trước.
2. **Xóa bỏ commit chứa file rác (`temp.txt`)**:
   - Dòng 4 (`4785302`): Đổi thành `drop` (hoặc `d`) hoặc xóa hẳn dòng này khỏi tệp cấu hình.

**Nội dung sau khi chỉnh sửa trong trình soạn thảo:**
```text
pick 98cae19 feat: khoi tao module auth
squash 7b36359 fix typo
squash eaa38fb adds utility functions
drop 4785302 add temp file for debug
```

---

### 4.3. Soạn thảo thông điệp commit sau khi gộp
Sau khi lưu và đóng trình soạn thảo cấu hình rebase, Git sẽ thực hiện gộp commit và tự động mở màn hình soạn thảo thông điệp commit tổng hợp:

```text
# This is a combination of 3 commits.
# This is the 1st commit message:

feat: khoi tao module auth

# This is the commit message #2:

fix typo

# This is the commit message #3:

adds utility functions

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
```

**Thực hiện chỉnh sửa:** Xóa toàn bộ các thông điệp cũ và thay thế bằng thông điệp chuẩn duy nhất:
```text
feat: hoan thien module authentication
```

Lưu và đóng trình soạn thảo. Git sẽ hoàn tất quá trình rebase.

**Thông báo thành công từ Git:**
```text
[detached HEAD ed3df7a] feat: hoan thien module authentication
 Date: Mon Oct 5 00:14:09 2026 +0700
 1 file changed, 1 insertion(+)
 create mode 100644 auth.js
Successfully rebased and updated refs/heads/master.
```

---

## 5. Kiểm tra & Xác nhận kết quả

### 5.1. Kiểm tra lịch sử commit sau khi rebase
Chạy lệnh:
```bash
git log --oneline
```

**Kết quả mong đợi:**
```text
ed3df7a (HEAD -> master) feat: hoan thien module authentication
7d27823 Initial commit
```
✅ 3 commit nhỏ lẻ đã được gộp thành duy nhất một commit sạch sẽ mang thông điệp: `feat: hoan thien module authentication`.
✅ Commit rác `4785302 add temp file for debug` đã hoàn toàn biến mất khỏi lịch sử commit.

---

### 5.2. Kiểm tra trạng thái tệp nguồn
1. Kiểm tra tệp rác `temp.txt`:
   ```bash
   ls temp.txt
   # Hoặc trên PowerShell:
   Test-Path temp.txt
   ```
   **Kết quả:** Trả về `False` (tệp rác `temp.txt` đã bị xóa hoàn toàn khỏi cả cây thư mục lẫn lịch sử Git).

2. Kiểm tra tệp `auth.js`:
   ```bash
   cat auth.js
   # Hoặc trên PowerShell:
   Get-Content auth.js
   ```
   **Kết quả:**
   ```javascript
   // auth module fixed + utils
   ```
   ✅ Toàn bộ nội dung hoàn chỉnh của module xác thực vẫn được giữ nguyên đầy đủ.

3. Kiểm tra trạng thái làm việc:
   ```bash
   git status
   ```
   **Kết quả:**
   ```text
   On branch master
   nothing to commit, working tree clean
   ```

---

## 6. Bảng tổng kết các lệnh phổ biến trong Interactive Rebase

| Lệnh / Phím tắt | Tên đầy đủ | Ý nghĩa & Ứng dụng |
|:---|:---|:---|
| `p` | `pick` | Giữ nguyên commit này trong lịch sử. |
| `r` | `reword` | Giữ commit nhưng cho phép sửa lại thông điệp commit. |
| `e` | `edit` | Tạm dừng tại commit để sửa nội dung tệp hoặc chia nhỏ commit (`git commit --amend`). |
| `s` | `squash` | Gộp commit vào commit liền trước và kết hợp thông điệp lại. |
| `f` | `fixup` | Gộp commit vào commit liền trước nhưng loại bỏ thông điệp của commit này. |
| `d` | `drop` | Xóa bỏ hoàn toàn commit cùng toàn bộ thay đổi mà nó tạo ra. |

---

## 7. Lưu ý quan trọng khi sử dụng Git Rebase
> [!WARNING]
> **Quy tắc vàng của Rebase (The Golden Rule of Rebasing):**
> **Không bao giờ rebase các commit đã được đẩy (push) lên nhánh công khai (public branch)** như `main`, `master` hoặc nhánh đang làm việc chung với thành viên khác. Rebase sẽ tạo ra các mã hash commit mới, làm thay đổi lịch sử và dẫn đến xung đột nghiêm trọng cho các cộng sự.
> 
> Chỉ nên dùng `git rebase -i` trên nhánh cục bộ cá nhân trước khi mở Pull Request / Merge Request.
