# scann-repo · Quick Start

> 一句话:纯静态扫描 CANN ops 仓,找出用了 Ascend 950 硬件特性(SIMT / HIF8 / RegBase)的算子,给测试团队产出一份靶子清单。

## 什么时候用
- 想知道某个 ops 仓里哪些算子用了 950 特性,好挑跑测目标时
- 要给测试团队一份「950 相关算子」清单(带证据)时
- 想快速摸一下某仓的 950 覆盖度时

## 怎么用
- 调用:直接说大白话「扫一下 ops-transformer 的 950 算子」「帮我找 950 相关算子」,或 `/scann-repo`
- 无需 NPU,纯静态读源码即可;仓路径不写死——skill 会在当前工作目录(CWD)下找含 `docs/zh/op_list.md` 的仓当候选让你确认,找不到就问你要绝对路径。
- 最小示例:
  ```
  你:扫一下当前目录里那个 ops 仓的 950 算子
  skill:发现候选 ./ops-transformer(含 docs/zh/op_list.md),确认扫这个?→ 扫描 → 汇报产物
  ```

## 产出
落在 CWD 下的 `cann-ops-report/<repo>/scann/`:
- `summary.md` —— 主清单,命中算子 + README/代码不一致提示(给人看)
- `detail.md` —— 每个命中的证据明细
- `_intermediate.json` —— 机读 JSON,给下游工具用

## 注意
- 目标仓必须有 `docs/zh/op_list.md`,否则视为「非 CANN ops 仓」直接报错。
- 只认三条关键字规则:SIMT=`__simt_vf__`、HIF8=`HIFLOAT8`、RegBase=`AscendC::MicroAPI::RegTensor`;是静态匹配,不做编译/跑测。
