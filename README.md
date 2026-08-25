# git-github-workflow

一個 [Claude Code](https://claude.com/claude-code) skill,提供「本機專案 → 建立 Git 版控 → 上傳/推送到 GitHub」的完整標準流程與檢查清單。內容以繁體中文撰寫,不綁定特定程式語言或框架,適用任何專案。

## 這個 skill 做什麼

當你請 Claude Code 幫忙「把專案上傳到 GitHub」、「建立 git repo」、「設定 git 工作流程」時,Claude 會依照 [SKILL.md](./SKILL.md) 裡的步驟給出可直接照抄執行的指令,而不是憑記憶隨口回答。內容涵蓋:

1. **本機初始化** — `git init`
2. **建立基本檔案** — `.gitignore`、`README.md`
3. **第一次提交** — `git add` / `git commit`,並提醒先用 `git status` 檢查,避免密鑰或敏感檔案被提交
4. **在 GitHub 建立遠端 repo** — 網頁操作或 `gh repo create` 一步到位
5. **連結遠端並推送** — `git remote add` / `git push`,附常見錯誤(`Repository not found`、`Write access not granted`、`fetch first` 等)的排解方式
6. **日常開發流程** — 單人直接 `add/commit/push`,多人協作改用分支 + Pull Request
7. **跨帳號搬移** — 需要把 repo 複製到別的帳號/組織時,指向搭配使用的 `github-repo-migrate` skill

## 如何使用

將 `SKILL.md` 放入 Claude Code 的 skills 目錄(例如個人層級的 `~/.claude/skills/git-github-workflow/`),Claude Code 會自動偵測並在符合情境時載入這份流程。

## 授權

[MIT License](./LICENSE) — 可自由使用、修改、散布。
