# Supabase Keep-Alive 保活脚本

这是一个用于保持 Supabase 项目活跃的自动化脚本，通过 GitHub Actions 定时执行，防止免费版 Supabase 项目因长期不活跃而被暂停。

## 📋 功能特点

- ✅ 自动定时执行（每 5 天一次）
- ✅ 多种保活方式（Storage API、Database Query、Auth API）
- ✅ 详细的执行日志
- ✅ 支持手动触发
- ✅ 使用 Bun 运行时，执行速度快

## 🚀 快速开始

### 1. Fork 或克隆此仓库

将此项目 fork 到您的 GitHub 账号下，或直接克隆到本地：

```bash
git clone https://github.com/your-username/supabase-keep-alive.git
cd supabase-keep-alive
```

### 2. 获取 Supabase 配置信息

登录您的 [Supabase Dashboard](https://supabase.com/dashboard)，找到您需要保活的项目：

1. 进入项目的 **Settings** (设置)
2. 点击 **API** 选项
3. 复制以下两个值：
   - **Project URL** (`NEXT_PUBLIC_SUPABASE_URL`)
   - **anon public** key (`NEXT_PUBLIC_SUPABASE_ANON_KEY`)

### 3. 配置 GitHub Secrets

在您的 GitHub 仓库中配置密钥：

#### 步骤详解：

1. **打开仓库设置**
   - 进入您 fork 的仓库页面
   - 点击顶部的 **Settings** (设置)

2. **进入 Secrets 配置页面**
   - 在左侧菜单中找到 **Secrets and variables**
   - 点击 **Actions**

3. **添加第一个密钥 - Supabase URL**
   - 点击 **New repository secret** (新建仓库密钥)
   - Name (名称): `NEXT_PUBLIC_SUPABASE_URL`
   - Secret (值): 粘贴您的 Supabase Project URL
   - 点击 **Add secret** (添加密钥)

4. **添加第二个密钥 - Supabase Key**
   - 再次点击 **New repository secret**
   - Name (名称): `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - Secret (值): 粘贴您的 Supabase anon public key
   - 点击 **Add secret** (添加密钥)

配置完成后，您应该能看到两个密钥：
- ✅ `NEXT_PUBLIC_SUPABASE_URL`
- ✅ `NEXT_PUBLIC_SUPABASE_ANON_KEY`

### 4. 启用 GitHub Actions

1. 进入仓库的 **Actions** 标签页
2. 如果看到提示，点击 **I understand my workflows, go ahead and enable them** (我了解我的工作流，继续并启用它们)
3. 在左侧找到 **Keep Supabase Alive** 工作流
4. 点击 **Enable workflow** (启用工作流)

### 5. 测试运行

手动触发工作流以测试配置是否正确：

1. 在 **Actions** 标签页中，点击左侧的 **Keep Supabase Alive**
2. 点击右上角的 **Run workflow** (运行工作流)
3. 选择分支（通常是 `main` 或 `master`）
4. 点击绿色的 **Run workflow** 按钮

等待几秒钟，工作流会开始执行。点击工作流运行记录可以查看详细日志。

## ⏰ 定时任务说明

### 当前配置

工作流配置为 **每 5 天自动运行一次**，时间为 UTC 0:00（北京时间上午 8:00）。

### Cron 表达式说明

在 `.github/workflows/keep-alive.yml` 中的 cron 配置：

```yaml
schedule:
  - cron: '0 0 */5 * *'
```

**格式解析：**
```
┌───────────── 分钟 (0 - 59)
│ ┌───────────── 小时 (0 - 23)
│ │ ┌───────────── 日期 (1 - 31)
│ │ │ ┌───────────── 月份 (1 - 12)
│ │ │ │ ┌───────────── 星期 (0 - 6) (周日到周六)
│ │ │ │ │
0 0 */5 * *
```

- `0 0` = 每天的 00:00 (UTC)
- `*/5` = 每 5 天
- `* *` = 任意月份和星期

### 修改执行频率

如果需要修改执行频率，编辑 `.github/workflows/keep-alive.yml` 文件中的 cron 表达式：

**常用配置示例：**

```yaml
# 每 3 天一次
- cron: '0 0 */3 * *'

# 每 7 天（每周）一次
- cron: '0 0 */7 * *'

# 每 10 天一次
- cron: '0 0 */10 * *'

# 每天一次
- cron: '0 0 * * *'

# 每天两次（上午 8 点和晚上 8 点 UTC）
- cron: '0 8,20 * * *'
```

**注意：** GitHub Actions 的 cron 使用 UTC 时间，需要转换到您的本地时区。

## 📦 本地测试

如果您想在本地测试脚本：

### 安装 Bun

```bash
# Windows (PowerShell)
powershell -c "irm bun.sh/install.ps1 | iex"

# macOS / Linux
curl -fsSL https://bun.sh/install | bash
```

### 安装依赖

```bash
bun install
```

### 设置环境变量并运行

```bash
# Windows PowerShell
$env:NEXT_PUBLIC_SUPABASE_URL="your-supabase-url"
$env:NEXT_PUBLIC_SUPABASE_ANON_KEY="your-anon-key"
bun run keep-alive

# macOS / Linux
export NEXT_PUBLIC_SUPABASE_URL="your-supabase-url"
export NEXT_PUBLIC_SUPABASE_ANON_KEY="your-anon-key"
bun run keep-alive
```

## 🔍 工作原理

脚本通过三种方式与 Supabase API 交互，保持项目活跃：

1. **Storage API 检查** - 列出所有存储桶
2. **Database Query** - 执行一个简单的查询操作
3. **Auth API 检查** - 检查认证服务状态

只要有一个操作成功，就认为保活成功。

## 📊 查看运行日志

1. 进入仓库的 **Actions** 标签页
2. 点击任意一次工作流运行记录
3. 点击 **keep-alive** 任务
4. 展开各个步骤查看详细日志

## ❓ 常见问题

### Q: 为什么需要保活？

A: Supabase 免费版项目如果长期不活跃（通常超过 1 周），可能会被暂停。定期执行保活脚本可以防止这种情况。

### Q: 保活脚本会消耗我的 Supabase 配额吗？

A: 会有极少量的 API 调用，但消耗可以忽略不计。脚本每次只执行 3 个简单的 API 调用。

### Q: 可以修改执行频率吗？

A: 可以！编辑 `.github/workflows/keep-alive.yml` 文件中的 cron 表达式即可。建议不要低于 3 天一次。

### Q: GitHub Actions 的 cron 不准时怎么办？

A: GitHub Actions 的 cron 任务在高峰期可能会有延迟（最多几小时）。这是正常现象，不影响保活效果。如果需要更精确的时间控制，可以考虑使用其他定时任务服务。

### Q: 密钥安全吗？

A: GitHub Secrets 是加密存储的，只有在工作流运行时才会解密使用，不会在日志中显示。请不要在代码中硬编码密钥。

### Q: 可以同时保活多个 Supabase 项目吗？

A: 可以！您可以：
1. 为每个项目创建一个单独的仓库
2. 或者修改脚本支持多个项目配置（需要添加额外的 Secrets）

## 📝 License

MIT License

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

---

**注意：** 请确保妥善保管您的 Supabase 密钥，不要泄露给他人或提交到公开仓库中。
