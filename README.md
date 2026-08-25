# git-github-workflow

一個 [Claude Code](https://claude.com/claude-code) skill,提供「本機專案 → 建立 Git 版控 → 上傳/推送到 GitHub」的完整標準流程與檢查清單。內容以繁體中文撰寫,不綁定特定程式語言或框架,適用任何專案。

## 這個 skill 做什麼

當你請 Claude Code 幫忙「把專案上傳到 GitHub」、「建立 git repo」、「設定 git 工作流程」時,Claude 會依照 [SKILL.md](./SKILL.md) 裡的步驟給出可直接照抄執行的指令,而不是憑記憶隨口回答。內容涵蓋:

1. **本機初始化** — `git init`
2. **建立基本檔案** — `.gitignore`、`README.md`
3. **第一次提交** — `git add` / `git commit`,並提醒先用 `git status` 檢查,避免密鑰或敏感檔案被提交
4. **在 GitHub 建立遠端 repo** — 建議先在網頁手動建立空 repo 再 push,避免 `gh repo create --push` 因 token 權限不足而失敗(`gh auth login` 走瀏覽器授權時才建議一步到位)
5. **連結遠端並推送** — `git remote add` / `git push`,附常見錯誤(`Repository not found`、`Write access not granted`、`fetch first` 等)的排解方式
6. **日常開發流程** — 單人直接 `add/commit/push`,多人協作改用分支 + Pull Request
7. **跨帳號搬移** — 需要把 repo 複製到別的帳號/組織時,指向搭配使用的 `github-repo-migrate` skill

## 如何使用

將 `SKILL.md` 放入 Claude Code 的 skills 目錄(例如個人層級的 `~/.claude/skills/git-github-workflow/`),Claude Code 會自動偵測並在符合情境時載入這份流程。

## 使用前提

想套用這個 skill 的人,需要自行具備以下條件,skill 本身不會幫你代勞:

1. **需要 Claude Code**:這是 Claude Code 的 skill,必須有 Claude Code 環境,並把 `SKILL.md` 放進自己的 skills 目錄,Claude 才會在對話中自動載入這份流程。
2. **需要自己的 git / gh 環境**:skill 只是「教 Claude 該下什麼指令、注意什麼」的說明文件,不處理身分驗證。每個人都要自行 `gh auth login` 或設定好自己帳號的 Personal Access Token,才能實際 push 到自己的 GitHub。
3. **關鍵步驟仍會請你確認**:skill 內明確要求 Claude 在推送、建立 repo 這類會影響 GitHub 帳號或他人可見狀態的動作前,先跟使用者確認,不會單憑一句話就自動執行 —— 這是刻意保留的安全機制。
4. **建議先手動建立 repo**:見上方第 4 點,若使用的是 fine-grained personal access token,直接用 `gh repo create --push` 常會因帳號層級權限不足而失敗,先在網頁手動建立空 repo 再 push 可以完全避開這個問題。

## 授權

[MIT License](./LICENSE) — 可自由使用、修改、散布。
