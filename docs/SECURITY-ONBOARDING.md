# 🚀 OpenClaw Hardened Fork - 安全上線指南

## ⚠️ 重要提醒

呢個係 hardened 版本，權限已大幅收緊。唔好因為係「已硬化」就掉以輕心。

---

## 📅 建議上線時間表

### 第 1 天（今天）- 測試階段

❌ **唔好用重要資料**

- 用 測試模式 跑 1-2 小時
- 只用低風險任務（寫 script、聊天、生成 code 但唔執行）

### 第 2-3 天 - 漸進使用

✅ 無問題後開始日常使用

- 用 Telegram 控制日常 coding 任務
- 保持 `require_approval = true`

### 第 5 天之後 - 正式日常使用

- 跑高危命令測試（`rm -rf test_folder`、`curl google.com`）→ 確認被 block/需 approval
- 測試 memory isolation（開 2 個 subagent 做同類任務，確認 memory 唔 overlap）
- 用 Minimax 測試 coding 能力
- Telegram 控制測試（從手機發 10 個唔同指令）

---

## 🔒 權限檢查（Mac 專屬）

```bash
# 檢查已授權嘅 app
tccutil list | grep openclaw
```

**建議只給必要權限：**

- Accessibility → 建議 SELECTIVE（只用必要嘅）
- Screen Recording → 如果唔需要 screen capture 可以唔給
- Full Disk Access → ❌ 唔建議俾

---

## 🐳 Sandbox 確認

```bash
# 確認所有 tool 行 Docker sandbox
docker ps

# 確認 workspace 只限特定資料夾
cat ~/.openclaw/openclaw.json | grep workspaceOnly
```

**原則：**唔好 mount 整個 Home folder，只 mount 具體工作目錄。

---

## 💾 Backup

```bash
# 備份 hardened config
cp ~/.openclaw/openclaw.json ~/openclaw-hardened-config-backup.json

# 備份 config repo
cd ~/openclaw && git bundle create ~/openclaw-hardened.bundle my-hardened-version
```

---

## 📊 監控設定

1. 開啟 audit log（記錄所有 tool call）
2. 設定 daily log review
3. 每週檢查 `secret-scanner.yml` workflow 結果

```bash
# 查看最近嘅 tool calls
cat ~/.openclaw/audit.log 2>/dev/null | tail -50
```

---

## 🚀 如何啟動

```bash
cd ~/openclaw

# 標準啟動（arm64）
arch -arm64 pnpm start

# 確認安全模式
cat ~/.openclaw/openclaw.json | grep -E "(safeBinProfiles|workspaceOnly|agentId)"
```

---

## 📱 日常使用方式

| 方式          | 命令                                 |
| ------------- | ------------------------------------ |
| Telegram 控制 | 直接 send message 俾 bot             |
| CLI 模式      | `openclaw ask "任務描述"`            |
| Mac 控制      | 語音指令（如果已設定 accessibility） |

---

## ✅ 最佳實踐

1. **唔同任務用唔同 agent** — 善用 `agentId` isolation
2. **高風險操作保持 `require_approval = true`**
3. **每週檢查一次 log**
4. **永遠假設佢可能會錯** — hardened 唔等於完美

---

## 🔴 緊急回滾

如果發現問題：

```bash
# 停止所有運行中嘅 agent
pkill -9 -f openclaw

# 回復原始 config
cp ~/.openclaw/openclaw.json.bak ~/.openclaw/openclaw.json

# 或者完全停用
rm -rf ~/.openclaw/openclaw.json
```

---

## 📞 有問題？

Issue: https://github.com/ihateusingai-beep/openclaw/issues
