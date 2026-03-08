# 🦞 OpenClaw Offmode 版本发布流程

**版本**: v1.0  
**创建日期**: 2026-03-08  
**维护人**: stonerzju

---

## 📋 目录

1. [分支策略](#分支策略)
2. [版本号规范](#版本号规范)
3. [发布流程](#发布流程)
4. [日常同步](#日常同步)
5. [冲突解决](#冲突解决)
6. [常见问题](#常见问题)

---

## 分支策略

### 分支结构

```
main                    # 跟踪上游 openclaw/openclaw (只读，不直接修改)
├── offmode_2026.3.4   # 已发布的旧版本 (永久保留)
├── offmode_2026.3.5   # 当前开发版本
└── offmode_2026.3.6   # 下一个版本 (开发中)
```

### 分支命名规则

| 分支类型     | 命名格式         | 示例               |
| ------------ | ---------------- | ------------------ |
| **主分支**   | `main`           | `main`             |
| **功能分支** | `offmode_版本号` | `offmode_2026.3.5` |
| **发布 Tag** | `v 完整版本号`   | `v2026.3.5.1`      |

### 分支用途

| 分支        | 用途         | 修改策略        |
| ----------- | ------------ | --------------- |
| `main`      | 跟踪上游代码 | ❌ 非必要不修改 |
| `offmode_*` | 离线功能开发 | ✅ 可以修改     |

### ⚠️ 版本保留策略

**重要原则**: 所有历史版本必须永久保留，不得删除！

| 资源类型    | 保留策略 | 说明                                              |
| ----------- | -------- | ------------------------------------------------- |
| **分支**    | 永久保留 | `offmode_2026.3.4`, `offmode_2026.3.5` 等永不删除 |
| **Tag**     | 永久保留 | `v2026.3.5.1`, `v2026.3.5.2` 等永不删除           |
| **Release** | 永久保留 | GitHub Release 永不删除                           |

**原因**:

1. 用户可能需要回退到旧版本
2. 问题排查时需要对比不同版本
3. 某些环境可能固定使用特定版本

**检查清单**:

- [ ] 发布新版本前，确认旧版本分支已推送到远端
- [ ] 确认旧版本 Tag 已推送到远端
- [ ] 确认旧版本 Release Notes 已创建
- [ ] 永不执行 `git branch -D offmode_*`
- [ ] 永不执行 `git tag -d v*`

---

## 版本号规范

### 版本号格式

```
<上游版本>.<本地修订号>

示例:
2026.3.5.1  # 基于上游 2026.3.5，本地第 1 次修订
2026.3.5.2  # 基于上游 2026.3.5，本地第 2 次修订
2026.3.6.1  # 基于上游 2026.3.6，本地第 1 次修订
```

### 版本号更新规则

1. **上游版本升级** → 更新主版本号（第 1-3 段）
   - `2026.3.5` → `2026.3.6`
2. **本地修订** → 更新修订号（第 4 段）
   - `.1` → `.2` → `.3`

3. **每次发布从 .1 开始**
   - 新版本基于新的上游版本时，修订号重置为 1

4. **版本号递增示例**
   ```
   2026.3.5.1  →  2026.3.5.2  →  2026.3.5.3
   ↓ (上游升级)
   2026.3.6.1  →  2026.3.6.2  →  2026.3.6.3
   ```

### 版本号文件

**位置**: `package.json`

```json
{
  "name": "openclaw",
  "version": "2026.3.5.1",
  "offmodeBase": "2026.3.5",
  "offmodeRevision": 1
}
```

### Tag 命名

```
v<完整版本号>

示例:
v2026.3.5.1  # 正式发布版本
v2026.3.5.2  # 第二次修订
```

---

## 发布流程

### 前置条件

- [ ] 确认上游已发布新版本 (检查 https://github.com/openclaw/openclaw/releases)
- [ ] 确认 `main` 分支已同步到最新
- [ ] 确认上一个 offmode 版本已稳定发布

### Step 1: 同步 main 分支

```bash
cd /path/to/openclaw

# 1. 切换到 main 分支
git checkout main

# 2. 拉取上游最新代码
git pull origin main

# 3. 查看最新 tag (确认上游版本号)
git tag -l "v*" --sort=-version:refname | head -5
# 输出示例: v2026.3.5
```

### Step 2: 创建新版本分支

```bash
# 基于旧的 offmode 分支创建新分支
# 假设旧版本是 offmode_2026.3.4，新版本是 2026.3.5

git checkout offmode_2026.3.4
git checkout -b offmode_2026.3.5
```

### Step 3: Rebase 到 main

```bash
# Rebase 到 main 分支的指定 tag
git rebase v2026.3.5

# 或者 rebase 到 main 分支的最新提交
git rebase main
```

### Step 4: 解决冲突

```bash
# 如果有冲突，git 会提示
# CONFLICT (content): Merge conflict in package.json

# 1. 编辑冲突文件
vim package.json

# 2. 标记为解决
git add package.json

# 3. 继续 rebase
git rebase --continue

# 4. 重复直到完成
```

### Step 5: 更新版本号

```bash
# 1. 编辑 package.json
vim package.json

# 2. 更新版本号 (新格式)
{
  "name": "openclaw",
  "version": "2026.3.5.1",
  "offmodeBase": "2026.3.5",
  "offmodeRevision": 1
}

# 3. 提交版本号更新
git add package.json
git commit -m "chore: release version 2026.3.5.1"
```

### Step 6: 打 Tag

```bash
# 1. 创建 tag (新格式)
git tag -a v2026.3.5.1 -m "Release v2026.3.5.1

Based on upstream v2026.3.5
Offline mode enhancements:
- [列出主要修改]
"

# 2. 查看 tag
git tag -l "v*" --sort=-version:refname | head -5

# 输出示例:
# v2026.3.6.1
# v2026.3.5.2
# v2026.3.5.1  ← 新创建的 tag
# v2026.3.4.3
# v2026.3.4.2
```

### Step 7: 推送到远端

```bash
# 1. 推送分支
git push origin offmode_2026.3.5

# 2. 推送 tag
git push origin v2026.3.5.1 --tags

# 3. 验证推送
gh release view v2026.3.5.1
```

### Step 8: 发布说明

**创建 Release Notes** (GitHub):

````markdown
## 🦞 OpenClaw Offmode v2026.3.5.1

### 基于版本

- Upstream: OpenClaw v2026.3.5
- 上游仓库：https://github.com/openclaw/openclaw/releases/tag/v2026.3.5

### 离线功能增强

- [ ] 功能 1
- [ ] 功能 2

### 修复的问题

- [ ] Bug 1
- [ ] Bug 2

### 已知问题

- [ ] 问题 1

### 安装方式

```bash
git clone -b offmode_2026.3.5 https://github.com/stonerzju/openclaw.git
cd openclaw
npm install
```
````

### 版本历史

- v2026.3.5.1 (当前)
- v2026.3.4.3
- v2026.3.4.2
- v2026.3.4.1

````

---

## 日常同步

### 每周同步 main 分支

```bash
# 1. 切换到 main
git checkout main

# 2. 拉取上游
git pull origin main

# 3. 查看变更
git log --oneline -10
````

### 同步开发分支

```bash
# 如果在 offmode 分支开发中
git checkout offmode_2026.3.5

# 定期 rebase main 保持同步
git rebase main

# 解决冲突后继续
git rebase --continue
```

---

## 冲突解决

### 常见冲突文件

| 文件             | 冲突原因        | 解决策略                         |
| ---------------- | --------------- | -------------------------------- |
| `package.json`   | 版本号/依赖更新 | 保留上游版本 + 添加 offmode 字段 |
| `pnpm-lock.yaml` | 依赖锁文件      | 使用上游的，重新 `pnpm install`  |
| `docs/*.md`      | 文档更新        | 合并双方修改                     |
| `src/version.ts` | 版本号代码      | 更新为上游版本 + offmode 标记    |

### 冲突解决工具

```bash
# 使用 meld 可视化解决
git mergetool

# 使用 vscode
code package.json

# 手动编辑
vim package.json
```

### Rebase 冲突处理

```bash
# 1. 开始 rebase
git rebase main

# 2. 遇到冲突
# CONFLICT (content): Merge conflict in package.json

# 3. 解决冲突
vim package.json
git add package.json

# 4. 继续
git rebase --continue

# 5. 如果问题太多，可以中止
git rebase --abort
```

---

## 常见问题

### Q1: Rebase 和 Merge 选哪个？

**A**:

- **个人维护** → Rebase (历史清晰)
- **多人协作** → Merge (保留历史)

### Q2: 如何处理大的冲突？

**A**:

1. 先备份当前分支
2. 分批次 rebase，不要一次 rebase 太多提交
3. 使用 `git rebase -i` 交互式 rebase

### Q3: 版本号忘记更新怎么办？

**A**:

1. 立即创建新的 tag `v2026.3.5.2`
2. 在 Release Notes 中说明原因
3. 考虑添加 CI 检查版本号

### Q4: 如何回滚发布？

**⚠️ 重要**: 根据版本保留策略，**不允许删除已发布的版本**！

**正确做法**:

```bash
# 如果发现问题，创建新的修订版本修复
git checkout offmode_2026.3.5
# 修复问题
git commit -m "fix: 修复 xxx 问题"
git tag -a v2026.3.5.2 -m "Release v2026.3.5.2 (fix for .1)"
git push origin v2026.3.5.2 --tags
```

### Q5: 如何查看版本历史？

**A**:

```bash
# 查看所有 offmode tag
git tag -l "v*" --sort=-version:refname

# 查看某个 tag 的变更
git show v2026.3.5.1

# 查看分支历史
git log offmode_2026.3.5 --oneline -20

# 查看版本树
git log --graph --oneline --all | head -30
```

---

## 自动化脚本

### 创建新版本脚本

创建 `scripts/create-offmode-release.sh`:

```bash
#!/bin/bash
# create-offmode-release.sh
# 用法：./create-offmode-release.sh 2026.3.5 1

set -e

UPSTREAM_VERSION=$1
OFF_NUMBER=$2
BRANCH_NAME="offmode_${UPSTREAM_VERSION}"
TAG_NAME="v${UPSTREAM_VERSION}.off${OFF_NUMBER}"

echo "🦞 创建 Offmode 版本: $TAG_NAME"

# 1. 切换到旧版本分支
git checkout "offmode_${PREVIOUS_VERSION}"

# 2. 创建新分支
git checkout -b "$BRANCH_NAME"

# 3. Rebase 到上游
git rebase "v$UPSTREAM_VERSION"

# 4. 更新版本号
# (需要手动编辑 package.json)
echo "✏️  请手动更新 package.json 中的版本号"
vim package.json

# 5. 提交
git add package.json
git commit -m "chore: update offmode version to $TAG_NAME"

# 6. 打 tag
git tag -a "$TAG_NAME" -m "Release $TAG_NAME"

# 7. 推送
git push origin "$BRANCH_NAME"
git push origin "$TAG_NAME" --tags

echo "✅ 发布完成：$TAG_NAME"
```

### 版本号检查脚本

创建 `scripts/check-version.sh`:

```bash
#!/bin/bash
# check-version.sh
# 检查版本号是否正确

PACKAGE_VERSION=$(grep '"version"' package.json | cut -d'"' -f4)
OFFMODE_VERSION=$(grep '"offmodeVersion"' package.json | cut -d'"' -f4)

echo "📦 Package Version: $PACKAGE_VERSION"
echo "🦞 Offmode Version: $OFFMODE_VERSION"

# 检查是否匹配
if [[ ! "$OFFMODE_VERSION" =~ "v$PACKAGE_VERSION" ]]; then
  echo "❌ 版本号不匹配!"
  exit 1
fi

echo "✅ 版本号检查通过"
```

---

## 检查清单

### 发布前检查

- [ ] main 分支已同步到上游最新
- [ ] 上一个 offmode 版本已稳定
- [ ] 所有测试通过
- [ ] 版本号已更新
- [ ] CHANGELOG 已更新
- [ ] 文档已更新

### 发布后检查

- [ ] 分支已推送到远端
- [ ] Tag 已推送到远端
- [ ] Release Notes 已创建
- [ ] 通知用户新版本发布

---

## 附录

### 相关仓库

- **上游**: https://github.com/openclaw/openclaw
- **Offmode**: https://github.com/stonerzju/openclaw

### 参考资料

- [Git Rebase 文档](https://git-scm.com/docs/git-rebase)
- [Git Tag 文档](https://git-scm.com/docs/git-tag)
- [语义化版本规范](https://semver.org/lang/zh-CN/)

---

**最后更新**: 2026-03-08  
**维护人**: stonerzju
