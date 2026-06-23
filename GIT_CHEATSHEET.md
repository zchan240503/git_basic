# Git Cheatsheet — Tổng hợp lệnh Git
**AI Vietnam | Bài giảng: Git & GitHub for Version Control**

---

## Mục lục

1. [Cấu hình](#1-cấu-hình)
2. [Khởi tạo & Sao chép](#2-khởi-tạo--sao-chép)
3. [4 Trạng thái & Vòng đời file](#3-4-trạng-thái--vòng-đời-file)
4. [Staging & Commit](#4-staging--commit)
5. [Xem lịch sử](#5-xem-lịch-sử)
6. [Hoàn tác thay đổi](#6-hoàn-tác-thay-đổi)
7. [Branch — Tạo, chuyển, gộp](#7-branch--tạo-chuyển-gộp)
8. [Remote — GitHub / GitLab](#8-remote--github--gitlab)
9. [Tagging](#9-tagging)
10. [Rebase](#10-rebase)
11. [Stash](#11-stash)
12. [Git Hooks](#12-git-hooks)
13. [Cấu hình nâng cao](#13-cấu-hình-nâng-cao)
14. [Aliases hữu ích](#14-aliases-hữu-ích)
15. [Vibe Coding Workflow](#15-vibe-coding-workflow)
16. [Giải quyết sự cố thường gặp](#16-giải-quyết-sự-cố-thường-gặp)

---

## 1. Cấu hình

Thiết lập một lần duy nhất trên mỗi máy — Git ghi nhớ vĩnh viễn.

```bash
# Thông tin cá nhân (bắt buộc — xuất hiện trong mỗi commit)
git config --global user.name "Nguyễn Thái Hà"
git config --global user.email "email@example.com"

# Editor để viết commit message dài
git config --global core.editor "code --wait"    # VS Code
git config --global core.editor "nano"           # nano (đơn giản hơn)

# Tên nhánh mặc định khi git init
git config --global init.defaultBranch main

# Màu sắc terminal
git config --global color.ui auto

# Hành vi khi git pull
git config --global pull.rebase false            # dùng merge (khuyến nghị cho người mới)

# Xem toàn bộ cấu hình hiện tại
git config --global --list

# Xem giá trị một key cụ thể
git config --global user.name

# Xóa một key
git config --global --unset core.editor

# Mở file .gitconfig trong editor
git config --global --edit
```

> **Tại sao phải cấu hình?** Git cần biết bạn là ai để ghi vào lịch sử commit. Nếu không cấu hình, Git sẽ báo lỗi khi bạn cố gắng commit lần đầu.

---

## 2. Khởi tạo & Sao chép

```bash
# Khởi tạo repo mới trong thư mục hiện tại
git init
# → Tạo thư mục ẩn .git/ — "não bộ" của Git

# Khởi tạo với tên nhánh cụ thể
git init -b main

# Sao chép repo từ xa (HTTPS)
git clone https://github.com/user/repo.git
# → Tải về toàn bộ lịch sử và tất cả nhánh

# Sao chép qua SSH (khuyến nghị — không cần nhập mật khẩu)
git clone git@github.com:user/repo.git

# Sao chép vào thư mục với tên khác
git clone https://github.com/user/repo.git ten-thu-muc-moi

# Sao chép chỉ nhánh cụ thể (nhanh hơn khi repo lớn)
git clone --branch develop --single-branch https://github.com/user/repo.git

# Sao chép nông (shallow) — chỉ lấy N commits gần nhất
git clone --depth 10 https://github.com/user/repo.git
```

> **`git init` vs `git clone`:** Dùng `init` khi bắt đầu project mới từ đầu. Dùng `clone` khi muốn làm việc với project đã có trên remote.

---

## 3. 4 Trạng thái & Vòng đời file

```
Untracked  ──git add──▶  Staged  ──git commit──▶  Committed
                                                        │
   Modified  ◀──────────────────── chỉnh sửa ──────────┘
```

| Trạng thái | `git status` hiển thị | Ý nghĩa | Hành động tiếp |
|------------|----------------------|---------|----------------|
| **Untracked** | `Untracked files:` | File mới, Git chưa biết đến | `git add` |
| **Modified** | `Changes not staged:` | Đã sửa so với commit trước, chưa staged | `git add` |
| **Staged** | `Changes to be committed:` | Sẵn sàng được commit | `git commit` |
| **Committed** | *(không hiển thị)* | Đã lưu an toàn trong lịch sử Git | *(không cần gì)* |

```bash
# Xem trạng thái hiện tại (lệnh dùng nhiều nhất)
git status

# Xem trạng thái dạng ngắn gọn
git status -s
# M = Modified, A = Added (staged), ? = Untracked, D = Deleted
```

---

## 4. Staging & Commit

### git add — Đưa file vào Staging Area

```bash
# Thêm một file cụ thể
git add tên-file.py

# Thêm nhiều file
git add file1.py file2.py

# Thêm tất cả file trong thư mục hiện tại (cẩn thận!)
git add .

# Thêm tất cả file có extension cụ thể
git add *.py

# Thêm từng hunk (đoạn) trong file — chọn lọc chi tiết
git add -p tên-file.py
# → Hỏi từng hunk: y (yes), n (no), s (split), ? (help)

# Xem những gì sẽ được commit (diff của staged changes)
git diff --staged
```

> **Tại sao cần Staging Area?** Nếu đang sửa 10 files cho 3 tính năng khác nhau, staging cho phép bạn commit từng nhóm file riêng — lịch sử Git sạch và có ý nghĩa hơn.

### git commit — Lưu thay đổi vào lịch sử

```bash
# Commit với message ngắn (phổ biến nhất)
git commit -m "feat: thêm hàm validate_email()"

# Mở editor để viết message dài hơn
git commit

# Commit tất cả file đã tracked (bỏ qua bước git add)
git commit -a -m "fix: sửa lỗi logic trong calculate()"
# Cẩn thận: không thêm file Untracked

# Commit rỗng (hữu ích để trigger CI/CD)
git commit --allow-empty -m "chore: trigger CI"

# Sửa commit cuối (CHỈ khi chưa push)
git commit --amend -m "feat: message đúng hơn"
git commit --amend --no-edit    # giữ nguyên message, chỉ thêm file
```

### .gitignore — File Git bỏ qua

```bash
# Tạo .gitignore
touch .gitignore

# Kiểm tra file nào bị ignore
git status --ignored

# Kiểm tra tại sao một file bị ignore
git check-ignore -v tên-file.log

# Force thêm file bị ignore (dùng cẩn thận)
git add -f tên-file.log
```

**Ví dụ .gitignore phổ biến:**
```
.env           # biến môi trường và secrets
__pycache__/   # Python cache
node_modules/  # Node dependencies
*.log          # log files
/build/        # build output
.DS_Store      # macOS metadata
```

### Conventional Commits — Format message chuẩn

```
<type>(<scope>): <mô tả ngắn>

# Types:
feat     → tính năng mới
fix      → sửa bug
docs     → thay đổi documentation
style    → format, không thay đổi logic
refactor → cải thiện cấu trúc, không thêm/bớt feature
test     → thêm/sửa tests
chore    → build, deps, config

# Ví dụ:
feat: add user authentication
fix(auth): resolve token expiry bug
feat: AI-generated error handling for calculator
docs: update README with setup instructions
```

---

## 5. Xem lịch sử

```bash
# Log đầy đủ (hash, author, date, message)
git log

# Một dòng mỗi commit (dùng nhiều nhất)
git log --oneline

# Cây lịch sử visual cho tất cả nhánh
git log --oneline --graph --all
git log --oneline --graph --decorate --all

# Kèm thống kê số dòng thay đổi
git log --stat

# Kèm toàn bộ diff (xem code thay đổi)
git log -p

# Chỉ xem N commits gần nhất
git log -5
git log -p -2               # diff của 2 commits gần nhất

# Lọc theo thời gian
git log --since="2 weeks ago"
git log --since="2026-01-01" --until="2026-06-01"
git log --since="yesterday"

# Lọc theo tác giả
git log --author="Nguyen Thai Ha"
git log --author="@gmail.com"    # lọc theo email

# Lọc theo nội dung message
git log --grep="feat"
git log --grep="AI-generated"

# Lọc theo file cụ thể
git log -- path/to/file.py

# Lọc theo nội dung code (tìm commit nào đã thêm/xóa dòng này)
git log -S "tên_hàm"
git log -G "regex_pattern"

# Xem commit cụ thể
git show abc1234
git show HEAD                # commit mới nhất
git show HEAD~1              # commit trước đó 1 bước
git show HEAD~3              # 3 bước trước

# Xem ai sửa từng dòng (blame)
git blame tên-file.py
git blame -L 10,20 tên-file.py    # chỉ xem dòng 10-20

# So sánh 2 commits
git diff abc1234 def5678
git diff HEAD~3 HEAD

# So sánh 2 nhánh
git diff main feature/login

# Xem thay đổi chưa staged
git diff

# Xem thay đổi đã staged
git diff --staged
git diff --cached            # tương đương --staged
```

> **Mẹo:** `git log --oneline --graph --all` là lệnh dùng nhiều nhất để "nhìn thấy" toàn bộ cây lịch sử. Đặt alias: `git config --global alias.lg "log --oneline --graph --decorate --all"`

---

## 6. Hoàn tác thay đổi

### Bảng quyết định nhanh

| Tình huống | Lệnh | An toàn? |
|------------|------|----------|
| Chưa staged, muốn discard | `git restore <file>` | ⚠️ Không khôi phục được |
| Đã staged, muốn unstage | `git restore --staged <file>` | ✓ |
| Sửa commit cuối (chưa push) | `git commit --amend` | ✓ |
| Hoàn tác commit đã push | `git revert <hash>` | ✓ An toàn nhất |
| Lưu tạm thay đổi dở dang | `git stash` | ✓ |
| Quay về commit cũ (nguy hiểm) | `git reset --hard <hash>` | ✗ Mất dữ liệu |

### git restore — Khôi phục file

```bash
# Discard thay đổi chưa staged (⚠️ không khôi phục được)
git restore tên-file.py
git restore .                   # discard tất cả

# Unstage file đã staged
git restore --staged tên-file.py
git restore --staged .          # unstage tất cả

# Khôi phục file về một commit cụ thể
git restore --source HEAD~2 tên-file.py
```

### git revert — Hoàn tác commit (an toàn)

```bash
# Tạo commit mới để hoàn tác commit cụ thể
git revert abc1234

# Revert không mở editor (dùng message mặc định)
git revert abc1234 --no-edit

# Revert nhiều commits
git revert abc1234 def5678

# Revert một range commits
git revert HEAD~3..HEAD         # hoàn tác 3 commits gần nhất
```

> **Tại sao dùng `revert` thay `reset`?** `revert` tạo commit MỚI để hoàn tác — lịch sử còn nguyên, an toàn khi đã push. `reset` xóa lịch sử — nguy hiểm khi đã push.

### git reset — Đặt lại HEAD (cẩn thận)

```bash
# Soft: giữ thay đổi trong staged (commit biến mất, code còn)
git reset --soft HEAD~1

# Mixed (mặc định): giữ thay đổi trong working dir, bỏ khỏi staged
git reset HEAD~1
git reset --mixed HEAD~1

# Hard: XÓA HOÀN TOÀN — code không còn (⚠️ nguy hiểm)
git reset --hard HEAD~1
git reset --hard abc1234

# Reset về trạng thái remote (⚠️ xóa mọi thay đổi local)
git reset --hard origin/main
```

---

## 7. Branch — Tạo, chuyển, gộp

### Tạo và chuyển nhánh

```bash
# Xem danh sách nhánh local (* = nhánh hiện tại)
git branch

# Xem nhánh local với thông tin chi tiết
git branch -v

# Xem nhánh remote-tracking
git branch -r

# Xem tất cả nhánh (local + remote)
git branch -a

# Xem nhánh kèm nhánh remote đang theo dõi
git branch -vv

# Tạo nhánh mới (chưa chuyển sang)
git branch feature/login

# Tạo và chuyển ngay sang nhánh mới (cách nhanh nhất)
git checkout -b feature/login

# Cách mới (Git 2.23+) — rõ ràng hơn
git switch -c feature/login

# Chuyển sang nhánh đã tồn tại
git checkout main
git switch main                 # cách mới

# Chuyển lại nhánh trước đó
git checkout -
git switch -
```

### Đổi tên và xóa nhánh

```bash
# Đổi tên nhánh hiện tại
git branch -m tên-mới

# Đổi tên nhánh khác
git branch -m tên-cũ tên-mới

# Xóa nhánh đã merge
git branch -d feature/login

# Xóa nhánh chưa merge (cẩn thận!)
git branch -D feature/login

# Xóa nhánh trên remote
git push origin --delete feature/login

# Tạo nhánh không có lịch sử (orphan branch)
git checkout --orphan gh-pages
```

### Merge — Gộp nhánh

```bash
# Merge nhánh vào nhánh hiện tại
git checkout main
git merge feature/login

# Merge với merge commit bắt buộc (không fast-forward)
git merge --no-ff feature/login

# Xem preview sẽ merge những gì
git log main..feature/login

# Hủy merge đang bị conflict
git merge --abort

# Sau khi giải quyết conflict
git add .
git commit                      # hoàn tất merge
```

**Giải quyết conflict:**
```bash
# 1. Xem file nào đang conflict
git status

# 2. Mở file — tìm markers:
# <<<<<<< HEAD
# code của nhánh hiện tại
# =======
# code của nhánh merge vào
# >>>>>>> feature/login

# 3. Sửa file, xóa markers, giữ code muốn giữ

# 4. Đánh dấu đã giải quyết
git add tên-file.py

# 5. Hoàn tất
git commit
```

### Nhánh remote

```bash
# Tạo nhánh local từ remote
git fetch origin
git checkout -b mybranch origin/feature-x
git checkout feature-x          # rút gọn nếu tên trùng

# Push nhánh mới lên remote lần đầu
git push -u origin feature/login
# -u: set upstream — sau đó chỉ cần 'git push'

# Cập nhật remote-tracking branches (không merge)
git fetch
git fetch --all                 # tất cả remotes
git fetch --prune               # xóa remote-tracking đã bị xóa

# Theo dõi nhánh remote
git branch --track feature-x origin/feature-x
git branch -u origin/develop    # đổi upstream của nhánh hiện tại
```

---

## 8. Remote — GitHub / GitLab

```bash
# Xem danh sách remote
git remote
git remote -v                   # kèm URL

# Thêm remote mới
git remote add origin git@github.com:user/repo.git
# "origin" là tên quy ước cho remote chính

# Thêm remote thứ hai
git remote add upstream git@github.com:original/repo.git

# Đổi URL remote
git remote set-url origin git@github.com:user/new-repo.git

# Xóa remote
git remote remove upstream

# Push lên remote
git push origin main
git push                        # nếu đã set upstream

# Push tất cả nhánh
git push --all origin

# Force push (⚠️ chỉ dùng trên nhánh cá nhân, KHÔNG push --force lên main)
git push --force-with-lease     # an toàn hơn --force (kiểm tra trước)
git push --force                # nguy hiểm — không kiểm tra

# Pull (fetch + merge)
git pull origin main
git pull                        # nếu đã set upstream

# Pull với rebase thay vì merge
git pull --rebase

# Fetch (tải về nhưng không merge)
git fetch origin
git fetch --all

# Merge sau khi fetch
git merge origin/main

# Xem remote info chi tiết
git remote show origin
```

---

## 9. Tagging

Tags đánh dấu các phiên bản phát hành quan trọng.

```bash
# === Annotated tag (khuyến nghị cho release) ===
# Có người tạo, ngày, message — đầy đủ thông tin
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag cho commit cụ thể (không phải HEAD)
git tag -a v0.9.0 abc1234 -m "Beta release"

# === Lightweight tag ===
# Con trỏ đơn giản đến commit, không có thông tin bổ sung
git tag v1.0.0-beta

# === Xem tags ===
git tag                         # liệt kê tất cả
git tag -l "v1.*"               # lọc theo pattern
git show v1.0.0                 # xem chi tiết tag

# === Push tags ===
git push origin v1.0.0          # push tag cụ thể
git push origin --tags          # push tất cả tags
# Lưu ý: git push mặc định KHÔNG push tags

# === Xóa tag ===
git tag -d v1.0.0-beta          # xóa local
git push origin --delete v1.0.0-beta  # xóa remote

# === Checkout tag ===
git checkout v1.0.0             # ở "detached HEAD" state
git checkout -b hotfix/v1.0.1 v1.0.0  # tạo branch từ tag
```

---

## 10. Rebase

```bash
# Rebase nhánh hiện tại lên đầu main
git checkout feature/login
git rebase main
# → Gỡ tạm commits feature, cập nhật base lên HEAD của main,
#   áp dụng lại commits feature → lịch sử tuyến tính

# Rebase tương tác (dọn dẹp lịch sử trước khi share)
git rebase -i HEAD~3            # chỉnh sửa 3 commits gần nhất
git rebase -i abc1234           # từ commit cụ thể đến HEAD

# Các lệnh trong rebase interactive:
# pick   → giữ nguyên
# reword → đổi message
# squash → gộp với commit trước (giữ message)
# fixup  → gộp với commit trước (bỏ message)
# drop   → xóa commit
# edit   → dừng lại để sửa

# Tiếp tục sau khi giải quyết conflict
git rebase --continue

# Bỏ qua commit hiện tại
git rebase --skip

# Hủy rebase, quay về trạng thái ban đầu
git rebase --abort
```

> **⚠️ Quy tắc vàng:** KHÔNG rebase nhánh đã push lên remote (shared branches). Rebase thay đổi SHA hash — gây rắc rối cho đồng đội đã pull về rồi.

---

## 11. Stash

Lưu tạm thay đổi dở dang để làm việc khác.

```bash
# Lưu tạm thay đổi hiện tại (tracked files)
git stash

# Lưu kèm message mô tả
git stash push -m "đang làm dở tính năng login"

# Lưu cả untracked files
git stash -u
git stash --include-untracked

# Xem danh sách stash
git stash list
# Output: stash@{0}: WIP on main: ...
#         stash@{1}: On feature: đang làm dở...

# Áp dụng stash gần nhất (giữ stash trong list)
git stash apply

# Áp dụng stash cụ thể
git stash apply stash@{2}

# Áp dụng và xóa khỏi list
git stash pop
git stash pop stash@{1}

# Xem nội dung stash (không áp dụng)
git stash show
git stash show -p               # kèm diff chi tiết

# Tạo branch từ stash
git stash branch feature/wip stash@{0}

# Xóa stash
git stash drop stash@{0}        # xóa một stash
git stash clear                 # xóa tất cả stash
```

> **Khi nào dùng stash?** Khi cần chuyển nhánh gấp (pull hotfix, review PR) mà không muốn commit code đang làm dở. `stash` > `commit` code tạm với message "wip".

---

## 12. Git Hooks

Scripts tự động chạy trước/sau các sự kiện Git.

```bash
# Vị trí hooks: .git/hooks/
ls .git/hooks/                  # xem hooks available (có đuôi .sample)

# Kích hoạt hook: xóa đuôi .sample + cấp quyền thực thi
cp .git/hooks/pre-commit.sample .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit

# Bypass hook tạm thời (cẩn thận)
git commit --no-verify -m "message"
```

**Các hook phổ biến:**

| Hook | Khi nào chạy | Dùng để |
|------|-------------|---------|
| `pre-commit` | Trước khi commit | Lint, test, kiểm tra secrets |
| `commit-msg` | Sau khi nhập message | Kiểm tra format message |
| `post-commit` | Sau khi commit xong | Notification, logging |
| `pre-push` | Trước khi push | Chạy full test suite |
| `pre-receive` | Server nhận push | Enforce policy |
| `post-receive` | Server sau push | Trigger CI/CD deploy |

---

## 13. Cấu hình nâng cao

### .gitattributes

```
# Chuẩn hóa line endings
* text=auto eol=lf
*.bat text eol=crlf
*.sh  text eol=lf

# Binary files
*.png  binary
*.jpg  binary
*.pdf  binary

# Diff
*.py text diff=python

# Bỏ diff cho generated files
package-lock.json -diff
```

### Cấu hình merge/diff tool

```bash
git config --global merge.tool vscode
git config --global diff.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'

# Mở diff tool
git difftool
git mergetool                   # khi đang có conflict
```

### Credential helper

```bash
# macOS — Keychain
git config --global credential.helper osxkeychain

# Linux — lưu tạm trong bộ nhớ 1 giờ
git config --global credential.helper 'cache --timeout=3600'

# Linux — lưu vĩnh viễn (không khuyến nghị máy shared)
git config --global credential.helper store
```

### Proxy (môi trường doanh nghiệp)

```bash
git config --global http.proxy http://proxy.company.com:8080
git config --global https.proxy https://proxy.company.com:8080

# Xóa proxy
git config --global --unset http.proxy
```

---

## 14. Aliases hữu ích

```bash
# Cài đặt aliases cơ bản
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br "branch -v"
git config --global alias.cm "commit -m"
git config --global alias.df diff
git config --global alias.dfs "diff --staged"
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.unstage "restore --staged"
git config --global alias.undo "reset --soft HEAD~1"

# Shell functions với alias (dùng !)
git config --global alias.nb "!f(){ git checkout -b \"\$1\"; }; f"
# Dùng: git nb feature/login

git config --global alias.ai "!f(){ git checkout -b \"ai/\$1\"; }; f"
# Dùng: git ai chatbot-feature

git config --global alias.cleanup "!git branch --merged main | grep -v '\\* main\\|develop' | xargs git branch -d"
# Dùng: git cleanup — xóa branches đã merge

git config --global alias.sync "!git fetch --all --prune && git pull origin \$(git branch --show-current)"
# Dùng: git sync — fetch + pull nhánh hiện tại
```

---

## 15. Vibe Coding Workflow

Workflow 7 bước khi làm việc với AI (Copilot, Cursor, Claude...):

```bash
# BƯỚC 1: Tạo branch với prefix ai/
git checkout -b ai/feature-name

# BƯỚC 2: Prompt AI → nhận code
# (thực hiện trong AI tool của bạn)

# BƯỚC 3: Chạy test
python -m pytest                # Python
npm test                        # Node.js

# BƯỚC 4: ⭐ ĐỌC DIFF — quan trọng nhất!
git diff                        # thay đổi chưa staged
git status                      # file nào bị ảnh hưởng
git diff --stat                 # tóm tắt nhanh

# BƯỚC 5: Commit với message rõ ràng
git add tên-file.py             # thêm có chọn lọc (không add .)
git commit -m "feat: AI-generated error handling for calculate()"

# BƯỚC 6: Nếu AI làm sai — rollback
git restore tên-file.py         # chưa staged → discard
git restore --staged tên-file.py # đã staged → unstage
git revert HEAD                 # đã commit → revert

# BƯỚC 7: Merge qua Pull Request
git push -u origin ai/feature-name
# → Mở PR trên GitHub, review, merge
```

**Chu trình lặp lại:** `prompt → test → diff → commit → (lặp) → PR → merge`

**Commit message cho AI code:**
```
feat: AI-generated <mô tả>      # tính năng mới
fix: AI-suggested <mô tả>       # sửa bug
refactor: AI-refactored <mô tả> # cải thiện cấu trúc
```

---

## 16. Giải quyết sự cố thường gặp

### "Tôi commit nhầm branch"

```bash
# Lấy hash của commit nhầm
git log --oneline -3

# Cherry-pick commit đó sang branch đúng
git checkout branch-dung
git cherry-pick abc1234

# Xóa commit khỏi branch sai
git checkout branch-sai
git reset --hard HEAD~1         # nếu commit đó là commit cuối
```

### "Tôi muốn xem code của commit cũ"

```bash
# Checkout commit cụ thể (detached HEAD)
git checkout abc1234

# Tạo branch từ đó để làm việc
git checkout -b branch-tu-commit-cu

# Quay về HEAD
git checkout main
```

### "Conflict khi merge/rebase"

```bash
# Xem file nào conflict
git status

# Sau khi sửa file
git add tên-file.py
git commit                      # nếu merge
git rebase --continue           # nếu rebase

# Hủy và quay về trạng thái trước
git merge --abort
git rebase --abort
```

### "Tôi muốn tìm commit nào đã gây ra bug"

```bash
# Git bisect — tìm nhị phân
git bisect start
git bisect bad                  # commit hiện tại có bug
git bisect good abc1234         # commit cũ không có bug
# → Git checkout commit giữa, test, rồi:
git bisect good                 # hoặc git bisect bad
# → Lặp lại cho đến khi tìm được commit gây bug
git bisect reset                # kết thúc
```

### "Remote bị từ chối push (rejected)"

```bash
# Lý do phổ biến: remote có commits mà local chưa có
git pull --rebase               # pull + rebase
git push                        # push lại

# Nếu vẫn bị từ chối (đã rewrite history — chỉ dùng trên nhánh cá nhân)
git push --force-with-lease     # an toàn hơn --force
```

### "Tôi cần xem file .git/config"

```bash
cat .git/config                 # config của repo hiện tại
cat ~/.gitconfig                # config toàn cục

# Cấu hình repo (ghi đè global)
git config user.email "work@company.com"    # không có --global
```

---

## Quick Reference Card

```
SETUP         git config --global user.name/email/editor
INIT          git init | git clone <url>
STATUS        git status | git diff | git diff --staged
STAGING       git add <file> | git add . | git add -p
COMMIT        git commit -m "msg" | git commit --amend
LOG           git log --oneline --graph --all
UNDO          git restore <file> | git revert <hash> | git reset --soft HEAD~1
STASH         git stash | git stash pop | git stash list
BRANCH        git checkout -b <name> | git merge <name> | git branch -d <name>
REMOTE        git remote add origin <url> | git push | git pull | git fetch
TAG           git tag -a v1.0 -m "msg" | git push origin --tags
REBASE        git rebase main | git rebase -i HEAD~3
```

---

*Tham khảo thêm: git-scm.com/book | Pro Git Book (miễn phí, có tiếng Việt)*
*Thực hành: github.com/pcottle/learnGitBranching (interactive)*
