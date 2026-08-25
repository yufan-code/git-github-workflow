---
name: git-github-workflow
description: 提供「本機專案 → 建立 Git 版控 → 上傳/推送到 GitHub」的完整標準流程與檢查清單,包含 git init、.gitignore、首次 commit、在 GitHub 建立遠端 repo、設定 remote、push,以及後續日常的 add/commit/push 與分支(branch)/Pull Request 協作流程。當使用者提到「上傳到 GitHub」、「把專案 push 上去」、「建立 git repo」、「git 工作流程」、「怎麼用 git 管理這個專案」、「連結 GitHub remote」或類似需求時,務必使用這個 skill 來給出步驟,不要只憑記憶隨口回答,因為使用者常會需要依照自己環境調整指令細節(例如驗證方式、分支保護、既有 remote 衝突處理)。此 skill 不綁定特定技術棧,適用任何語言/框架的專案。
---

# Git → GitHub 工作流程

這是一份可直接照抄執行的標準流程,涵蓋「從零開始的專案」到「日常協作」的完整生命週期。使用時請依使用者實際情況(是否已有 git repo、是否已有 GitHub remote、是否多人協作)跳過不需要的步驟。

## 使用原則

- 每個步驟先說明「為什麼」,再給指令,方便使用者判斷是否適用自己的情境。
- 涉及「推送到遠端」、「建立/修改 GitHub 上的 repo」這類會影響到 GitHub 帳號或他人可見狀態的動作,執行前務必先跟使用者確認(repo 名稱、public/private、要推送的分支),不要自作主張直接推送。
- 若使用者已經有 git repo 或已有遠端,不要重新 `git init`,改成從對應步驟接續。先用 `git status`、`git remote -v` 確認現況再動作。

## 階段一:本機初始化

```bash
mkdir my-project && cd my-project
git init
```

若專案已存在但還不是 git repo,直接在專案根目錄執行 `git init` 即可,不需要新建資料夾。

## 階段二:建立基本檔案

- 撰寫程式碼。
- 建立 `.gitignore`,排除不該進版控的檔案(依語言而定,例如 `node_modules/`、`.venv/`、`__pycache__/`、`.env`、建置產物等)。忘記加 `.gitignore` 是最常見的「密鑰或大型檔案不小心被推上 GitHub」的原因,務必在第一次 commit 前處理好。
- 建議加上 `README.md` 簡述專案用途。

## 階段三:第一次提交

```bash
git add .
git status          # 先看清單,確認沒有不該提交的檔案(例如 .env、金鑰、大型二進位檔)
git commit -m "Initial commit"
```

`git status` 這一步不是形式,是避免密鑰外洩的最後防線 —— commit 之後歷史紀錄很難徹底清乾淨,務必在 commit 前檢查一次。

## 階段四:在 GitHub 建立遠端 repo(務必先手動建立,再 push)

**先在 GitHub 網頁手動建好空的 repo,再進行下一步的 push**,不要依賴 `gh repo create ... --push` 一步到位 —— 這個指令需要 token 具備「建立新 repository」的帳號層級權限(fine-grained PAT 的 Account permissions → Administration),但多數情境下(尤其是別人給的、或先前建立的 fine-grained token)並沒有開這項權限,實測會直接失敗於 `Resource not accessible by personal access token (createRepository)`。手動在網頁建立可以完全避開這個問題。

1. 登入 [github.com](https://github.com),右上角 `+` → `New repository`。
2. 填寫 repo 名稱,選擇 Public 或 Private。
3. **若本機已有內容,建立時不要勾選自動產生 README / .gitignore / LICENSE**,否則遠端和本機歷史會分岔,push 時容易衝突。
4. 建立後會拿到遠端網址,例如:
   `https://github.com/<帳號或組織>/my-project.git`

只有在確定目前登入的 token/帳號有帳號層級的 repo 建立權限(例如透過 `gh auth login` 瀏覽器登入,而非 fine-grained PAT)時,才可以省略手動建立這一步,改用一步到位的指令:

```bash
gh repo create <帳號或組織>/my-project --public --source=. --remote=origin --push
```

## 階段五:連結遠端並推送

```bash
git remote add origin https://github.com/<帳號或組織>/my-project.git
git branch -M main
git push -u origin main
```

- 若該遠端已存在但 URL 不同,用 `git remote set-url origin <新網址>` 修改,不要重複 `git remote add`。
- 第一次推送若需要身分驗證,建議用 `gh auth login`,或使用 Personal Access Token 取代密碼(GitHub 已不支援帳密登入 push)。
- 常見錯誤與對應:
  - `Repository not found` → 通常是網址打錯、repo 是 private 但目前登入帳號沒有權限,或 gh 認證的帳號不是 repo 擁有者。
  - `Permission ... denied` / `Write access to repository not granted`(HTTP 403)→ 即使 `gh api repos/<owner>/<repo> --jq .permissions` 顯示 `push: true`,實際推送仍可能被拒,因為那個欄位反映的是**帳號**在該 repo 的角色,不是**token 本身**的授權範圍。fine-grained PAT 常見兩個坑:(1) repo 是新建的,還沒被加進 token 的 Repository access 清單;(2) Permissions → Contents 停留在預設的 Read-only。到 `github.com/settings/tokens?type=beta` 把目標 repo 加入清單、Contents 改成 Read and write,存檔立即生效,不用重新產生 token,改完直接重試 push 即可。
  - `failed to push...fetch first` → 遠端有本機沒有的 commit(例如遠端建立時勾了自動產生檔案),先 `git pull --rebase origin main` 再 push。

## 階段六:日常開發流程

單人開發,直接在 main(或預設分支)上迭代:

```bash
git add <變更的檔案>
git commit -m "說明這次改了什麼"
git push
```

多人協作或想避免直接改動 main 時,改用分支 + PR 流程:

```bash
git checkout -b feature/xxx     # 開新分支
git push -u origin feature/xxx  # 推送分支
# 到 GitHub 開 Pull Request,經過 review 後再合併到 main
```

合併後記得同步回本機:

```bash
git checkout main
git pull
```

## 跨帳號 / 搬移 repo

如果需求是「把某個 repo 複製/搬到另一個 GitHub 帳號或組織」,或是「gh 目前登入的帳號不是要 push 的目標帳號」,這已經超出本 skill 的標準流程範圍,改用 `github-repo-migrate` skill 處理帳號切換、認證與跨帳號複製的細節。
