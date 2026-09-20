---
name: 编程手
description: 数学建模的 Python 或 MATLAB 实现、运行、表格输出、可视化和复现阶段。
---

# 编程手

## 路径

- `ROLE_ROOT`：本文件所在目录。
- `SKILL_ROOT`：`ROLE_ROOT/../../..`，只读。
- `PROJECT_ROOT`：用户项目目录，所有代码、结果和图只写这里。

## 输入

优先读取 `PROJECT_ROOT/题目分析报告.md`、`PROJECT_ROOT/术语表格.md` 和题目附件。若用户只执行本阶段，可从用户提供的模型说明开始；若说明不足以实现，先反馈缺项。

## 固定产物

- Python `.py`、MATLAB `.m`，或用户要求的两套实现。
- `results/` 中的运行结果表格和必要文本结果。
- `figures/` 中按论证需要生成原始数据图、模型运行过程图和模型最终结果图。每个子问题要有足以支撑核心结论的证据，但不强制每问同时具有三类图，也不设置统一的 9 图最低配额。需要生成图时使用 `raw_q1_*`、`process_q1_*`、`result_q1_*` 命名；同一图的 SVG、PNG 和灰度预览只计 1 张。
- **必须至少 1 幅总体建模流程图** `flow_overall_model.*`，覆盖输入、子问题依赖、模型、求解、验证与输出；子问题存在独立分支、循环或三步以上求解链时，补充 `flow_qN_model.*`。与三类候选图分开，不计入其配额，也不替代结果图。
- `results/复现清单.json`。

## 执行顺序

1. 按用户要求或现有项目语言选择 Python/MATLAB；没有偏好时按模型依赖和现有环境选择并说明。
2. 按选中的模型功能动态检查依赖，禁止一次性要求全部包：
   - Python：`python scripts/check_env.py --features data visualization optimization`
   - MATLAB：`check_matlab_env(["data","visualization","optimization"])`
3. 实现数据读取、预处理和核心求解链，用真实输入或结构等价小实例跑通从 `PROJECT_ROOT` 执行的最小命令；任何结论必须来自真实输出。
4. 在全量计算、参数扫描和正式出图前执行 `P1` 最小可运行结果门禁。P1 最多两轮独立质检；第二次仍失败时停止自动返工。只有用户显式 `HUMAN_OVERRIDE:P1` 接受已列明风险后才可继续扩展，且不得把 P1 记为 PASS。
5. 从题目分析报告提取全部子问题并规范为 `q1…qN`。绘图前加载 `tools/figure/SKILL.md` 并完成数据剖析与图表契约，按子问题核对行列、类型、缺失、分组样本量、分布、异常值和单位；先用一句话写出核心结论，再选择图型、证据面板、主次比例、统计口径、图例策略与最终尺寸。无法判断图型或用户指定图型存在误导风险时，读取 `tools/figure/references/chart-types/chart_selection.md`。
6. 将 `scripts/plot_style.py` 或 MATLAB 的出版绘图工具复制到 `PROJECT_ROOT/utils/` 后使用。按 `tools/figure/SKILL.md` 的视觉论证流程只生成对结论有明确作用的图；不为满足内部数量指标制造冗余图。不同重要性的面板不机械等分，统计标注必须由代码计算，官方模板要求优先于内置基线。
7. 所有正式图必须经 `tools/figure/scripts/export_figure.py` 或 `export_publication_figure()` 的布局与设计门禁，同时输出 SVG 与至少 300 DPI PNG；运行 `python "<SKILL_ROOT>/references/roles/编程手/scripts/figure_audit.py" "<PROJECT_ROOT>/figures" --questions q1 q2 ... qN --strict` 检查已有图的格式、DPI 与基础完整性。默认不检查固定类别数量；只有官方规则或用户明确要求图类/图数时才追加 `--require-category-check --min-per-category N`。随后实际打开成图检查可读性。
8. 生成建模流程图（**必须**）：至少 1 幅总体流程图，覆盖 输入 → 子问题依赖 → 模型 → 求解 → 验证 → 输出；仅当子问题存在独立分支、循环或三步以上求解链时，才补充该子问题的 `flow_qN_model`。节点与连线必须来自已确认的模型和真实代码，用已安装的 Matplotlib/MATLAB 原生绘图能力生成（不新增依赖），保留可运行生成代码；样式、命名、导出与自检见 `tools/figure/references/chart-types/flowchart.md`。
9. 生成复现清单：`python scripts/repro_manifest.py --project-root <PROJECT_ROOT> ...`。
10. 按 `references/质检清单.md` 完成作者自检，再执行 `P2` 编程终检。P2 最多两轮；第二次仍失败时停止自动返工。用户显式 `HUMAN_OVERRIDE:P2` 后可进入论文阶段，但必须保留失败证据并在最终交付中披露。

## 阶段内独立门禁

- `P1`：质检 Subagent 在隔离环境或只读副本中执行最小命令，核对退出码、输入到结果的追溯、单位、数值范围、关键约束和 `M1` 模型合同。它是纵向切片，不要求完整图表或最终性能。
- `P2`：代码、结果、支撑各子问题核心结论的证据和复现清单冻结后，质检 Subagent 独立运行唯一复现命令并核对输入哈希、种子、关键数值、边界、量纲、图表语义及文件完整性。`figure_audit.py --strict` 默认只审已有图；固定类别数量仅在官方或用户明确要求时启用。Subagent 不得因为“还能多画图/多做实验”而阻塞。

两次门禁均按 `../../../references/Subagent调度.md` 返回证据；被审代码、数据或参数发生实质变化时重跑受影响门禁。

## 何时加载

| 情形 | 读取 |
|---|---|
| 开始实现 | `references/工作流程.md` |
| 使用 MATLAB | `references/MATLAB规范.md` |
| 画图 | `tools/figure/SKILL.md` |
| 不确定用什么图，或需审查指定图型 | `tools/figure/references/chart-types/chart_selection.md` |
| 需要图表函数 | `tools/figure/references/api-templates/plot_recipes.md` |
| 需要具体算法 | `../../../references/算法索引.md`，再读取匹配的 `../../../assets/*.md` |
| 处理 Excel | `../../../tools/xlsx/SKILL.md` |
| 交付前 | `references/质检清单.md` |
| 阶段内独立验收 | `../../../references/Subagent调度.md` |

若实际运行证明模型公式、约束或参数定义冲突，停止通过改算法规避问题，把证据反馈给建模手。
