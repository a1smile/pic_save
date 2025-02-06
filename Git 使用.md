# Git 使用

## 一、Git 初始化配置

配置用户信息（**必须步骤**，否则无法提交代码）：

```
# 全局配置（适用于所有仓库）
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"

# 局部配置（仅针对当前仓库）
git config user.name "项目专用名字"
git config user.email "项目专用邮箱"
```

常用附加配置：

```
# 设置默认编辑器为 VS Code
git config --global core.editor "code --wait"

# 查看所有配置项
git config --list

# 创建命令别名（例如用 `git co` 代替 `git checkout`）
git config --global alias.co checkout
```

------

## 二、Git 克隆 / 拉取 / 提交 / 推送

1. **克隆远程仓库**

   ```
   git clone https://github.com/用户名/仓库名.git
   # 克隆到指定目录
   git clone https://github.com/用户名/仓库名.git 目录名
   ```
   
2. **拉取远程更新**

   ```
   # 拉取并自动合并（常用）
   git pull origin 分支名
   
   # 先获取更新，再手动合并/变基
   git fetch origin        # 下载远程更新
   git merge origin/main   # 合并到当前分支
   # 或
   git rebase origin/main  # 变基操作（重写提交历史）
   ```
   
3. **提交代码到本地仓库**

   ```
   git add .                # 添加所有修改
   git add 文件名           # 添加特定文件
   git commit -m "提交信息"  # 提交到本地仓库
   
   # 修改上一次提交（未推送时）
   git add 漏掉的文件
   git commit --amend       # 进入编辑器修改提交信息
   ```
   
4. **推送代码到远程仓库**

   ```
   git push origin 分支名
   
   # 首次推送需关联远程分支
   git push -u origin 分支名
   ```

------

## 三、Git 提交规范（参考 Angular 规范）

**提交格式**：
`<类型>(<作用域>): <主题>`
`<空行>`
`<正文>`
`<空行>`
`<脚注>`

**示例**：

```
feat(用户管理): 新增用户注册功能

- 添加手机号验证接口
- 实现注册表单提交逻辑

Closes #123
```

**常用类型**：

| 类型       | 说明                                     |
| :--------- | :--------------------------------------- |
| `feat`     | 新功能                                   |
| `fix`      | 修复 bug                                 |
| `docs`     | 文档更新                                 |
| `style`    | 代码格式调整（空格、分号等，不影响功能） |
| `refactor` | 重构代码（非功能修改）                   |
| `test`     | 添加或修改测试用例                       |
| `chore`    | 构建流程或工具变动（如依赖更新）         |
| `perf`     | 性能优化                                 |

**规范要点**：

1. 主题行不超过 50 字，正文每行不超过 72 字符
2. 使用祈使语气（如 "add" 而非 "added" 或 "adds"）
3. 关联 Issue 或任务编号（例如 `Closes #123`）

------

## 四、其他实用命令

| 操作           | 命令                              |
| :------------- | :-------------------------------- |
| 查看状态       | `git status`                      |
| 查看提交历史   | `git log --oneline --graph`       |
| 撤销工作区修改 | `git restore 文件名`              |
| 创建新分支     | `git branch 分支名`               |
| 切换分支       | `git checkout 分支名`             |
| 合并分支       | `git merge 分支名`                |
| 删除远程分支   | `git push origin --delete 分支名` |

------

## 五、注意事项

1. **推送前先拉取**：避免远程有更新导致冲突
2. **频繁提交**：小步提交便于回滚和代码审查
3. **合理使用 `.gitignore`**：忽略编译文件、日志、敏感信息等
4. **谨慎修改历史**：已推送的提交避免使用 `git commit --amend` 或 `git rebase`