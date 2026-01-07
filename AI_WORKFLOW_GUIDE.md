# AI 部署工作流程指南

> 本文档旨在帮助 AI 助手快速、高效地将项目部署到 GitHub 和 Vercel
>
> **目标**: 5分钟内完成从本地到线上的完整部署流程

## 📋 前置检查清单

在开始之前，快速确认以下信息：

```bash
# 1. 检查当前目录和项目结构
pwd && ls -la

# 2. 检查 Git 状态（是否在子仓库中）
git status 2>&1 | head -5

# 3. 检查 GitHub CLI 状态
gh auth status

# 4. 检查网络连接
curl -Is https://github.com | head -1
```

## 🚀 快速部署流程（5步法）

### 步骤 1: 准备文件结构（1分钟）

```bash
# 如果主文件不是 index.html，重命名
mv [原文件名].html index.html

# 创建 README.md（简洁版即可）
cat > README.md << 'EOF'
# [项目名称]

[一句话项目描述]

## 在线演示

[部署后填写]

## 本地运行

直接在浏览器中打开 `index.html` 即可。

## 技术栈

- HTML5 / CSS3 / JavaScript

## 许可证

MIT
EOF
```

**AI 注意事项**:
- ✅ 始终将主HTML文件重命名为 `index.html`
- ✅ README保持简洁，核心信息即可
- ❌ 不要花时间创建复杂的文档结构

### 步骤 2: 初始化 Git（30秒）

```bash
# 如果在父仓库的子目录中，需要创建独立仓库
rm -rf .git && git init

# 提交所有文件
git add .
git commit -m "feat: 初始提交"
```

**AI 注意事项**:
- ⚠️ 检查是否在父仓库的子目录中，如果是则需要独立初始化
- ✅ 使用简洁的 commit message

### 步骤 3: 创建 GitHub 仓库（1分钟）

```bash
# 一键创建并推送（推荐）
gh repo create [项目名] \
  --public \
  --source=. \
  --remote=origin \
  --push \
  --description="[简短描述]"
```

**如果遇到网络问题**，分步执行：

```bash
# 方案A: 仅创建仓库，稍后推送
gh repo create [项目名] --public --description="[描述]"
git remote add origin https://github.com/[用户名]/[项目名].git

# 方案B: 使用后台推送，不等待完成
git push -u origin main &
# 继续执行后续步骤
```

**AI 注意事项**:
- ✅ 仓库名使用 kebab-case (例如: `my-project`)
- ✅ 如果 `gh repo create` 推送超时，不要重试多次
- ✅ 创建仓库成功就继续，推送可以异步进行或让用户手动完成
- ⏱️ 设置合理的超时时间，避免长时间等待

### 步骤 4: 提供 Vercel 部署指令（1分钟）

**不要花时间创建长篇部署文档**，直接提供核心步骤：

```markdown
## Vercel 一键部署

1. 访问 https://vercel.com/new
2. 导入仓库: [用户名]/[项目名]
3. 点击 Deploy

或使用一键部署按钮:
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/[用户名]/[项目名])
```

**AI 注意事项**:
- ✅ 对于纯静态HTML项目，Vercel零配置即可部署
- ✅ 提供一键部署按钮链接
- ❌ 不需要创建 `vercel.json` 配置文件
- ❌ 不需要详细说明每个配置选项

### 步骤 5: 总结并交付（30秒）

向用户提供：

```markdown
## ✅ 完成情况

- [x] 项目文件已准备
- [x] Git 仓库已初始化
- [x] GitHub 仓库已创建: https://github.com/[用户名]/[项目名]
- [ ] 代码推送: [如有网络问题，提供手动命令]
- [ ] Vercel 部署: [提供链接]

## 🎯 下一步

1. 如需推送代码: `cd [路径] && git push -u origin main`
2. 部署到 Vercel: https://vercel.com/new?repository=[用户名]/[项目名]

预计3分钟内完成部署！
```

## ⚡ 性能优化技巧

### 1. 并行执行

```bash
# 同时执行多个不相关的操作
{
  echo "Creating README..." && cat > README.md << 'EOF'
  [内容]
  EOF
} &

{
  echo "Checking network..." && curl -Is https://github.com
} &

wait
```

### 2. 避免重复工作

**已有 Git 仓库时**:
```bash
# 不要重新初始化，直接使用
git remote set-url origin https://github.com/[新地址]
git push -u origin main
```

**已有 README 时**:
```bash
# 只更新必要部分，不要重写整个文件
```

### 3. 超时控制

```bash
# 为耗时操作设置超时
timeout 30s git push origin main || echo "推送超时，请手动执行"
```

## ⚠️ 常见陷阱与解决方案

### 陷阱 1: 在父仓库的子目录中工作

**识别**:
```bash
git remote -v
# 如果显示的是父项目的远程仓库，说明你在子目录中
```

**解决**:
```bash
rm -rf .git && git init
```

### 陷阱 2: 网络超时

**识别**:
- `git push` 超过 30 秒无响应
- 报错: "Failed to connect to github.com"

**解决**:
- ✅ 不要反复重试
- ✅ 告知用户手动推送命令
- ✅ 继续后续步骤（仓库创建成功即可）

### 陷阱 3: 文件命名问题

**识别**:
- 主文件不是 `index.html`
- Vercel 部署后显示 404

**解决**:
```bash
# 始终确保主文件名为 index.html
mv [任何名字].html index.html
```

### 陷阱 4: 过度文档化

**问题**:
- 创建多个长篇 MD 文档
- 详细解释每个配置选项
- 提供多种部署方案对比

**解决**:
- ✅ README 保持 50-100 行
- ✅ 只提供一种最简单的部署方式
- ✅ 用户需要时再补充文档

## 📊 时间分配参考

| 步骤 | 预期时间 | 最大时间 |
|------|----------|----------|
| 文件准备 | 30秒 | 1分钟 |
| Git 初始化 | 20秒 | 30秒 |
| 创建 GitHub 仓库 | 30秒 | 1分钟 |
| 推送代码 | 10秒 | 30秒* |
| 提供部署指令 | 30秒 | 1分钟 |
| **总计** | **2分钟** | **4分钟** |

*如果网络慢，跳过等待，让用户手动完成

## 🎯 成功标准

满足以下条件即视为成功：

- ✅ GitHub 仓库已创建并可访问
- ✅ 用户获得了清晰的 Vercel 部署指令
- ✅ 所有必要文件已准备就绪（index.html, README.md）
- ✅ 总耗时 < 5 分钟

**不需要**:
- ❌ 代码必须已推送到 GitHub（可以异步或手动完成）
- ❌ 已经部署到 Vercel（用户自己点击即可）
- ❌ 完整的文档体系（够用即可）

## 💡 与用户沟通技巧

### 开始时

```markdown
我将帮你快速部署到 GitHub 和 Vercel，预计 3-5 分钟完成。

我会：
1. 准备文件结构
2. 创建 GitHub 仓库
3. 提供 Vercel 一键部署链接

开始执行...
```

### 结束时

```markdown
## ✅ 部署准备完成！

**GitHub 仓库**: https://github.com/xxx/xxx

**下一步操作**:
1. [如需要] 推送代码: `git push -u origin main`
2. 部署到 Vercel: [一键部署链接]

3分钟内即可看到你的网站上线！🎉
```

## 🔄 版本历史

- v1.0 (2026-01-07): 初始版本，基于 binary-converter-game 项目的部署经验

## 📝 反馈与改进

如果这个流程有问题或可以改进的地方，请更新此文档。

---

**记住**: 快速 > 完美。用户需要的是快速看到结果，而不是完美的文档。
