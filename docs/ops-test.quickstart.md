# ops-test · Quick Start

> 一句话:对 CANN ops 仓里的目标算子跑示例跑测(build → install → 真机跑算子 examples),产出跑测报告。

## 什么时候用
- 想验证某个算子在真机上能不能编、能不能跑(单算子)。
- 想把一个仓、甚至多个仓的目标算子批量跑一遍看通过率。
- 之前跑挂了,要续跑 / 重测失败算子,或诊断某个算子为什么失败。

## 怎么用
- 调用:直接说大白话「帮我跑一下 ops-transformer 的算子」「诊断下 xxx 算子为啥失败」,或 `/ops-test`。
- skill 会依次问你:目标仓 + 本地路径、目标算子来源(scann 产物 / 你列举 / 清单文件)、**SOC**(必问,不臆测)。
- 最小示例(单算子,体现要传 SOC):
  ```
  你:帮我跑 ops-transformer 里的 grouped_matmul,SOC 是 ascend910b
  ```
  SOC 可填 `ascend910b` / `ascend950` 等,也可以选「自动探测」让 runner 用 `acl.get_soc_name()` 读真机芯片。

## 产出
落在当前工作目录的 `cann-ops-report/` 下,按仓分子目录:
- `cann-ops-report/<repo>/test/run_state.json` — 每个算子的机读跑测状态(权威)
- `cann-ops-report/<repo>/test/logs/` — 每个算子的 build/install/run 日志
- `cann-ops-report/SUMMARY.md` — 每轮跑完自动生成的跨仓简洁摘要(给人看)
- `cann-ops-report/<repo>/test/PHASE{N}_FINAL_REPORT.md` — 最终详细报告(仅你要求时才生成)

判定分 4 层:L0 退出码≠0 → FAIL;L1 强失败信号 → FAIL;L2 强成功信号 → PASS;L3 都没命中 → UNCERTAIN(不阻塞,最后统一复核)。跑完支持续跑(跳过已 PASS)、失败诊断、修复后重测。

## 注意
- 必须在**真机 NPU + 已装 CANN** 的环境跑(build/install/run 都在昇腾硬件上执行);本地 Mac 只能开发,跑不了。
- 当前只开放「示例跑测(examples)」;kernel UT / pytest / msprof 暂未开放。CANN 环境无需手动 source,runner 会自动处理。
