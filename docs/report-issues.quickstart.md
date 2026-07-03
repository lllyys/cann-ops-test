# report-issues · Quick Start

> 一句话:把 ops-test 跑测出的失败算子,整理成上游社区可受理的 issue 草稿(一算子一篇),支持半自动提交。

## 什么时候用
- 跑完 `ops-test`,有算子 build/install/run 失败,想报给上游社区
- 想给这些失败批量生成规范的 issue 草稿,提交前自己审一遍
- 已经手工提了 issue,想回写记录标成「已提交」(供后续 track-issues 跟踪)

## 怎么用
- 调用:直接说大白话「给这些失败算子提 issue / 向社区报告失败」,或 `/report-issues`
- 最小示例:
  1. 你:「把 ops-transformer 跑挂的算子提到上游社区」
  2. skill 读 `run_state.json`,列出各仓失败,问你要提哪些(全部 / 挑仓挑类型)
  3. 每个失败算子生成一篇草稿(环境 / 复现命令 / 真实错误日志摘录 / 建议 labels)
  4. 逐篇预览让你 review;你可选「先看草稿改好再说」或「直接提交」
  5. 提交:或输出 prefilled URL 你自己点提,或由 skill 帮你提(GitHub/Gitee/GitCode)
  6. 记录提交成功的 issue URL,回写 `state.json`

## 产出
- 全部落在 `cann-ops-report/issues/` 下:
  - `drafts/<repo>/per_op/<op>__<type>.md` — 每算子一篇 issue 草稿(另有 `by_type/<type>.md`、`whole_repo.md` 两种粒度)
  - `state.json` — 去重与提交状态
  - `submitted/<repo>/<id>.json` — 已提交记录

## 注意
- 前置:先跑过 `ops-test` 并产出 `cann-ops-report/<repo>/test/run_state.json`,否则报错退出;skill 还会**询问 / 确认 SOC**(写进 issue 环境和标题,不臆测)。
- 提交是外发、不可撤的操作:**默认只生成本地草稿**,每篇先预览、经你确认才真提;
  提交需凭据——GitHub 用 `gh`(先 `gh auth login`),Gitee 用 `GITEE_TOKEN`,GitCode 用 `GITCODE_TOKEN`。
