# cann-ops · Skills 快速上手

一套 Claude Code 插件(6 个 skill),帮你完成 CANN 算子的「**搭环境 → 扫仓找靶子 → 跑测 → 失败上报社区 → 跟进修复复测**」闭环,外加**文档体检**。每个 skill 都能**直接用大白话触发**(也可用命令名 `/<skill>`),仓路径 / SOC / 版本等一律**每次会话发现或询问,不写死**。

## 闭环怎么串

```
                         setup-env(把机器搭成能编能跑)
                                │  铺路
                                ▼
   scann-repo ──▶ ops-test ──▶ report-issues ──▶ track-issues
   扫 950 靶子    build/install  失败转社区        查回复 → 读懂修复
   出算子清单     真机跑示例      issue 草稿        → 真机复测 → 关 issue + 写 FAQ
                                                        │
                                └──── 复用已验证修复(FAQ / known_fixes)◀──┘

   tech-docs-guard(文档体检):独立一条线,通读文档对照代码,找「对不上/讲错」
```

## 6 个 skill 一览

| Skill | 一句话 | 需要什么 | Quick Start |
|---|---|---|---|
| **scann-repo** | 扫 CANN ops 仓里用了 950 硬件特性(SIMT / HIF8 / RegBase)的算子,出清单 | 纯静态,**不需 NPU** | [scann-repo](scann-repo.quickstart.md) |
| **ops-test** | 对目标算子跑示例跑测:build → install → 真机跑 examples,出报告 | **需真机 NPU + CANN** · 传 SOC | [ops-test](ops-test.quickstart.md) |
| **report-issues** | 把跑测失败的算子整理成社区 issue 草稿(一算子一篇),半自动提交 | 提交需 `gh` / `GITEE_TOKEN` / `GITCODE_TOKEN` | [report-issues](report-issues.quickstart.md) |
| **track-issues** | 查已提 issue 的回复 → 读懂修复 → 复测 → PASS 关 issue + 写 FAQ | 复测需 NPU · 提交需 token | [track-issues](track-issues.quickstart.md) |
| **setup-env** | 把机器搭成「可编译 / 可跑测」环境(找 CANN、切 tag、装依赖、冒烟) | 在目标机真跑,**有副作用** | [setup-env](setup-env.quickstart.md) |
| **tech-docs-guard** | 通读算子文档 + 对照代码静态查证,出带证据的文档体检报告 | 纯静态,**不需 NPU** | [tech-docs-guard](tech-docs-guard.md)（完整版） |

> 注:除 tech-docs-guard 是完整使用说明外,其余 5 篇均为 30 秒 quick start。

## 通用约定

- **调用**:直接说需求(「帮我跑一下 X 仓的算子」「查下我提的 issue 回复了没」),skill 按描述自动激活;也可 `/<skill>` 显式调。
- **启用**:各 skill 在 `skills/<name>/`;项目里用符号链接进 `.claude/skills/`(或随插件安装),**新增 / 重链后需重启会话**。
- **产物**:统一落在你当前工作目录的 `cann-ops-report/` 下(每仓 / 每用途一个子目录)。
- **副作用先确认**:提 issue / 关 issue / 发评论 / 装环境等外发或改动操作,skill 都先预览、经你点头才做;多数支持 `CANN_OPS_DRY_RUN=1` 干跑。
