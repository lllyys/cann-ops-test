# setup-env · Quick Start

> 一句话:把一台裸机/新服务器搭成「能 build、能跑测 CANN 算子仓」的基础环境,是跑测闭环的前置第 0 步(为 ops-test 铺路)。

## 什么时候用
- 拿到一台新机器,想开始跑算子,但 `source set_env.sh` 用不了、仓还没拉、依赖没装。
- 想给机器配好 conda 环境 + 把算子仓切到跟 CANN 版本配套的 tag。
- ops-test 跑测前报「ASCEND_HOME_PATH 未设置 / build.sh 缺依赖 / 仓在 master 编不过」。

## 怎么用
- 调用:直接说大白话「帮我在这台机器搭 CANN 跑测环境」「初始化下环境 / 配个 conda」,或 `/setup-env`。
- skill 按 P1–P6 走:先只读探测现状(CANN 路径/版本/SOC、conda、构建依赖 cmake/gcc、python)→ 再逐步补齐(**系统包只给安装命令、不替你 sudo**;建 conda、拉仓切 tag、装仓依赖、单算子冒烟编译由它代做)。机器布局、CANN 版本、conda、python 版本每次运行时探测或问你,不写死。
- 最小示例:
  ```
  你:帮我把这台新服务器搭成能跑 CANN 算子的环境
  skill:探测到 CANN 9.0.0-beta.1(SOC 自动识别 ascend910_93)、有 conda、cmake 齐、ccache 缺
        → 列计划(建 conda env / 拉 ops-nn 并切配套 tag / 装依赖)→ 你点头 → 逐步执行 → 冒烟编译验证
  ```

## 产出
落在当前工作目录 `cann-ops-report/setup/` 下:
- `env_report.md` —— P1–P6 结果 + 「已就绪 / 待办」中文总结(给人看)
- `status.json` —— 机读状态,并指引下一步 `scann-repo`

## 注意
- **要在目标机器上真跑**,会有副作用(建 conda、pip install、git clone/checkout);但建 env / 装依赖 / 切 tag 等每类动作都**先列计划、经你确认**才做,或 `CANN_OPS_DRY_RUN=1` 全程干跑只出计划。
- 系统包(ccache 等)缺失只报告 + 给安装命令,**不替你 sudo**;算子仓 master 是中间态,无配套 tag 时会问你(用最近 tag / 暂用 master / 跳过),不静默用 master 硬编。
