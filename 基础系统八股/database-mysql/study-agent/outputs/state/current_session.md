# 当前学习会话

- 主题：MySQL notes 综合 MCQ 复习
- 源材料：`outputs/notes/` 下所有 MySQL 学习笔记
- 当前抽题范围：架构、SELECT 执行、行格式、索引、COUNT、事务/MVCC、幻读、锁、死锁、Buffer Pool、日志、UPDATE 加锁等。
- 当前 schema：COUNT 的非 NULL 表达式语义
- 学习者水平：Level 2-3。已有多章分步学习记录；本轮目标是通过 MCQ 巩固边界判断，而不是重新讲完整章节。学习者已说明“架构分层”完全掌握，后续综合 MCQ 不再优先抽该点。
- 出题规则：
  1. 一次只出一道选择题。
  2. 学习者作答后，再给正确答案、解析和必要的边界修复。
  3. 后续题目轮换覆盖 `outputs/notes/` 的不同主题。
- 上次回答：
  - MCQ 1 选择 B。
  - MCQ 2 选择 B。
  - MCQ 3 选择 C。
  - MCQ 4 选择 B。
- 评价：
  - MCQ 1 正确。Server 层负责连接、解析/预处理、优化和执行协调；InnoDB 层负责页、B+Tree、Buffer Pool 等物理数据访问。学习者补充说明该架构分层点已完全掌握。
  - MCQ 2 正确。`trx_id=51` 位于 `[min_trx_id=51, max_trx_id=53)` 且在 `m_ids` 中，表示创建 Read View 时该事务仍活跃，所以该版本对事务 B 不可见。
  - MCQ 3 正确。非唯一索引 `age=22` 命中后，二级索引上要锁匹配记录的 next-key，匹配行对应的主键记录要加 Record Lock，并继续扫描到第一个不匹配项，在其前方 gap 上阻止新的同值记录插入。
  - MCQ 4 正确。redo log 处于 prepare 状态时，恢复判断要用 XID 去 binlog 查；binlog 中有同一 XID，说明事务应该提交，以保持 redo log 和 binlog 一致。
- 待回答问题：MCQ 5：表 `t(id, name)` 有 3 行，`name` 分别是 `'a'`、`NULL`、`'b'`。在没有 `where` 条件时，下列说法哪一个正确？
  - A. `count(*) = 3`，`count(1) = 3`，`count(name) = 2`。
  - B. `count(*) = 2`，因为 `*` 会跳过含 NULL 的行。
  - C. `count(1) = 2`，因为常量 `1` 会被替换成非 NULL 字段。
  - D. 三者都等于 3，因为 COUNT 只统计行数，不看表达式是否为 NULL。
- 下一步：等待学习者选择 A/B/C/D；答完后给答案与解析，再决定是否进入下一题或做边界修复。
- 更新时间：2026-07-09
