# 🖥️ 本地安裝指南（Mac M-series）

## 前置要求

- macOS (Apple Silicon or Intel)
- Docker Desktop ✅ 已安裝
- Node.js 18+（建議 v22）
- pnpm

## 第一步：Clone Repo

```bash
cd ~
git clone https://github.com/ihateusingai-beep/openclaw.git
cd openclaw
git checkout my-hardened-version
```

## 第二步：Install Dependencies

```bash
# 安裝 pnpm（如果未安裝）
npm install -g pnpm

# 安裝專案依賴
pnpm install
```

## 第三步：Config 準備

你的 hardened config 已經喺 `~/.openclaw/openclaw.json`

```bash
# 確認 config 存在
ls -la ~/.openclaw/openclaw.json

# 備份一份
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.bak
```

## 第四步：驗證 Hardened Config

```bash
# 確認所有硬化設置
cat ~/.openclaw/openclaw.json | jq '.tools.sandbox'
cat ~/.openclaw/openclaw.json | jq '.tools.fs'
cat ~/.openclaw/openclaw.json | jq '.agents.memory.agentId'
```

預期輸出：

- `workspaceOnly: true`
- `mode: "strict"`
- `agentId: "..."`（唔同 agent 有唔同 ID）

## 第五步：啟動 OpenClaw

### 方式 A：直接啟動（推薦新手）

```bash
cd ~/openclaw
arch -arm64 pnpm start
```

### 方式 B：安全模式（有更多 logging）

```bash
cd ~/openclaw
OPENCLAW_SECURE=1 arch -arm64 pnpm start
```

### 方式 C：Docker 內運行

```bash
cd ~/openclaw
docker build -t openclaw-hardened .
docker run --rm -it \
  -v ~/.openclaw/openclaw.json:/app/config/openclaw.json \
  -v ~/workspace:/workspace \
  openclaw-hardened
```

## 第六步：驗證運作

開瀏覽器訪問：

```
http://localhost:8080
```

檢查：

- ✅ Gateway 正常
- ✅ Telegram bot 回應
- ✅ API key 未泄漏

## 第七步：Telegram 設定（如果係第一次）

```bash
# 創建 Telegram bot（如果你未有一個）
# 搵 @BotFather 拎 token

# 設定 token
openclaw config set channels.telegram.botToken "YOUR_TOKEN"
```

## 常見問題

### Q: `arch: arm64: command not found`

```bash
# 用 uname -m 確認架構
uname -m
# 輸出應該係 arm64（Apple Silicon）or x86_64（Intel）
```

### Q: Docker 唔認得 `docker` command

```bash
# 確認 Docker Desktop 係開住
open -a Docker

# 測試
docker ps
```

### Q: `pnpm: command not found`

```bash
npm install -g pnpm
```

### Q: Permission denied

```bash
# 俾 workspace 資料夾適當權限
chmod 755 ~/workspace
```

## 安全 Checklist

| 項目                     | 確認                                                     |
| ------------------------ | -------------------------------------------------------- |
| ✅ Docker Desktop 運行中 | `docker ps`                                              |
| ✅ config 存在           | `cat ~/.openclaw/openclaw.json`                          |
| ✅ safeBinProfiles 設定  | `jq '.tools.exec.safeBins' ~/.openclaw/openclaw.json`    |
| ✅ workspaceOnly         | `jq '.tools.fs.workspaceOnly' ~/.openclaw/openclaw.json` |
| ✅ agentId tagging       | `jq '.agents.memory.agentId' ~/.openclaw/openclaw.json`  |

## Uninstall

```bash
# 停止
pkill -f openclaw

# 移除
rm -rf ~/openclaw

# 保留 config（如果你想留）
# rm ~/.openclaw/openclaw.json
```
