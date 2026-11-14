# git 日常使用（只需这 5 条命令）

> 用途：提交题解代码 / 更新文档时用到的最少命令集。单人学习项目不需要分支、不需要 PR。
> 关联：06-题解代码\README-代码提交规范.md（什么时候该提交）
> 更新：2026-11-14

---

## 仓库信息

| 项目 | 值 |
|---|---|
| 本地路径 | `D:\arithmetic` |
| 远程地址 | `git@github.com:lixianxin/leetcode-hot100.git` |
| 分支 | `main`（只用这一个） |
| 认证 | SSH 密钥，无需密码 |
| 提交身份 | `lixianxin <lixianxin@users.noreply.github.com>` |

---

## 每天的三条命令

做完题、写好文档之后，在 `D:\arithmetic` 里执行：

```bash
git add -A
git commit -m "001 两数之和: 哈希一次遍历 O(n)"
git push
```

就这三条。`git add -A` 表示"把所有改动加入本次提交"，`commit` 是"存档并写说明"，`push` 是"上传到 GitHub"。

---

## 五个必须记住的命令

| 命令 | 作用 | 什么时候用 |
|---|---|---|
| `git status` | 看哪些文件改了 | 每天开工前、提交前 |
| `git add -A` | 把所有改动暂存 | 提交前 |
| `git commit -m "说明"` | 存档 | 提交时 |
| `git push` | 上传到 GitHub | 提交后 |
| `git log --oneline` | 看历史 | 想回顾进度时 |

---

## 什么时候该 commit

| 时机 | 说明怎么写 |
|---|---|
| 一道题裸写通过 | `001 两数之和: 哈希一次遍历 O(n)` |
| 修了一个 bug | `fix 026: 空数组时越界` |
| 重写了同一题的解 | `refactor 001: 暴力改哈希` |
| 更新了文档 | `docs W01: 补 D3 语法坑记录` |
| 一天结束 | `daily W01-D3: 完成 2 题，裸写 1/2` |

**判断标准：做完一件完整的事就 commit 一次。** 不要攒一周再提交——攒久了 commit 信息就只能写"本周更新"，历史就废了。

---

## 常见报错怎么办

### `nothing to commit, working tree clean`

没有改动，不用提交。如果明明改了文件却没检测到，检查是不是改错了目录（`D:\arithmetic` 之外的文件不在这个仓库里）。

### `! [rejected] main -> main (fetch first)`

远程有你本地没有的提交（通常是在 GitHub 网页上改过文件）。先拉下来再推：

```bash
git pull --rebase
git push
```

### `Author identity unknown`

没配提交身份。执行一次：

```bash
git config user.name "lixianxin"
git config user.email "lixianxin@users.noreply.github.com"
```

### `Permission denied (publickey)`

SSH 密钥失效或不是这台机器。检查：

```bash
ssh -T git@github.com
```

正常应返回 `Hi lixianxin! You've successfully authenticated`。如果不是，说明密钥没配对。

### `file is 105.00 MB; this exceeds GitHub's file size limit`

提交了超过 100MB 的文件（通常是数据集或视频）。这个仓库只放代码和 markdown，不该出现这种文件。

---

## 不要做的事

| 禁止 | 原因 |
|---|---|
| `git push -f` | 强制覆盖远程，会丢历史。真需要时先问 |
| 提交 `__pycache__`、`.venv` | 已在 `.gitignore` 里，别手动加进去 |
| 一个 commit 塞 20 道题 | 历史失去追溯价值 |
| 提交含个人隐私的文件 | 仓库是 public 的 |

---

## 万一搞乱了

不用慌，也不需要重装。记一件事：**本地文件不会因为 git 出错而消失**。把 `git status` 的输出发给我，我告诉你下一步执行什么，不要自己试 `git reset --hard`（这个真的会删改动）。
