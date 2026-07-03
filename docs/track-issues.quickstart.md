# track-issues · Quick Start

> 一句话:查已提社区 issue 的回复,读懂社区给的修复方案并真机复测,PASS 就关 issue + 写 FAQ,FAIL 经确认后追问——闭环的最后一环。

## 什么时候用
- 之前用 report-issues 把失败算子提到了 GitHub / Gitee / GitCode,想看看社区回没回、回了啥。
- 社区在 issue 里给了修复建议(设环境变量 / 改构建参数 / 清理遗留文件 / 给出补丁 / 升级版本),想按方案重跑一遍验证到底修没修好。skill 会读懂方案、准备好(如给补丁则**建分支 + 存 diff,不自动 apply**),**是否继续复测经你确认**。
- 想把已验证的修复沉淀成 FAQ,并把修好的 issue 关掉。

## 怎么用
- 调用:直接说大白话「查一下我提的 issue 有没有回复」「按社区方案重试一下并关 issue」,或 `/track-issues`。
- 最小示例:
  ```
  你:查下已提交的 issue 回复,能修的复测一下
  skill:读 cann-ops-report/issues/state.json,按状态分组列出「等待回复 / 有回复待处理 / 已闭环…」,问你要查哪些
  你:查有回复的
  skill:拉评论 → 读懂候选方案(标可信度)→ 让你选一个 → 自动发现 SOC/repo_path → 真机复测
        → PASS:预览回评+关闭动作(dry-run)让你确认 → 确认后真发、写 FAQ
  ```

## 产出
- 复测结果(PASS / FAIL / partial-PASS)与更新后的 issue 状态,均写回 `cann-ops-report/issues/`(评论 `comments/`、方案 `plans/`、回评草稿 `replies/`、state.json)。
- 已验证修复沉淀到 FAQ:`cann-ops-report/faq/known_fixes.json` + `cann-ops-report/faq/FAQ.md`。
- 结尾给一段汇总:各 issue 分别 PASS 闭环 / FAIL 追问 / 还没回复 / 等 PR 合并,以及新开的 follow-up issue 链接。

## 注意
- **复测要真机 NPU**:方案是靠真跑算子示例(phase1)验证的,纯本地跑不了这一步;需要 `state.json` 里已有已提交记录(没有就先跑 report-issues,或手动登记 issue URL)。
- **查回复 / 关 issue / 发评论需要凭据并先经你确认**:按平台配好 `gh` 或 `GITEE_TOKEN` / `GITCODE_TOKEN`;关闭、评论、开 follow-up 等外发动作默认先 dry-run 预览,点头才真发,可全程 `CANN_OPS_DRY_RUN=1` 干跑。
