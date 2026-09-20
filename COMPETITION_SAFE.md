# Competition-Safe 分支说明

本分支用于真实数学建模竞赛中的稳定运行。

## 第一阶段改动

- M1/P1/P2/W1/W2 默认最多两次独立质检。
- 第一次为 discovery review，第二次为 regression review。
- 两轮后仍失败则 `AUTO_RETRY_EXHAUSTED`，停止自动返工并交由人工决策。
- 引入稳定问题 ID 和作用域化 PASS 失效规则。
- 增加 `MODEL_FROZEN`、`RESULTS_FROZEN`、`PAPER_FROZEN`。
- Checkpoint V1 形成后优先保护可提交版本。

## 回归测试重点

用曾触发大量 M1 循环的真实题目测试：第二次 M1 后无论 PASS/FAIL，都不得自动出现第 3 次 M1；若失败，应汇总 unresolved issues 后停止。