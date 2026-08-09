# Error Log

## 2026-07-07

- Topic: MySQL non-unique secondary-index equality hit - missing primary-key record lock.
- Question: `age=22` 命中 `(age=22,id=10)`，下一条不匹配是 `(age=39,id=20)`。为什么需要二级索引 `(21,22]`、主键 `id=10` Record Lock、以及二级索引 `(22,39)` Gap Lock？
- User answer: "不能只锁 (21,22]，因为如果只锁到已有的 age=22,id=10，别人仍可能插入新的 age=22,id=12，下一次查询 age=22 就多一行，产生幻读。加 (22,39) Gap Lock，阻止新的 age=22 插入到后方位置。"
- Correct reasoning: The secondary-index gap reasoning is correct, but a matching row was actually found. Because the row's primary key is `id=10`, InnoDB also locks the clustered primary-key record to prevent other transactions from updating or deleting that real row while the locking read holds it.
- Error type: Boundary confusion / incomplete lock target.
- Fix strategy: Separate the two jobs: secondary-index locks protect the search range and prevent same-value phantom inserts; the primary-key Record Lock protects the matched real row from update/delete.
- Repair status: Passed targeted repair. User stated that locking primary key `id=10` prevents other transactions from updating or deleting the matched row.

- Topic: MySQL insertion intention lock purpose.
- Question: 事务 A 持有 `(3,5)` 间隙锁且未提交，事务 B 插入 `id=4` 时会发生什么？这里生成的插入意向锁是前面说的表级意向锁吗？
- User answer: "b会阻塞，这个插入意向锁是行锁。但是我无法理解插入意向锁的意思，感觉没有也不影响吧"
- Correct reasoning: The blocking conclusion and row-level classification are correct. The missing concept is that an insertion intention lock is the engine's waiting lock structure: it records that a transaction wants to insert at a specific point inside a locked gap and is currently waiting. Waiting state does not mean the lock has been successfully acquired, and the lock is not the same as a table-level intention lock.
- Error type: Concept misunderstanding.
- Fix strategy: Repair with the contrast "gap lock blocks the interval; insertion intention lock records the blocked insert request at a point" before asking the learner to explain why the waiting lock structure is useful.
- Repair status: Passed immediate repair. User stated that the insertion intention lock records transaction B waiting and the insert position information.

## 2026-07-06

- Topic: MySQL COUNT - InnoDB row count accuracy boundary.
- Question: 请用 4-6 句话口述回答：“`count(*)` 和 `count(1)` 有什么区别？哪个性能最好？InnoDB 为什么不能像 MyISAM 一样直接给出准确总行数？大表 count 怎么优化？”
- User answer: "几乎没区别，性能差不多。InnoDB支持事务，不同事务看到不同的count，所以不能保证准确。大表 count 尽量走二级索引来算总行数，减少IO次数"
- Correct reasoning: InnoDB can return an accurate `count(*)` for the current transaction/read view by scanning visible records. The issue is not that InnoDB cannot guarantee accuracy, but that one global `row_count` cannot accurately answer all concurrent transaction views. For large tables, scanning a smaller secondary index is an execution-plan optimization, but business-level optimization also asks whether approximate counts are acceptable or whether a precise counter table should be maintained.
- Error type: Boundary confusion.
- Fix strategy: Repair the sentence from "InnoDB cannot guarantee accuracy" to "InnoDB cannot use one global row count for all transaction views"; then ask the learner to restate the big-table optimization choices.
- Repair status: Passed immediate repair. User restated that InnoDB can give an accurate `count(*)` for the current transaction view, but cannot use one global `row_count` for all transactions.

## 2026-06-26

- Topic: MySQL page-oriented B+Tree - data page vs leaf user data boundary.
- Question: B+ 树非叶子节点里的“目录项”主要是为了直接找到某一行，还是为了定位下一层应该去哪个数据页？为什么？
- User answer: "为了定位下一层应该去哪个数据页，因为数据页只存在于叶子节点，非叶子节点只是用来定位的"
- Correct reasoning: The routing part is correct: non-leaf directory entries are used to decide which lower-level page to visit. The boundary error is that every InnoDB B+Tree node is a data page; what is specific to leaf pages is storing user records or, for secondary indexes, primary-key values. Non-leaf pages are still pages, but they store directory entries rather than full user rows.
- Error type: Boundary confusion.
- Fix strategy: Repair with the contrast "every node is a data page; only leaf nodes store index payload/user data" before advancing to clustered vs secondary indexes.

## 2026-05-30

- Topic: MySQL InnoDB Compact row format - variable-length field length list.
- Question: `VARCHAR(20)` stores `'Tom'`; why does InnoDB also record length information?
- User answer: "记录长度信息，方便查找数据，比如知道大小就好查询"
- Correct reasoning: Length information is mainly for parsing and reading the row correctly. Since `VARCHAR` has variable size, InnoDB needs to know where this field ends and where the next field begins.
- Error type: Concept misunderstanding.
- Fix strategy: Re-teach variable-length fields through field boundary reading, not general data lookup.

## 2026-05-31

- Topic: MySQL InnoDB Compact row format - NULL value list storage size.
- Question: Why do many schema conventions recommend `NOT NULL` from the row-format perspective?
- User answer: "全部是NOT NULL就会节省掉记录null值表的值，少一个NULL就少一个bit"
- Correct reasoning: Nullable columns need bits in the NULL value list, but the NULL value list is stored in whole bytes. Removing one nullable column removes one logical bit, but may not reduce physical row size unless it crosses a byte boundary or all nullable columns are removed.
- Error type: Detail precision issue.
- Fix strategy: Re-teach bit versus byte allocation with 1-8 nullable columns and 9 nullable columns examples.

## 2026-05-31

- Topic: MySQL index classification dimensions.
- Question: Is `UNIQUE KEY uk_email(email)` usually a clustered index or a secondary index in InnoDB? Why?
- User answer: "二级索引，但是我不知道原因。我可能缺失前置知识，比如这四个维度分类分别底层代表什么意思？无法从概念上区别，从文字上太难看懂"
- Correct reasoning: InnoDB normally has one clustered index that stores the full row, chosen from the primary key or an internal fallback. A unique key on `email` is a separate B+Tree whose leaf nodes point to the primary key, so it is a secondary index. Its uniqueness is a field constraint dimension, not the physical-storage dimension.
- Error type: Missing schema / surface-level memorization.
- Fix strategy: Teach the four classification dimensions as four concrete questions instead of a list of names.

## 2026-06-04

- Topic: MySQL B+Tree vs Hash index.
- Question: Why is Hash index not suitable as InnoDB's most common index structure, even though equality lookup is fast?
- User answer: "我猜可能是hash需要太多存储空间了，虽然很快但是空间不够/"
- Correct reasoning: Space and collision costs can matter, but the main reason is that Hash does not preserve key order. It is good for equality lookup, but cannot naturally support range scans, ordered traversal, `ORDER BY`, or prefix-style positioning the way B+Tree can.
- Error type: Concept priority issue.
- Fix strategy: Re-teach B+Tree vs Hash through the question "does this structure preserve order?" before discussing secondary costs such as space and collisions.
