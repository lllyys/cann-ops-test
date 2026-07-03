# tech-docs-guard —— 算子文档「对照代码」体检 · 使用说明

给 CANN 算子仓的技术文档(进阶教程 / 开发指南 / 安装 / context 等)做**质量体检**:通读文档 + 拿算子代码当裁判做**静态**核对,逐条带证据指出「漏讲 / 对不上代码 / 过时 / 讲错」,出一份带修改建议的报告(Markdown + 可交互 HTML)。

> 本文件面向**使用者**(怎么调、怎么读报告);skill 的**行为定义**在 [`SKILL.md`](../skills/tech-docs-guard/SKILL.md),问题分类清单在 [`references/problem-taxonomy.md`](../skills/tech-docs-guard/references/problem-taxonomy.md)。

---

## 一、这是什么 / 不是什么

| 它做 | 它不做 |
|---|---|
| ✅ 读文档 + grep/parse 代码,静态核对一致性 | ❌ **不真跑**(不编译 / 不安装 / 不执行算子) |
| ✅ 出体检报告(问题 + 证据 + 改法) | ❌ **不改文档、不补内容、不探索** |
| ✅ 结论分「确认对不上 / 疑似 / 未找到静态证据」三态 | ❌ 不下「能跑通」结论(那是 quickstart-check / ops-test 的事) |

一句话:**只评、不改、不跑**,只对着文档和代码出诊断。纯静态,**本地即可,不需 NPU**。

---

## 二、安装与快速开始

### 0. 安装 / 启用

skill 目录是 `skills/tech-docs-guard/`,是一个标准的 Claude Code skill(`SKILL.md` + scripts)。让你的环境认得它——两种常见方式:

```bash
# 方式一(本项目用的):把 skill 链接进项目的 .claude/skills/(单一源、改仓即生效)
mkdir -p .claude/skills
ln -sfn <本仓路径>/skills/tech-docs-guard .claude/skills/tech-docs-guard
```

- 方式二:随 **cann-ops 插件**一起安装,由插件加载。
- 具体启用路径以你所用平台为准;**新增 / 重链后一般需重启(重新加载)** 才认得它,之后改 SKILL.md / scripts 内容即时生效。

### 1. 调用

**最省事:直接用大白话说需求**,skill 会按描述自动激活——「评一下 ops-nn 的文档写得行不行 / 跟代码对得上吗 / 哪里漏了」。

也可以用命令名显式调(Claude Code 里技能名即命令):

```
/tech-docs-guard
```

调用后它会**先发现、再确认**(不写死任何路径):
- **目标仓 + 代码根**:从当前目录发现含 `docs/` 的候选仓;**有多个或拿不准时会问你确认**,你也可直接给绝对路径;
- **评哪些文档**:用 `AskUserQuestion` **让你确认评哪些**(可多份);**范围默认只取根 `docs/` 下的技术文档**(开发指南 / 安装 / context / invocation 等),不逐个算子目录扫,要扩到某算子目录由你显式指定。

> 这些是 skill 按 SKILL.md 驱动的**交互意图**,不是脚本级的硬保证——只有一个明显候选仓时,它可能直接采用而不额外发问。

**一次典型交互长什么样**(真实例子):你在含多个算子仓的工作区里说「评一下文档」——
1. 它扫出候选仓(如 `ops-nn / ops-cv / ops-math / ops-transformer` 都含 `docs/`),多个 → 弹窗问你评哪个;
2. 你选 `ops-nn` → 它列出 `ops-nn/docs/` 下的技术文档(如 31 篇,数量随仓而定),确认评哪些(默认全评);
3. 逐篇读文档 + 对照 `ops-nn/` 代码核对 → 出报告到 `cann-ops-report/tech-docs-guard/ops-nn/`;
4. 打开 `REPORT.html` 看结果。

### 2. 产物落在哪

```
<你的当前目录>/cann-ops-report/tech-docs-guard/<repo>/
├── REPORT.html     ← 体检报告(可交互,浏览器打开即用,推荐)
├── REPORT.md       ← 同源 Markdown(另含五轴总评 + 未过闸的「待补项」,便于 diff / PR 引用)
├── findings.json   ← 机读:每条问题的完整字段(不去重)
└── doc_meta.json   ← 本轮评了哪些文档、哪些轴
```

---

## 三、它查什么(8 类)+ 怎么判

每条问题 = 一个**类型** + 三根轴(**严重度** / 缺陷类型 / 确定度)。

**A. 能对着代码客观核对的(对错分明)**
1. **引用找不到** — 链接 / 章节 / 让你参考的文件目录,仓里其实没有
2. **名字 / 接口 / 参数与代码对不上** — 文件名 / 命令 / 签名 / dtype 表 vs 代码
3. **贴出的代码片段本身有错** — 拼写错、声明与使用对不上
4. **文档自相矛盾** — 同篇 / 同算子多篇说法不一
5. **支持范围声明有误** — 产品支持表 √× / 芯片 / 版本适配 vs 代码注册(假 √ 最坑)
6. **运行结果 / 数值 / 行为说错** — 命令产物 / 路径 / 应见输出、数值 / 公式 vs 实际

**B. 要靠人判断的(影响「学不学得会」)**
7. **概念讲错 / 术语用混**
8. **缺步骤 / 没讲为什么 / 指代含糊**

**严重度三档**(报告按此排序,阻断在前):
- **阻断**:照做会失败(编不过 / 步骤扑空 / 选到用不了的芯片) → 优先修;
- **误导**:会困惑、走弯路,但最后能弄对;
- **瑕疵(minor)**:几乎无影响 → **默认舍弃不报**(想看用 `--with-minor`)。

**另两根轴**(报告卡片上的徽章,别被绕住):
- **确定度**:`确认`(对照代码 / 文档坐实)或 `疑似`(线索级、需人工复核);
- **缺陷类型**:`不可信`(与代码 / 自身冲突)/ `缺失`(文档里根本没有)/ `易读`(在且对但表达费解)——决定卡片用哪种对照图(冲突 / 缺口 / 并列)。

**三条判定原则**(所以报告可信、不噪):
1. **不打玄学分** — 主观判断(讲不清 / 讲错)报之前先 steelman(替它辩护到最强,辩不过才算问题);
2. **grep 不到 ≠ 编造** — 只有强证据反证才写「确认对不上」,否则标「疑似 / 未找到静态证据」;
3. **按实际开发者影响判** — 文档里大量是示意 / 演示,不是逐字粘贴;只有「规定式」内容照做真出错才升阻断。

---

## 四、报告怎么读(HTML)

打开 `REPORT.html`,顶部到底部:

- **汇总条** — 开发者影响分布(阻断 / 误导)、缺陷类型分布、总数 / 代码可核 / 教学判断 / 疑似;
- **阻断横幅** — 阻断级问题的徽章,点一下跳到对应条目;
- **8 类问题速览表** — 每类的问题数 + 阻断 / 误导分布,**点任一行 = 只看该类**;
- **筛选条** — 切「按类型 / 按文件」视图,按严重度 / 确定度 / 类型筛;
- **问题卡片**,每条含:
  - 一句话**问题** → **后果**(阻断类标红);
  - **红绿对照图**(finder 给出 `fig` 时才显示,拿不准会省略):红框「文档写的」↔ 绿框「实际应为」;
  - 绿色「✓ 应改为」修改建议;
  - 折叠区:**文档原文引证 + 代码位置**(可去仓核对);
  - 三轴徽章:严重度(阻断 / 误导)· 确定度(确认 / 疑似)· 类型;
- **「复制修改清单」** — 一键复制当前筛选下的待改项。

> `REPORT.md` 与 HTML **同源但不完全等同**:HTML 便于交互筛选;MD 另有**五轴总评** + 未过自校验闸的**「待补项」**,便于贴进 PR / issue、做 diff。

---

## 五、进阶:直接调脚本

> **完整报告由 skill 流程产出**:它跑 LLM finder 通读文档、对照代码,把问题写进 `cann-ops-report/tech-docs-guard/<repo>/findings.json`(+ `doc_meta.json`),再由 `render_report` 渲染。下面的脚本是流程里的**部件**,可单独跑来**预览范围 / 复核 / 二次渲染**——但**把这几条串起来 ≠ 完整报告**:缺了 finder 这步,且 T0 脚本的输出**不会自动进** `findings.json`。

脚本以模块方式运行,**cwd 在 skill 目录**:

```bash
cd skills/tech-docs-guard

# ① 预览「评哪些文档」(只列文档、不评):严格限定 <repo>/docs/ 子树
python3 -m scripts.find_tutorials <repo_root> --under docs --json

# ② T0 确定性检查(~0 token):把候选 finding 写到指定 JSON —— 是 T0 素材,不会自动进报告
python3 -m scripts.linkcheck <repo_root> --under docs --json /tmp/link.json                 # 死链 / 死锚(C1)
python3 -m scripts.support_table_check <repo_root> --under <algo_dir> --json /tmp/sup.json   # 支持表跨文档矛盾(C5,针对算子文档)

# ③ 从已有 state 渲染报告(读 cann-ops-report/.../findings.json + doc_meta.json → MD + HTML)
python3 -m scripts.render_report --repo <repo> --format both        # md | html | both
python3 -m scripts.render_report --repo <repo> --with-minor         # 保留瑕疵(默认舍弃)
```

> 想「只跑 T0 就出报告」:需把 ② 的 JSON 合并写进 state 的 `findings.json`、再写一份 `doc_meta.json`,然后 ③ 渲染;正常流程里这步(含 LLM finder 的判断)由 skill 自动完成。

> **关于批量 / 全仓**:评多篇算子文档时,skill / 工作流内部会**按算子分组**(一个 finder 评一个算子的全部文档,分类清单 + 该算子代码只载一次),实测省约一半 token;中央文档(`docs/zh/...`)逐篇评。这是 **skill 编排层的策略,不是你手敲的命令**——你只管说「评这个仓」,分组由它自理。

**常用选项**
| 选项 | 作用 |
|---|---|
| `--under <dir>` | 限定发现 / T0 到 `<repo>/<dir>` 子树。**skill 默认传 `--under docs`;脚本裸跑(不传)默认各不同**:`find_tutorials` 全仓找「进阶教程」启发式、`linkcheck` 全仓、`support_table_check` 全仓但跳过 `docs/` |
| `--with-minor`(或 `TECH_DOCS_GUARD_WITH_MINOR=1`) | 保留瑕疵级问题(默认舍弃,只报阻断 / 误导) |
| `--format md\|html\|both` | 只出 MD / 只出 HTML / 两者(默认 both) |

---

## 六、注意事项

- **纯静态、本地即可**:只读文档 + grep 代码,默认不真跑,**无 build/install 副作用**,不需 NPU;若代码在远程,可在远程 grep 或把代码根同步回本地。
- **依赖**:`python3`(注意不是 `python`)+ 系统 `grep`;纯 stdlib,无第三方包。单测 `python3 -m pytest tests/ -q`(需 pytest)。
- **结论的分寸**:可量化类(1–6)可下「确认对不上」并计数;教学判断类(7–8)最多写「疑似」,除非有外部反证。报告不会凭空指控。
- **范围**:默认只看根 `docs/` 技术文档(算子文档全仓上千篇、问题过多);要扫算子目录请显式 `--under <algo_dir>`。

---

## 常见问题

- **报告是空的 / 没有问题条目?** 几种常见成因:① 只跑了脚本、没跑 finder,`findings.json` 没写入(见第五节开头);② `render_report` 的 cwd / `--repo` 指错,读不到 state;③ 命中的全是 minor,被默认过滤(`--with-minor` 可保留);④ 条目没过自校验闸(缺代码位置 / 判例)→ **HTML 里不显示,但 MD 会以「待补项」列出**。
- **`python: command not found`?** 用 `python3`(脚本一律 `python3` 调)。
- **想连瑕疵(minor)一起看?** 加 `--with-minor`,或设 `TECH_DOCS_GUARD_WITH_MINOR=1`。
- **想扫某个算子目录(不只 `docs/`)?** 让它 `--under <algo_dir>`,或直接说「评 ops-nn 的某算子目录」。
- **报告里某轴标「本轮未评」?** 那是如实标注覆盖缺口(没评的轴不会假装「合格」),不是 bug。

---

## 相关文件

| 文件 | 作用 |
|---|---|
| [`SKILL.md`](../skills/tech-docs-guard/SKILL.md) | skill 行为定义(给 AI 执行用) |
| [`references/problem-taxonomy.md`](../skills/tech-docs-guard/references/problem-taxonomy.md) | 8 类问题清单 + 三轴 + 判定纪律(finder 的 checklist) |
| [`templates/report-engine.html`](../skills/tech-docs-guard/templates/report-engine.html) | HTML 报告引擎(自包含 CSS+JS,勿改) |
| `scripts/` | `find_tutorials` 发现 · `codecheck` 对照 · `linkcheck` / `support_table_check` T0 · `render_report` 渲染 · `_state` 状态与自校验闸 |
