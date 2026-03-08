# NPM 包命名规范

**版本**: v1.0  
**创建日期**: 2026-03-08  
**维护人**: stonerzju

---

## 📋 版本号格式

### SemVer 规范

OpenClaw Offmode 版本遵循 [Semantic Versioning 2.0.0](https://semver.org/) 规范，使用 **build metadata** 格式：

```
<上游版本>+offmode.<修订号>

示例:
2026.3.7+offmode.1    # 基于上游 2026.3.7，第 1 次修订
2026.3.7+offmode.2    # 基于上游 2026.3.7，第 2 次修订
2026.3.8+offmode.1    # 基于上游 2026.3.8，第 1 次修订
```

### 版本号组成

| 部分         | 格式         | 说明                         | 示例       |
| ------------ | ------------ | ---------------------------- | ---------- |
| **主版本号** | `YYYY.MM.DD` | 上游 OpenClaw 版本           | `2026.3.7` |
| **分隔符**   | `+`          | SemVer build metadata 分隔符 | `+`        |
| **标识符**   | `offmode`    | 固定标识，表示 Offmode 版本  | `offmode`  |
| **修订号**   | `.` + 数字   | 本地修订次数                 | `.1`, `.2` |

---

## 🎯 版本号规则

### 1. 上游版本升级

当上游 OpenClaw 发布新版本时：

```
2026.3.7+offmode.1  →  2026.3.8+offmode.1
                              ↑
                        修订号重置为 1
```

### 2. 本地修订

当 Offmode 版本需要修复或更新时：

```
2026.3.7+offmode.1  →  2026.3.7+offmode.2  →  2026.3.7+offmode.3
                              ↑                    ↑
                        第 2 次修订              第 3 次修订
```

### 3. Tag 命名

Git tag 使用 `v` 前缀：

```bash
git tag -a v2026.3.7+offmode.1 -m "Release v2026.3.7+offmode.1"
```

---

## 📦 NPM 包发布

### package.json 配置

```json
{
  "name": "@stonerzju/openclaw",
  "version": "2026.3.7+offmode.1",
  "description": "OpenClaw Offmode - Multi-channel AI gateway with offline enhancements"
}
```

### 发布命令

```bash
# 发布到 npm
npm publish --access public

# 验证发布
npm view @stonerzju/openclaw version
npm view @stonerzju/openclaw versions
```

### 版本对比

| 包名                            | 版本格式             | 发布作用域 |
| ------------------------------- | -------------------- | ---------- |
| `openclaw` (官方)               | `2026.3.7`           | 官方组织   |
| `@stonerzju/openclaw` (Offmode) | `2026.3.7+offmode.1` | @stonerzju |

---

## 🚀 发布流程

### Step 1: 更新版本号

```bash
# 编辑 package.json
vim package.json

# 更新版本号
{
  "version": "2026.3.7+offmode.1"
}
```

### Step 2: 提交版本更新

```bash
git add package.json
git commit -m "chore: release version 2026.3.7+offmode.1"
```

### Step 3: 创建 Git Tag

```bash
git tag -a v2026.3.7+offmode.1 -m "Release v2026.3.7+offmode.1

Based on upstream OpenClaw v2026.3.7
Offmode revision: 1"
```

### Step 4: 推送到远端

```bash
git push origin offmode_2026.3.7
git push origin v2026.3.7+offmode.1 --tags
```

### Step 5: 发布到 NPM

```bash
npm publish --access public
```

### Step 6: 验证发布

```bash
# 查看最新版本
npm view @stonerzju/openclaw version

# 查看所有版本
npm view @stonerzju/openclaw versions

# 安装测试
npm install -g @stonerzju/openclaw@2026.3.7+offmode.1
openclaw --version
```

---

## 📝 版本历史示例

### 2026.3.7 系列

| 版本                 | 发布日期   | 说明                   |
| -------------------- | ---------- | ---------------------- |
| `2026.3.7+offmode.1` | 2026-03-08 | 首个 Offmode 版本      |
| `2026.3.7+offmode.2` | 2026-03-09 | 修复 WhatsApp 登录问题 |
| `2026.3.7+offmode.3` | 2026-03-10 | 添加 Feishu 知识库集成 |

### 2026.3.8 系列

| 版本                 | 发布日期   | 说明              |
| -------------------- | ---------- | ----------------- |
| `2026.3.8+offmode.1` | 2026-03-15 | 基于上游 2026.3.8 |

---

## ⚠️ 注意事项

### 1. Build Metadata 不参与版本比较

根据 SemVer 规范，`+` 后面的 build metadata **不参与版本优先级比较**：

```
2026.3.7+offmode.1  ==  2026.3.7+offmode.2  (版本优先级相同)
```

但 npm 会将其视为不同版本，可以正常发布和安装。

### 2. 不要使用 `-` 预发布标识

❌ 错误格式：

```
2026.3.7-offmode.1   # 这是预发布版本，会被视为低于 2026.3.7
```

✅ 正确格式：

```
2026.3.7+offmode.1   # 这是 build metadata，版本等于 2026.3.7
```

### 3. 作用域前缀

Offmode 版本发布到个人作用域下：

```
@stonerzju/openclaw  # ✅ 正确
openclaw             # ❌ 错误 (属于官方组织)
```

---

## 🔗 相关文档

- [Semantic Versioning 2.0.0](https://semver.org/)
- [NPM SemVer Calculator](https://semver.npmjs.com/)
- [NPM Publish Documentation](https://docs.npmjs.com/cli/v10/commands/npm-publish)
- [OFFMODE-RELEASE-PROCESS.md](./OFFMODE-RELEASE-PROCESS.md)

---

**最后更新**: 2026-03-08  
**维护人**: stonerzju
