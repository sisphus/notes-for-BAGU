# Schema Ledger

## 用架构分层串起 MySQL 组件

- 触发场景：回答“MySQL 的架构是怎么样的”或需要把 Server 层、InnoDB、页、索引、缓存、日志、binlog 串成一个整体。
- 压缩概念：MySQL 位于应用和数据之间；Server 层负责连接、SQL 分析、优化、执行协调和 binlog；InnoDB 层负责页、B+Tree、Buffer Pool、Change Buffer、Adaptive Hash Index、undo log、redo log 和磁盘文件。
- 可用能力：能先按层定位组件，再按一次查询或一次更新说明 SQL 如何从 Server 层进入 InnoDB，并落到页、缓存和日志上。
- 常见失败信号：只背组件名但不能按层分类；把 Server 层和存储引擎层混成一个黑盒；把 undo/redo/binlog 都说成同一种恢复日志。
- 状态：稳定。MCQ 复习中学习者正确区分 Server 层和 InnoDB 层，并明确说明“架构分层”已完全掌握；后续综合题不再优先抽该点。

## MySQL Two-Layer Architecture

- Trigger situation: Explaining what happens when MySQL executes a SQL statement.
- Compressed concepts: Server layer handles connection, SQL parsing, optimization, and execution coordination; storage engine layer stores and retrieves actual data.
- What the learner can do after acquiring it: Separate "SQL processing" questions from "data/index storage" questions when answering MySQL basics.
- Common failure signal: Treats MySQL as one black box, or says the parser/optimizer directly reads disk data.
- Status: Stable.

## SELECT Execution Pipeline

- Trigger situation: Explaining `SELECT * FROM product WHERE id = 1`.
- Compressed concepts: Connector -> query cache -> parser -> preprocessor -> optimizer -> executor -> storage engine.
- What the learner can do after acquiring it: Locate where syntax errors, missing table errors, index choice, and data retrieval happen.
- Common failure signal: Confuses parser with preprocessor, or optimizer with executor.
- Status: Stable.

## Parser vs Preprocessor Boundary

- Trigger situation: Distinguishing SQL syntax errors from missing table or missing field errors.
- Compressed concepts: Parser checks lexical and grammar correctness; preprocessor checks whether referenced tables and fields exist and expands `*`.
- What the learner can do after acquiring it: Correctly place syntax errors in parser stage and missing object errors in preprocessor stage.
- Common failure signal: Says a missing table or missing field is a parser error.
- Status: Stable.

## Optimizer Cost-Based Choice

- Trigger situation: Explaining why MySQL may or may not use an available index.
- Compressed concepts: Optimizer estimates query cost and chooses an execution plan, such as index access or full table scan.
- What the learner can do after acquiring it: Avoid saying "index exists means index must be used"; explain the decision through estimated cost.
- Common failure signal: Treats index usage as a fixed rule rather than a cost-based choice.
- Status: Stable.

## InnoDB Row Storage Hierarchy

- Trigger situation: Explaining where a row lives and why row format details matter.
- Compressed concepts: Tablespace file -> segment -> extent -> page -> row; Compact row format stores extra info plus real data.
- What the learner can do after acquiring it: Explain page-based I/O, NULL bitmap, varchar length storage, hidden fields, and row overflow at a basic level.
- Common failure signal: Treats one row as the I/O unit, or treats `varchar(n)` as byte length.
- Status: Forming.

## Compact Row Format Skeleton

- Trigger situation: Explaining what parts exist inside one InnoDB row record.
- Compressed concepts: A Compact row has record extra information and real data; extra information helps InnoDB parse and manage the row.
- What the learner can do after acquiring it: Separate user-visible column values from storage metadata such as variable-length lists, NULL bitmap, record header, and hidden fields.
- Common failure signal: Says a row only stores the columns defined in `CREATE TABLE`.
- Status: Forming.

## Variable-Length Field Boundary

- Trigger situation: Explaining why `VARCHAR` fields need length metadata in Compact row format.
- Compressed concepts: A variable-length field stores actual data plus byte length so InnoDB can identify where the field ends and the next field begins.
- What the learner can do after acquiring it: Compute simple ascii byte lengths and explain length metadata as row parsing support rather than general lookup acceleration.
- Common failure signal: Says length metadata is mainly for searching data, without mentioning field boundaries.
- Status: Stable.

## Compact NULL Bitmap

- Trigger situation: Explaining how Compact row format represents NULL column values.
- Compressed concepts: NULL values are not stored in the real-data area; Compact records which nullable columns are NULL using a bit list.
- What the learner can do after acquiring it: Explain why nullable columns require NULL metadata and why all-NOT-NULL tables can omit the NULL value list.
- Common failure signal: Says NULL is stored as a normal value inside the real data area.
- Status: Stable.

## Compact Record Header

- Trigger situation: Explaining how InnoDB manages each row record beyond user column values.
- Compressed concepts: Record header stores management metadata such as delete marker, next-record pointer, and record type.
- What the learner can do after acquiring it: Explain why deleting a row can first become a marker change and how records can be linked inside a page.
- Common failure signal: Says record header stores business column values, or says delete always immediately removes the row physically.
- Status: Stable.

## InnoDB Hidden Fields

- Trigger situation: Explaining why the real-data area stores fields that were not written in `CREATE TABLE`.
- Compressed concepts: `row_id` identifies rows when no explicit key exists; `trx_id` records the creating transaction; `roll_pointer` links to an older version for rollback/MVCC.
- What the learner can do after acquiring it: Explain the three hidden fields as row identity, transaction identity, and version link without needing full MVCC details yet.
- Common failure signal: Memorizes field names but cannot say what job each one does.
- Status: Stable.

## Varchar Max Length Accounting

- Trigger situation: Explaining why `varchar(n)` cannot simply use `n = 65535`.
- Compressed concepts: `varchar(n)` counts characters, but the row limit is bytes; the 65535-byte row limit includes row-format overhead such as variable-length metadata and NULL bitmap.
- What the learner can do after acquiring it: Explain why max `varchar` length depends on overhead, nullability, character set, and other columns.
- Common failure signal: Says `varchar(n)` maximum is always 65535.
- Status: Stable.

## Row Overflow

- Trigger situation: Explaining how InnoDB stores a row when large variable-length columns do not fit cleanly in the current page.
- Compressed concepts: InnoDB pages are commonly 16KB, but large column data can be stored on overflow pages with pointers kept in the original row.
- What the learner can do after acquiring it: Reject the claim that a row can never exceed 16KB and explain overflow pages at a basic level.
- Common failure signal: Equates page size with absolute row size.
- Status: Stable.

## Index as Ordered Locator

- Trigger situation: Explaining what a MySQL index is and why it can speed up queries.
- Compressed concepts: An index is a storage-engine data structure that keeps key values organized so MySQL can locate candidate rows without scanning every row.
- What the learner can do after acquiring it: Answer "what is an index" by connecting directory-like lookup, ordered keys, and the space-for-time tradeoff.
- Common failure signal: Says only "index improves query speed" without explaining the locating structure or the extra storage and maintenance cost.
- Status: Forming.

## Index Structure Evaluation Lens

- Trigger situation: Answering why MySQL/InnoDB chooses B+Tree rather than only comparing abstract data-structure time complexity.
- Compressed concepts: MySQL indexes and records live on disk; disk I/O is far slower than memory access; a good index structure should minimize page reads while supporting both point lookup and range lookup.
- What the learner can do after acquiring it: Explain why `O(log n)` alone is not enough, and judge candidate structures through disk I/O count, tree height, update cost, and range-scan path.
- Common failure signal: Says "B+Tree is O(log n), so MySQL uses it" without mentioning disk pages or range queries.
- Status: Forming; first check passed with precision repair on range-query leaf scanning.

## Binary and Balanced Trees Are Not Enough for Disk Indexes

- Trigger situation: Explaining why MySQL does not mainly use binary search trees, AVL trees, or red-black trees as disk index structures even though balanced binary trees are `O(log n)`.
- Compressed concepts: Binary search trees can degenerate into linked lists; self-balancing binary trees prevent degeneration but still have fanout 2, so large datasets produce taller trees and more disk page reads than high-fanout B/B+ trees.
- What the learner can do after acquiring it: Explain that database index design cares about tree height in pages, not only abstract comparison complexity.
- Common failure signal: Says red-black trees are `O(log n)`, so they should be as suitable as B+Tree for MySQL indexes.
- Status: Forming; first check passed.

## B Tree vs B+Tree Page Efficiency

- Trigger situation: Explaining why MySQL prefers B+Tree even after B Tree has already solved the low-fanout problem of binary trees.
- Compressed concepts: B Tree nodes can contain both index keys and record payload; B+Tree non-leaf nodes contain only index keys and child routing information, so a fixed-size page can hold more directory entries, creating higher fanout, lower height, and fewer page reads.
- What the learner can do after acquiring it: Derive the chain "non-leaf page stores no row payload -> more keys per page -> more children -> lower tree height -> fewer disk I/Os".
- Common failure signal: Says only that B+Tree is multiway, without explaining why its non-leaf pages have higher effective fanout than B Tree pages.
- Status: Forming; first check passed.

## B+Tree Insert Delete Stability

- Trigger situation: Explaining why B+Tree maintenance can be more stable than B Tree maintenance during insert and delete operations.
- Compressed concepts: B+Tree keeps real records in leaf nodes and repeats index keys in non-leaf nodes as redundant routing information; deleting a leaf record often does not require moving real records out of internal nodes, while B Tree may delete data from internal nodes and trigger more complex restructuring.
- What the learner can do after acquiring it: Explain redundant non-leaf indexes as a maintenance tradeoff: extra directory copies can reduce structural disruption for deletes and keep updates focused along one path.
- Common failure signal: Treats B+Tree redundant indexes as only wasted space, or says insert/delete is faster without explaining leaf-only data storage and internal-node routing copies.
- Status: Forming; first check passed.

## Storage Engine Page B+Tree Bridge

- Trigger situation: Preparing to explain why InnoDB indexes reduce disk I/O.
- Compressed concepts: The storage engine owns physical data and indexes; InnoDB reads and writes data mainly by page; a B+Tree organizes key ranges so MySQL can reach the target leaf page through a few page reads.
- What the learner can do after acquiring it: Explain an indexed lookup as "executor asks storage engine; storage engine walks B+Tree pages; leaf page leads to the row or primary key."
- Common failure signal: Treats B+Tree as an in-memory abstract tree only, or thinks querying one row means reading exactly one row from disk.
- Status: Forming.

## Page as I/O and Local Search Unit

- Trigger situation: Explaining `materials/mysql/index/page.md` or any question asking what a B+Tree node really is in InnoDB.
- Compressed concepts: InnoDB reads and writes data by 16KB data pages; each page contains records and page metadata; query execution first finds the relevant page, then searches inside that page.
- What the learner can do after acquiring it: Explain why querying one row can still involve reading a whole page, and why B+Tree index efficiency should be discussed in page reads rather than row reads.
- Common failure signal: Says the database reads only the target row from disk, or treats B+Tree nodes as abstract in-memory nodes instead of page-sized storage units.
- Status: Forming; first check passed.

## Page Directory as In-Page Index

- Trigger situation: Explaining how InnoDB finds a record after the target data page has already been loaded.
- Compressed concepts: User records in a page form a primary-key-ordered single linked list; the page directory groups records into slots pointing to each group's largest record; InnoDB can binary-search slots and then scan a few records inside the group.
- What the learner can do after acquiring it: Distinguish page-level B+Tree navigation from in-page slot search, and explain why page lookup does not require scanning every record in the page.
- Common failure signal: Thinks page directory stores every record address, or thinks page-internal lookup must traverse the full record linked list.
- Status: Forming; first check passed.

## B+Tree as Directory of Data Pages

- Trigger situation: Explaining the query path in `materials/mysql/index/page.md`, especially how InnoDB finds the leaf page containing a target primary key.
- Compressed concepts: InnoDB B+Tree nodes are data pages; non-leaf pages store directory entries that route the search to lower-level pages; leaf pages then use their own page directory to find the record.
- What the learner can do after acquiring it: Explain lookup as root page -> intermediate directory page -> leaf data page -> page-directory slot -> record group.
- Common failure signal: Thinks non-leaf B+Tree nodes directly store full user records, or cannot separate locating the target page from locating the row inside that page.
- Status: Stable for near-transfer; integrated lookup chain passed after boundary repair.

## Index Classification Dimensions

- Trigger situation: Classifying MySQL indexes such as primary key, unique key, clustered index, secondary index, B+Tree index, and composite index.
- Compressed concepts: Classify an index by asking four separate questions: what data structure stores it, whether its leaf stores the full row or a primary-key pointer, what field constraint it enforces, and how many fields are in the key.
- What the learner can do after acquiring it: Explain why one index can be both a B+Tree index, a secondary index, a unique index, and a single-column index.
- Common failure signal: Treats labels from different dimensions as mutually exclusive, or says "unique index" when asked about physical storage.
- Status: Forming; immediate classification succeeded.

## Clustered Secondary Index and Back-to-Table Lookup

- Trigger situation: Explaining why a query using a non-primary index may still need another lookup.
- Compressed concepts: InnoDB clustered index leaf nodes store full rows; secondary index leaf nodes store primary key values; selecting columns not contained in the secondary index requires using the primary key to fetch the full row.
- What the learner can do after acquiring it: Explain why `select * where name = 'Tom'` through `idx_name_age(name, age)` may need back-to-table lookup.
- Common failure signal: Says using any index directly returns the whole row, or cannot explain why `select *` differs from selecting indexed columns only.
- Status: Stable for this chapter; page-oriented check passed.

## Covering Index

- Trigger situation: Explaining when a secondary-index query can avoid back-to-table lookup.
- Compressed concepts: If all columns needed by the query are available in the secondary index leaf record, InnoDB can return the result from that index without fetching the clustered-index row.
- What the learner can do after acquiring it: Decide that `select age from product where name = 'Tom'` can be covered by `idx_name_age(name, age)`.
- Common failure signal: Thinks every secondary-index query must return to the clustered index, or ignores that secondary index records also contain the primary key.
- Status: Forming; first check answered correctly.

## B+Tree vs Hash Order Schema

- Trigger situation: Explaining why InnoDB commonly uses B+Tree indexes rather than Hash indexes.
- Compressed concepts: Hash maps a key to a bucket and is fast for equality lookup, but it does not preserve key order; B+Tree keeps keys ordered, has low height, and supports range scans, ordered traversal, and equality lookup.
- What the learner can do after acquiring it: Answer that Hash is suitable for exact equality lookup like `where id = 5`, while B+Tree is more general because it also supports range and order-based access.
- Common failure signal: Names space cost or collisions as the primary reason while missing ordered access.
- Status: Forming; immediate repair passed.

## B+Tree Low-Height Page I/O Schema

- Trigger situation: Explaining why B+Tree is more suitable than binary trees for database indexes.
- Compressed concepts: B+Tree nodes are page-sized and can hold many keys, giving high fanout, low tree height, and fewer page reads during lookup.
- What the learner can do after acquiring it: Explain that B+Tree reduces disk I/O because one page read can compare many keys and choose among many child pages.
- Common failure signal: Says only "B+Tree is O(log n)" without mentioning page reads, fanout, or tree height.
- Status: Forming; first check answered correctly.

## B+Tree vs B Tree Range Scan Schema

- Trigger situation: Explaining why B+Tree is preferred over B Tree for MySQL range queries.
- Compressed concepts: B+Tree keeps row data in leaf nodes and links leaf nodes in key order, so after locating the start of a range, InnoDB can scan forward through leaf pages.
- What the learner can do after acquiring it: Explain that B+Tree supports range scans well because leaf nodes are ordered and connected.
- Common failure signal: Says only "B+Tree is faster" without mentioning leaf-node links or ordered range traversal.
- Status: Forming; range-query check passed in `why_index_chose_bpuls_tree.md`.

## Composite Index Leftmost Prefix Schema

- Trigger situation: Explaining why a composite index such as `(a, b, c)` can use `a` first but usually cannot directly locate rows by `b` alone.
- Compressed concepts: A composite index is sorted lexicographically: first by `a`, then by `b` within equal `a`, then by `c` within equal `(a, b)`. The later columns are locally ordered only after the left prefix is fixed.
- What the learner can do after acquiring it: Explain why `where b = 2` cannot usually use `(a, b, c)` for efficient positioning, while `where a = 1 and b = 2` can.
- Common failure signal: Memorizes "leftmost prefix" but cannot explain it through global order vs local order.
- Status: Forming; first check answered correctly.

## Composite Index Range Stops Boundary Schema

- Trigger situation: Explaining why `where a > 1 and b = 2` on `(a, b)` mainly uses `a > 1` to form the scan range.
- Compressed concepts: A range predicate on the left column can define a scan interval, but inside that interval the next column is not globally ordered across different left-column values, so later columns usually cannot further narrow the index boundary.
- What the learner can do after acquiring it: Say that `a > 1` forms the scan range and `b = 2` is usually applied as a filter during the scan.
- Common failure signal: Says both `a > 1` and `b = 2` equally shrink the B+Tree search boundary without checking ordering.
- Status: Forming; first check answered correctly.

## Composite Index Inclusive Boundary Schema

- Trigger situation: Explaining why `where a >= 1 and b = 2` on `(a, b)` can use `b` differently from `where a > 1 and b = 2`.
- Compressed concepts: An inclusive boundary such as `a >= 1` includes the fixed `a = 1` boundary group. Within that group, `b` is ordered, so `b = 2` can help position the starting boundary before scanning later `a` values.
- What the learner can do after acquiring it: Explain that `b` helps because the starting boundary includes a local ordered group where `a` is fixed, not because `b` is globally ordered across all `a >= 1`.
- Common failure signal: Overgeneralizes "range stops matching" and misses inclusive-boundary cases, or says `b` is globally ordered after `a >= 1`.
- Status: Forming; first check answered correctly.

## Composite Index BETWEEN Boundary Schema

- Trigger situation: Explaining why `where a between 2 and 8 and b = 2` on `(a, b)` can use `b` for boundary positioning.
- Compressed concepts: In MySQL, `BETWEEN` includes its boundary values. The starting boundary includes the fixed `a = 2` group, where `b` is locally ordered, so `b = 2` can help locate the start of the scan range.
- What the learner can do after acquiring it: Explain `BETWEEN` as an inclusive range that behaves like a boundary case, not as a simple "range always stops all later columns" rule.
- Common failure signal: Treats `BETWEEN` exactly like `>` and says later columns can only filter, without considering the inclusive start boundary.
- Status: Forming; first check answered correctly.

## Prefix LIKE Index Range Schema

- Trigger situation: Explaining why `name like 'j%'` can use an index range while `name like '%j'` usually cannot.
- Compressed concepts: A prefix LIKE pattern has a known starting prefix, so it can be mapped to an ordered range such as `['j', 'k')`. A leading-wildcard pattern has no fixed start, so the B+Tree cannot position at a useful starting point.
- What the learner can do after acquiring it: Explain why `where name like 'j%' and age = 22` can use `(name, age)` better than `where name like '%j' and age = 22`.
- Common failure signal: Says all LIKE queries invalidate indexes, or does not distinguish prefix matching from leading-wildcard matching.
- Status: Forming; first check answered correctly.

## Index Condition Pushdown Schema

- Trigger situation: Explaining what happens to later composite-index conditions that cannot form the scan boundary.
- Compressed concepts: Index condition pushdown lets MySQL evaluate conditions on columns contained in the secondary index during index scanning, so non-matching records can be filtered before clustered-index lookup.
- What the learner can do after acquiring it: Explain that ICP reduces back-to-table lookups, not the initial scan range formed by `a > 1`.
- Common failure signal: Says ICP makes `b = 2` become a boundary condition for `where a > 1 and b = 2`, or says it reduces secondary-index scan count rather than back-to-table count.
- Status: Forming; first check answered correctly.

## Index Selectivity Schema

- Trigger situation: Choosing column order for a composite index from the perspective of filtering power.
- Compressed concepts: Selectivity is roughly distinct values divided by total rows. Higher-selectivity columns filter more rows and are often better candidates for earlier positions in a composite index when query patterns allow it.
- What the learner can do after acquiring it: Explain why `(user_id, status)` is better than `(status, user_id)` from selectivity alone when `user_id` is highly selective and `status` has few values.
- Common failure signal: Puts low-cardinality columns first by default, or treats selectivity as the only rule without considering actual query predicates and ordering.
- Status: Forming; first check answered correctly.

## Composite Index for Filtering and Ordering

- Trigger situation: Designing an index for a query with both `WHERE` and `ORDER BY`, such as `where status = 1 order by create_time`.
- Compressed concepts: A composite index can first group/filter by the equality column and then keep rows ordered by the next column inside that group, reducing extra sorting work.
- What the learner can do after acquiring it: Explain why `(status, create_time)` may be useful even when `status` has low selectivity, because it supports both filtering and ordered retrieval.
- Common failure signal: Chooses index column order by selectivity only and ignores `ORDER BY` or `GROUP BY` requirements.
- Status: Stable for near-transfer; user correctly applied the schema to `(user_id, create_time)`.

## Index Maintenance Cost Schema

- Trigger situation: Deciding whether frequently updated columns should be indexed.
- Compressed concepts: Indexes speed up reads but add write-time maintenance. When an indexed column changes, InnoDB must update the table row and maintain the related B+Tree order, which can add page writes, page splits, and extra time cost.
- What the learner can do after acquiring it: Explain why frequently updated fields are usually poor index candidates unless query benefits clearly outweigh write costs.
- Common failure signal: Mentions only index disk space without explaining B+Tree maintenance during insert, delete, and update.
- Status: Forming; first check partially correct.

## Index Invalidated by Expression Schema

- Trigger situation: Explaining why calculations or functions on an indexed column can make the index hard to use.
- Compressed concepts: A B+Tree index is ordered by the original stored column value. If a predicate applies an expression such as `age + 1 = 20`, MySQL cannot directly seek using the original ordered `age` key in the same way as `age = 19`.
- What the learner can do after acquiring it: Rewrite simple expression predicates into direct column comparisons when possible, such as `age + 1 = 20` to `age = 19`.
- Common failure signal: Says only "calculation invalidates index" without explaining original-key ordering, or cannot rewrite the condition.
- Status: Forming; first check answered correctly.

## EXPLAIN Index Verification Schema

- Trigger situation: Checking whether a SQL query actually uses an index.
- Compressed concepts: `possible_keys` lists candidate indexes; `key` is the index actually chosen; `type`, `rows`, `key_len`, and `Extra` help judge access quality and optimizations such as covering index or index condition pushdown.
- What the learner can do after acquiring it: Use `key` instead of `possible_keys` to identify the actual index used by the execution plan.
- Common failure signal: Treats `possible_keys` as proof that the query used an index.
- Status: Forming; first check answered correctly.

## EXPLAIN Access Type Schema

- Trigger situation: Judging how MySQL scans data from the `type` column in EXPLAIN.
- Compressed concepts: `type` describes the access method. `ALL` means full table scan, `range` means index range scan, `ref` means non-unique index lookup, and `const` is very efficient lookup by primary or unique key with a constant.
- What the learner can do after acquiring it: Explain that `type: ALL` with `key: NULL` usually means MySQL did a full table scan and did not choose an index.
- Common failure signal: Looks only at `possible_keys` and ignores that `type: ALL` plus `key: NULL` means no actual index access.
- Status: Forming; first check answered correctly.

## EXPLAIN Extra Sort Schema

- Trigger situation: Interpreting `Extra` in EXPLAIN for index-related optimizations or costs.
- Compressed concepts: `Using index` usually indicates covering index, `Using index condition` indicates index condition pushdown, and `Using filesort` means MySQL cannot satisfy ordering directly from index order and needs an extra sort step.
- What the learner can do after acquiring it: Explain that `Using filesort` is a warning sign for queries with `ORDER BY`, often addressed by a suitable composite index such as `(status, create_time)`.
- Common failure signal: Treats `Using filesort` as using an index for sorting, or ignores it when optimizing `ORDER BY`.
- Status: Forming; first check answered correctly.

## 把 2000W 看成 B+Tree 页层高经验值

- 触发场景：回答“ MySQL 单表不要超过 2000W 行靠谱吗？”这类问题。
- 压缩概念：2000W 不是 MySQL 硬上限，而是基于 InnoDB 16KB 页、B+Tree 非叶子页扇出 `x`、叶子页行数 `y`、树高 `z` 推出的粗略性能经验值；Buffer Pool、SQL 形态、行大小和硬件都会改变这个阈值。
- 可用能力：能解释 `总行数 ≈ x^(z-1) * y`，能说明 1KB 行可能估出 2000W 量级，而 5KB 行会把阈值压低到约 500W，并且不会只凭行数就判断必须拆表。
- 常见失败信号：把“2000W”背成固定规则，或者只说“B+Tree 三层”却说不清行大小、页容量、缓存和 SQL 对性能的影响。
- 状态：形成中；学习者已通过 `y` 变量解释行变大导致三层 B+Tree 容量阈值下降，也能把慢查询归因转到是否走索引和需要读取多少页；整合回答需要补充“不是硬上限”和“B+Tree 页层高估算来源”。

## 用查询路径诊断 2000W 表慢不慢

- 触发场景：看到一个大表查询慢，需要判断是不是因为“超过 2000W”。
- 压缩概念：行数是风险信号，不是直接根因；真正要看 SQL 是否走有效索引、索引能否缩小扫描范围、一次查询需要读多少索引页和数据页，以及这些页是否命中缓存。
- 可用能力：能区分主键点查和无索引范围扫描：前者可能只访问少量 B+Tree 页，后者可能扫描大量数据页，因此慢的直接原因是查询路径读页太多。
- 常见失败信号：只说“表太大所以慢”，不检查索引、扫描范围、读页数量和缓存命中。
- 状态：形成中；第一轮诊断题回答正确。

## Buffer Pool 与工作集边界

- 触发场景：解释为什么同样是访问几个 B+Tree 页，性能可能差很多。
- 压缩概念：Buffer Pool 缓存 InnoDB 的数据页和索引页；如果查询需要的页已经在内存中，访问成本低；如果页不在缓存中，需要从磁盘加载，磁盘 I/O 会显著拖慢查询。
- 可用能力：能解释大表不一定慢，关键要看查询工作集是否能被 Buffer Pool 覆盖，以及热点索引页/数据页是否命中内存。
- 常见失败信号：只说“树高是 3 层所以最多 3 次 I/O”，但不区分逻辑页访问、Buffer Pool 命中和真实磁盘读取。
- 状态：形成中；第一轮 Buffer Pool 边界题回答正确。

## 用“页”而不是“行”理解 Buffer Pool

- 触发场景：解释为什么查询一条记录也可能加载整个页，或解释 Buffer Pool 到底缓存什么。
- 压缩概念：InnoDB 以页作为磁盘和内存交互单位，默认 16KB；索引先定位到页，Buffer Pool 缓存的是页，页进入内存后再通过页内结构定位具体记录。
- 可用能力：能回答“查一行为什么读一页”，并把 Buffer Pool 命中/未命中、缓存页、数据页/索引页和页内查找串起来。
- 常见失败信号：说 Buffer Pool 缓存单条记录；说索引可以直接定位到磁盘上的某一行；漏掉索引页、undo 页等也可能被缓存。
- 状态：形成中；第一轮检查通过。学习者能说明 InnoDB 和磁盘交互的基本单位是默认 16KB 的页，B+Tree 先定位目标记录所在页，而不是直接定位磁盘上的孤立记录；页加载到 Buffer Pool 后，再通过页内结构找到具体行。

## 用控制块和三条链表管理缓存页

- 触发场景：解释 Buffer Pool 如何快速找到空闲页、脏页和可淘汰页。
- 压缩概念：每个缓存页都有控制块，记录表空间、页号、缓存页地址和链表节点；Free List 管理空闲页，Flush List 管理脏页，LRU List 管理已使用页的冷热淘汰，脏页同时在 LRU 和 Flush 中。
- 可用能力：能把“页状态”和“管理链表”分开，说明 Free/Flush/LRU 分别解决什么管理问题。
- 常见失败信号：把 Free、Flush、LRU 当成互斥分类；说脏页只在 Flush 链表；只背链表名字但说不出各自用途。
- 状态：形成中；第一轮检查通过。学习者能说明 Free List 找空闲缓存页装新磁盘页，Flush List 找脏页方便后台刷盘，LRU List 管理已使用页的冷热淘汰；并能说明脏页不只在 Flush List，因为它也是已使用页，所以仍在 LRU List。精确补充为脏页同时在 LRU List 和 Flush List。

## 用改良 LRU 区分预读失效和 Buffer Pool 污染

- 触发场景：解释为什么 InnoDB 不使用简单 LRU，以及 `innodb_old_blocks_pct`、`innodb_old_blocks_time` 的作用。
- 压缩概念：预读失效是提前读入的页没有被真正访问，解决方式是预读页先进入 old 区域；Buffer Pool 污染是批量扫描页被访问一次后挤掉热点页，解决方式是页必须在 old 区域停留超过 `innodb_old_blocks_time` 后才进入 young 区域。
- 可用能力：能判断一个 SQL 是因为预读无效还是因为大扫描污染缓存，并解释 old/young 区域和时间门槛如何保护热点页。
- 常见失败信号：把预读失效和 Buffer Pool 污染混成同一个问题；认为访问一次就应进入 young 区域；以为结果集小就不会污染 Buffer Pool。
- 状态：形成中；第一轮检查通过。学习者能说明全表扫描即使最终只返回少量行，也会读取并访问很多页；如果这些一次性扫描页立刻进入 young 区域，就会挤掉热点页，造成 Buffer Pool 污染；`innodb_old_blocks_time` 用来提高进入 young 区域的门槛。精确补充为后续访问发生时，如果距离第一次访问已超过阈值，才移动到 young。

## 用刷脏页触发点解释偶发慢 SQL

- 触发场景：解释为什么更新不一定马上写数据页，或为什么慢 SQL 监控里会偶尔出现用时稍长的 SQL。
- 压缩概念：更新先改 Buffer Pool 中的页并标记为脏页，redo log + WAL 保护崩溃恢复；脏页会在 redo log 快满、Buffer Pool 空间不足且要淘汰脏页、MySQL 空闲、正常关闭前等时机刷盘。
- 可用能力：能把脏页延迟落盘、redo log crash-safe、后台刷脏和性能抖动连成机制链。
- 常见失败信号：认为提交时数据页一定落盘；认为 redo log 可以让脏页永远不刷盘；排查偶发慢 SQL 时完全忽略刷脏页压力。
- 状态：形成中；第一轮检查通过。学习者能说明同步随机写数据页成本高，所以 update 先改 Buffer Pool 中的页并标脏，后续批量刷盘；也能说明脏页未刷盘时宕机会丢内存修改，redo log + WAL 先写日志再写数据页，崩溃恢复可用 redo log 重做。

## 用原始有序键判断索引是否能定位

- 触发场景：判断一个 SQL 条件是否会导致索引失效。
- 压缩概念：B+Tree 索引按原始索引值有序；能走索引定位，通常要求查询条件还能基于原始索引值确定一个点或连续范围。左模糊、函数、表达式、隐式转换作用在索引列、联合索引非最左、OR 一边无索引，本质上都可能破坏这种定位能力。
- 可用能力：看到 SQL 后先问“索引列是否保持原始形态、是否有可定位起点、联合索引是否从最左连续匹配、OR 两边是否都有可用索引”，再用 `EXPLAIN` 验证。
- 常见失败信号：只背索引失效清单，或者认为 where 里出现索引列就一定会走索引。
- 状态：形成中；第一课开始。

## 前缀可定位，前导通配不可定位

- 触发场景：判断 `LIKE` 条件是否能使用字符串索引。
- 压缩概念：字符串索引按从左到右的字符顺序排列；`like '林%'` 有固定左前缀，可以定位一段连续范围；`like '%林'` 或 `like '%林%'` 没有固定起点，通常无法利用 B+Tree 快速定位。
- 可用能力：能解释为什么不是所有 `LIKE` 都失效，而是前导通配会破坏起点定位。
- 常见失败信号：说 “LIKE 一定不能用索引”，或只背 `%` 在左边失效但无法解释起点问题。
- 状态：形成中；第一轮检查通过，学习者能解释左前缀可定位和前导通配无固定起点。

## 函数或表达式不要作用在索引列上

- 触发场景：判断 `length(name) = 6`、`id + 1 = 10` 这类条件是否能使用索引。
- 压缩概念：B+Tree 索引保存的是索引列的原始值，而不是函数或表达式计算后的值；如果函数/表达式作用在索引列上，MySQL 很难直接用原始有序键定位。把计算移到常量侧，例如 `id = 10 - 1`，可以保留索引列原始形态。
- 可用能力：能把 `id + 1 = 10` 改写为 `id = 9`，并解释为什么前者通常索引失效、后者可以走索引。
- 常见失败信号：只背“函数导致索引失效”，但分不清函数作用在索引列还是作用在常量参数。
- 状态：形成中；第一轮检查通过，学习者能解释索引保存原始 `id` 值而不是 `id + 1` 计算结果。

## 隐式类型转换要看函数作用在哪一边

- 触发场景：判断字符串索引列和数字参数比较，或数字索引列和字符串参数比较时，索引是否会失效。
- 压缩概念：MySQL 遇到字符串和数字比较时通常把字符串转成数字；如果被转换的是索引列，例如 `CAST(phone AS signed int)`，就相当于函数作用在索引列上，可能索引失效；如果转换发生在常量参数上，例如 `id = CAST('1' AS signed int)`，索引列仍保持原始形态，通常可以走索引。
- 可用能力：能区分 `varchar phone = 1300000001` 与 `int id = '1'` 的差异，不再把“类型不一致”简单等同于“一定索引失效”。
- 常见失败信号：只说隐式类型转换会导致索引失效，但无法说明 CAST 作用在索引列还是常量参数上。
- 状态：形成中；第一轮检查通过，学习者能区分 CAST 作用在索引列和常量参数的差异。

## 联合索引必须从最左连续匹配

- 触发场景：判断 `(a, b, c)` 联合索引能否支持 `where b = 2`、`where a = 1 and b = 2`、`where a = 1 and c = 3` 等条件。
- 压缩概念：联合索引按字典序组织：先按 `a` 全局排序，`a` 相同后才按 `b` 局部排序，`a、b` 都相同后才按 `c` 局部排序。跳过最左列时，后面的列没有全局有序起点；跳过中间列时，连续定位会中断。
- 可用能力：能解释为什么 `where b = 2` 不能直接用 `(a,b,c)` 高效定位，而 `where a = 1 and b = 2` 可以；能区分“定位边界”和“索引下推过滤”。
- 常见失败信号：只背最左匹配，无法解释全局有序和局部有序；或把索引下推误认为跳过中间列后还能完整定位。
- 状态：形成中；第一轮检查通过。学习者已能解释 `where b=2` 缺少最左列导致不能高效定位，也能解释 `where a=1 and b=2` 可以先定 `a` 范围、再用局部有序的 `b` 定位。

## 索引下推减少回表，不改变定位边界

- 触发场景：解释联合索引 `(a,b,c)` 下 `where a = 1 and c = 3` 或 `Extra = Using index condition`。
- 压缩概念：`a=1` 可以形成索引扫描边界；中间列 `b` 缺失会打断从左连续匹配，所以 `c=3` 不能变成完整的定位边界。但因为 `c` 存在于二级索引 `(a,b,c)` 中，MySQL 5.6 之后可以用索引下推在存储引擎扫描索引时先过滤 `c=3`，减少回表次数。
- 可用能力：能区分“用联合索引确定扫描范围”和“在索引扫描过程中提前过滤，减少回表”。
- 常见失败信号：说索引下推让 MySQL 跳过 `b` 后还能完整使用 `c` 定位，或者说它减少的是二级索引扫描边界而不是回表次数。
- 状态：形成中；第一轮检查通过，学习者能说明 `b` 缺失导致 `c` 不能完整定位，并能说明索引下推减少的是回表次数。

## OR 两边都要有可用索引

- 触发场景：判断 `where id = 1 or age = 18` 这类 OR 条件是否会导致索引失效。
- 压缩概念：`OR` 表示任一条件满足即可；如果一边没有可用索引，MySQL 仍需要找出这一路满足条件的记录，可能选择全表扫描。若两边都有可用索引，优化器可能使用 `index merge` 分别扫描两个索引，再合并结果集。
- 可用能力：能解释为什么 OR 中只有一个条件有索引通常不够，并能把 `index merge` 理解为多个单列索引结果的合并，而不是联合索引。
- 常见失败信号：认为 OR 里只要有一个条件有索引就一定走索引，或把 `index merge` 误认为完整使用了一个联合索引。
- 状态：形成中；第一轮检查通过，学习者能说明无索引的 OR 分支仍需要找出满足条件的行，且 `index merge` 是分别扫描两边索引后合并结果。

## 用非 NULL 表达式理解 COUNT

- 触发场景：回答 `count(*)`、`count(1)`、`count(字段)` 到底统计什么，以及它们为什么性能不同。
- 压缩概念：`count(expr)` 统计的是符合查询条件的记录中 `expr` 不为 `NULL` 的数量；`count(字段)` 会跳过字段为 `NULL` 的行，`count(1)` 和 `count(*)` 都可以理解为用非 `NULL` 常量统计可见行数。
- 可用能力：能先判断 count 的语义是否是总行数，再解释 Server 层计数、InnoDB 扫描可见记录、优化器选择较小二级索引这些执行细节。
- 常见失败信号：把 `count(*)` 当成 `select *`，或用 `count(nullable_col)` 统计总行数，或只背 `count(1)` 比 `count(*)` 快。
- 状态：形成中；第一轮检查通过，学习者能说明 `count(name)` 只统计 `name` 非 `NULL` 的行，而 `count(1)` 会把 `name` 为 `NULL` 的行也计入总行数。

## 用扫描对象解释 COUNT 性能差异

- 触发场景：比较 `count(*)`、`count(1)`、`count(主键)`、`count(字段)` 的执行效率。
- 压缩概念：InnoDB 做精确计数时，Server 层维护计数变量，InnoDB 返回符合条件且当前事务可见的记录或索引记录；优化器倾向扫描更小的索引树，通常二级索引比聚簇索引更轻。
- 可用能力：能解释为什么 `count(*)` 和 `count(1)` 在 InnoDB 中基本无性能差异，为什么它们可能扫描 `key_len` 更小的二级索引，以及为什么 `count(字段)` 可能更慢且语义不同。
- 常见失败信号：把 `count(*)` 当作读取所有字段，或认为有二级索引就不用扫描任何记录，或忽略 `count(字段)` 必须判断字段是否为 `NULL`。
- 状态：形成中；第一轮检查通过，学习者能说明优化器更可能扫描较小的二级索引，因为读取页更少、I/O 成本更低。

## 用 MVCC 解释 InnoDB 不能只存一个准确总行数

- 触发场景：回答为什么 InnoDB 的无条件 `count(*)` 通常仍要扫描，而 MyISAM 可以直接读元信息。
- 压缩概念：MyISAM 可以用表级锁维护一个全局 `row_count`；InnoDB 支持事务和 MVCC，不同事务在同一时刻可能看到不同版本集合，所以“准确行数”取决于当前事务视图，不能只靠一个全局计数值。
- 可用能力：能用并发事务例子解释两个会话同一时刻执行 `count(*)` 可能得到不同结果，并说明带 `where` 条件后 MyISAM 也需要扫描判断。
- 常见失败信号：只说“InnoDB 没保存行数”但说不出 MVCC 可见性，或误以为 MyISAM 在所有 `count()` 场景都能 O(1)。
- 状态：形成中；精度修复通过，学习者能说出 InnoDB 可以对当前事务视图给出准确 `count(*)`，只是不能靠一个全局 `row_count` 回答所有事务。

## 大表 COUNT 优化先判断精确还是近似

- 触发场景：大表频繁执行 `count(*)` 很慢，需要选择优化方案。
- 压缩概念：优化 `count(*)` 的第一问是业务要精确值还是近似值；近似场景可用 `show table status` 或 `explain` 的估算行数，精确场景可维护额外计数表，但要承担写入维护和一致性成本。
- 可用能力：能区分搜索结果数量这类可近似场景和订单/库存/计费这类必须精确场景，并说明为什么单纯把 `count(*)` 改成 `count(1)` 不能解决大表精确扫描成本。
- 常见失败信号：看到大表 `count(*)` 慢就只改 SQL 写法，或在必须精确的业务里使用估算值，或维护计数表却忽略增删事务一致性。
- 状态：形成中；第一轮检查通过，学习者能区分搜索页适合估算值、订单后台适合精确维护；综合口述仍需补上估算值和额外计数表两种业务优化方案。

## 用 trx_id 和 roll_pointer 构造版本链

- 触发场景：准备解释 MVCC、Read View、可重复读和读提交如何读取不同版本记录。
- 压缩概念：InnoDB 聚簇索引记录用 `trx_id` 标记最后修改该记录的事务 id，用 `roll_pointer` 指向 undo log 中的旧版本；多次修改会把一行串成版本链，MVCC 读取时沿版本链寻找对当前 Read View 可见的版本。
- 可用能力：能解释 `trx_id` 是“谁改的”，`roll_pointer` 是“旧版本在哪里”，并能说明 undo log 不只用于回滚，也用于快照读找旧版本。
- 常见失败信号：只背隐藏字段名称，无法说出 `trx_id` 和 `roll_pointer` 在版本可见性判断中的作用，或认为 MVCC 会复制整张表快照。
- 状态：形成中；第一轮检查通过。学习者能说明 `trx_id` 记录生成或最后修改该记录版本的事务 id，`roll_pointer` 沿 undo log 追溯旧版本，直到找到 Read View 可见版本。

## 用 Read View 四字段判断版本可见性

- 触发场景：判断一条记录版本是否对当前事务可见，尤其是 RC/RR 快照读为什么可能读到不同版本。
- 压缩概念：Read View 记录创建时的活跃事务范围：`m_ids` 是活跃事务 id 列表，`min_trx_id` 是其中最小值，`max_trx_id` 是下一个将分配的事务 id，`creator_trx_id` 是创建该 Read View 的事务 id。读取版本时用记录 `trx_id` 和这些边界判断可见性。
- 可用能力：能解释 `trx_id < min_trx_id` 可见、`trx_id >= max_trx_id` 不可见、中间区间要看是否在 `m_ids`，并记住当前事务自己的修改总是可见。
- 常见失败信号：把 `max_trx_id` 当成活跃事务最大 id，或忘记中间区间必须判断 `m_ids`，或只背字段名不能做可见性判断。
- 状态：稳定。学习者在原 MVCC 学习与本轮 MCQ 复习中都能判断 `trx_id=51` 处于 `[min_trx_id=51, max_trx_id=53)` 且在 `m_ids=[51,52]` 中，因此该版本不可见；后续只需穿插复习，不再作为优先修复点。

## 用 Read View 创建时机区分 RC 和 RR

- 触发场景：解释为什么读提交会出现不可重复读，而可重复读的普通 `select` 在事务内能保持一致。
- 压缩概念：RC 和 RR 都靠 Read View 做快照读，但 RC 每个查询语句都会新建 Read View，能看到其他事务已经提交的新版本；RR 在事务期间复用事务启动时的 Read View，因此普通读持续看到同一个快照。
- 可用能力：能用“事务 A 提交后，事务 B 在 RC 下重新拍照看到 200 万；RR 下继续用旧照片看到 100 万”解释两者差异。
- 常见失败信号：说 RR 每次读都创建 Read View，或说 RC 能读到未提交数据。
- 状态：形成中；第一轮检查通过。学习者能说明 RC 每次普通读新建 Read View，事务 A 提交后的新版本可能变得可见；RR 复用旧 Read View，所以事务 B 的普通读仍可能看到旧版本 100 万。

## 区分快照读和当前读的幻读处理方式

- 触发场景：解释为什么 InnoDB 在可重复读下能很大程度避免幻读，以及普通 `select` 和 `select ... for update` 为什么处理方式不同。
- 压缩概念：普通 `select` 是快照读，依赖 MVCC 和 Read View 读取事务开始时的一致快照；`select ... for update` 是当前读，要读取最新版本并准备加锁/修改，因此通过 next-key lock（记录锁 + 间隙锁）阻止锁范围内的新插入。
- 可用能力：能判断一个查询主要依赖 MVCC 还是锁，并能解释“普通读不加锁但看不到新插入；当前读要看最新，所以需要锁住范围”。
- 常见失败信号：说 MVCC 解决所有幻读，或说当前读也使用同一个旧 Read View 来避免幻读，或把 next-key lock 只理解成锁已经存在的记录。
- 状态：形成中；第二轮检查通过。学习者能说明普通 `select` 靠旧快照避免看到新插入，`select ... for update` 靠 next-key lock 锁住范围和间隙，阻止别人插入新的满足条件的行。

## 用当前读和自写可见解释先看不见再看得见

- 触发场景：解释 RR 下事务 A 第一次普通查询看不到 `id = 5`，事务 B 插入并提交后，事务 A 又 `update id = 5`，再普通查询却能看到该行的特殊幻读场景。
- 压缩概念：第一次普通 `select` 是快照读，只看旧 Read View；后续 `update` 是当前读，会读取最新已提交版本并修改它；被事务 A 修改后的记录成为 A 自己的版本，当前事务自己的修改对自己可见。
- 可用能力：能解释“看不见不等于 update 碰不到”，并能把原因拆成当前读读取最新版本、写操作改变记录 `trx_id`、当前事务自己的修改可见。
- 常见失败信号：认为普通读看不到的行，`update` 也一定匹配不到；或说这是 Read View 自动刷新导致的。
- 状态：形成中；第一轮检查通过，需保留精确锚点。学习者能说明 `update` 是当前读、能读取最新提交数据；需要稳定补上“更新后成为当前事务自己的修改，所以后续普通读可见，不是 Read View 刷新”。

## 用锁的时间点解释先快照后当前读的幻读

- 触发场景：解释 RR 下事务 A 先普通查 `id > 100` 得到 3 行，事务 B 插入 `id = 200` 并提交，事务 A 再 `select ... where id > 100 for update` 得到 4 行的特殊幻读场景。
- 压缩概念：第一次普通 `select` 是快照读，没有加锁保护范围；事务 B 可以在这个范围插入并提交；后续 `for update` 是当前读，会读取最新结果集，next-key lock 只能阻止之后的新插入，不能回溯阻止已经提交的新行。
- 可用能力：能解释“锁加晚了”为什么会导致先快照后当前读看到不同结果集，并能区分“之后阻止插入”和“之前已经提交的行会被当前读看到”。
- 常见失败信号：以为 RR 下后续任何读都必须和第一次普通读一致，或认为后来的 next-key lock 可以让已经提交的新行消失。
- 状态：形成中；第一轮检查通过。学习者能说明 next-key lock 只能阻塞后续插入，不能阻塞当前读之前已经完成并提交的事务。

## 串联 RR 幻读从定义到残留场景

- 触发场景：面试或复习中回答“MySQL InnoDB 可重复读是否完全解决幻读”。
- 压缩概念：幻读是同一查询条件下结果集变化；RR 下普通快照读靠 MVCC + Read View 大程度避免幻读，当前读靠 next-key lock 锁范围；但如果事务先快照读后当前读，或者先看不见别人插入的行再用当前读更新它，就可能因为读语义切换或自写可见而看到结果集变化。
- 可用能力：能用 6-8 句话说明“不完全解决，只是大程度避免”，并能分别解释两个特殊场景的原因。
- 常见失败信号：只回答“RR 解决幻读”，或只背 MVCC/next-key lock 名词，不能解释两个特殊场景。
- 状态：形成中；准备综合口述。

## 串联 MVCC 从并发问题到读取机制

- 触发场景：面试或复习中需要完整回答“MVCC 是什么、怎么实现 RR/RC、为什么能避免不可重复读和大程度避免幻读”。
- 压缩概念：从并发异常出发，读提交/可重复读用 Read View + 版本链做快照读；记录版本由 `trx_id` 标识来源、`roll_pointer` 连接 undo 旧版本；RC 每次读新建 Read View，RR 事务内复用 Read View；普通 `select` 是快照读，`select ... for update` 是当前读并依赖 next-key lock。
- 可用能力：能用一段 6-8 句的口述，把事务隔离级别、Read View、版本链、可见性规则和快照读/当前读边界串成完整解释。
- 常见失败信号：只背单点定义，无法从“读到哪个版本”推导到 RC/RR 差异，或把快照读和当前读混成同一种机制。
- 状态：形成中；综合口述开始。

## 用结果集变化识别幻读

- 触发场景：判断一个并发现象到底是不可重复读还是幻读，尤其是同一个事务内两次执行同一范围查询时。
- 压缩概念：幻读关注的是同一查询条件返回的行集合是否变化，而不是某一行字段值是否变化；结果集变多或变少都说明集合发生变化。
- 可用能力：能用“同一查询条件 + 同一事务内两次执行 + 返回行集合不同”识别幻读，并把它和同一行内容变化的不可重复读区分开。
- 常见失败信号：只把幻读理解成多出一行，或把一行字段值变化也直接叫幻读。
- 状态：形成中；本章边界复查通过。学习者能说明幻读关注满足查询条件的行集合变化，可以变多也可以变少；不可重复读更偏向同一行还在但字段值变化。

## 用 Record + Gap 同时防止删除和插入导致的幻读

- 触发场景：解释 `select ... where age > 20 for update` 后，为什么其他事务删除已命中的行会被阻塞，以及为什么插入新的满足条件行也会被阻塞。
- 压缩概念：当前读用 next-key lock 保护结果集稳定。next-key lock 的 Record 部分保护已有匹配记录，阻止其他事务删除或更新它们；Gap 部分保护索引间隙，阻止其他事务插入新的满足条件记录。删除导致结果集变少、插入导致结果集变多，本质上都属于结果集变化。
- 可用能力：能说明“记录锁 + 间隙锁可以防止删除导致的幻读”：不是靠 Gap Lock 单独防删除，而是靠 Record 部分锁住已有匹配行；同时能根据是否走 `age` 索引判断锁范围是近似全表还是二级索引范围 + 主键记录。
- 常见失败信号：只把幻读理解成新增行；说 Gap Lock 防删除；漏掉二级索引命中后还要锁匹配行主键记录；把 `LOCK_TYPE=RECORD` 误读为 Record Lock。
- 状态：形成中；`data_locks` 字段边界部分通过。学习者能说明 `LOCK_TYPE=RECORD` 不等于 Record Lock，具体锁种要看 `LOCK_MODE`；还需补全 `LOCK_MODE = X`、`X, REC_NOT_GAP`、`X, GAP` 分别对应 next-key lock、Record Lock、Gap Lock。

## 按加锁范围判断 MySQL 锁的保护边界

- 触发场景：回答“MySQL 有哪些锁”或分析一个 SQL/DDL/备份操作为什么阻塞。
- 压缩概念：先按加锁范围判断全局锁、表级锁、行级锁，再追问它保护的对象、阻塞的操作、兼容关系和释放时机。全局锁保护整个库，表级锁保护表或表级元信息，行级锁保护记录或索引区间。
- 可用能力：能把 `flush tables with read lock`、表锁、MDL、意向锁、AUTO-INC 锁、Record Lock、Gap Lock、Next-Key Lock 放进同一个“保护范围”框架，而不是背散乱名词。
- 常见失败信号：只罗列锁名；把普通 `select` 说成完全无锁而漏掉 MDL；把意向锁和插入意向锁混为一谈；把 Gap Lock 说成锁住已有记录。
- 状态：形成中；第一轮检查通过。学习者能按范围区分全局锁、表级锁、行级锁，并能把 next-key lock 放在行级锁细粒度分析中；需稳定补上“分类维度是加锁范围，主要回答保护谁/阻塞谁”。

## 用一致性备份选择全局锁或事务快照

- 触发场景：解释全库逻辑备份为什么可能需要全局锁，以及 InnoDB 为什么可以用 `mysqldump --single-transaction` 降低锁影响。
- 压缩概念：备份需要的是同一时刻的数据切面。全局锁通过 `flush tables with read lock` 让整个库只读，阻塞数据修改和表结构变更；InnoDB 可以用 RR 事务快照和 MVCC 让备份期间继续写入，但备份事务仍看到同一个 Read View。
- 可用能力：能解释“先备份用户表、期间发生下单、再备份商品表”为什么会产生跨表不一致，并能说明不支持事务的 MyISAM 更依赖全局锁。
- 常见失败信号：只说全局锁用于备份但不说备份一致性；认为 `--single-transaction` 适用于所有存储引擎；忽略全局锁会造成写业务停滞。
- 状态：形成中；第一轮检查通过。学习者经修复后能说出跨表不一致结果：用户余额未扣，商品库存已减少；也能说明 `--single-transaction` 依赖 InnoDB 的事务、RR Read View 和 MVCC，MyISAM 不支持事务/MVCC 快照所以不适合。

## 用 MDL 解释 DDL 为什么会卡住后续查询

- 触发场景：分析 `alter table` 被长事务卡住，或者一次 DDL 让后续查询大量阻塞。
- 压缩概念：CRUD 自动申请 MDL 读锁，DDL 自动申请 MDL 写锁；MDL 在事务提交后才释放。长事务持有 MDL 读锁时，DDL 的 MDL 写锁会等待；一旦写锁在队列中等待，由于写锁优先级高，后续新的 CRUD 读锁也会排队。
- 可用能力：能解释“长事务 select 不提交 -> alter table 等写锁 -> 后续 select 也被堵住”的阻塞链，并知道 DDL 前要检查或处理长事务。
- 常见失败信号：说普通 `select` 完全无锁；只解释 `alter table` 被阻塞，漏掉后续查询也被写锁等待队列影响；以为 MDL 在单条 `select` 结束后一定释放而忽略显式事务。
- 状态：形成中；第一轮检查通过。学习者能说明长事务持有 MDL 读锁会阻塞 DDL 的 MDL 写锁，并能说明写锁等待后会因写锁优先级高而阻塞后续新的 `select`。

## 用意向锁连接表锁和行锁

- 触发场景：解释 InnoDB 为什么在给记录加 S/X 行锁之前，还要先在表级加意向共享锁或意向独占锁。
- 压缩概念：意向锁是表级锁，作用像表级提示牌：在记录加 S/X 锁前，先在表上标记“表内可能已有行锁”。这样其他事务申请表锁时，不需要遍历所有记录检查行锁，只要检查表级意向锁即可。
- 可用能力：能解释 `select ... for update` 会先加意向独占锁，再加记录独占锁；能说清意向锁之间不冲突，主要和显式表锁冲突。
- 常见失败信号：把意向锁和插入意向锁混为一谈；认为意向锁直接锁住某一行；认为意向锁会和普通行级 S/X 锁冲突。
- 状态：形成中；第一轮检查通过。学习者能说明没有意向锁时申请表锁可能需要检查很多记录；有了意向锁后，可以通过表级意向锁快速判断表里可能已有行锁。

## 用自增锁模式权衡连续性、并发和复制一致性

- 触发场景：解释 AUTO-INC 锁、`innodb_autoinc_lock_mode`，以及自增主键在并发插入和主从复制中的一致性问题。
- 压缩概念：AUTO-INC 锁是特殊表锁，用于给 `AUTO_INCREMENT` 字段分配递增值。传统 AUTO-INC 锁要等插入语句结束才释放，保证语句内自增值连续但会阻塞其他插入；轻量级锁申请到自增值后即可释放，提高并发，但在 statement binlog 下主库并发执行、从库顺序重放，可能让自增 id 分配结果不同。
- 可用能力：能解释 `innodb_autoinc_lock_mode = 0/1/2` 的释放时机差异，并能说明 `mode = 2` 搭配 `binlog_format = row` 才能兼顾并发和复制一致性。
- 常见失败信号：只背 mode 数字；只说 `mode = 2` 性能最好但漏掉 statement binlog 的主从不一致；把 AUTO-INC 锁误认为事务提交后才释放。
- 状态：形成中；第一轮检查通过。学习者能解释 `mode = 2` 申请到自增值后释放轻量锁、并发最好，但在 statement binlog 下从库顺序重放 SQL 不能复现主库并发交错，row binlog 记录实际行结果所以可以接受。

## 区分 statement binlog 和 row binlog 重放对象

- 触发场景：解释主从复制时为什么并发执行、非确定性函数、自增 id 分配等场景在 statement 格式下可能和主库结果不同。
- 压缩概念：statement binlog 记录“执行了什么 SQL”，从库按日志顺序重新执行这些 SQL；row binlog 记录“每一行实际变成了什么值”，从库按主库已经产生的行结果落数据。statement 重放的是语句，row 重放的是结果。
- 可用能力：能解释主库并发插入时产生的不连续自增 id，为什么从库顺序执行原 SQL 不一定复现；也能解释 row 格式为什么能保留主库实际分配的自增值。
- 常见失败信号：以为 statement binlog 会记录主库每一行最终 id；以为从库顺序执行 SQL 时会自动复现主库并发交错；不知道 row binlog 记录的是实际行变化。
- 状态：形成中；缺前置概念已修复一次。学习者能说出 statement binlog 重放原 SQL，row binlog 多记录主库最终改动的行和实际值；安排 2026-07-10 复习。

## 用 Record/Gap/Next-Key 区分锁住记录、间隙、范围加端点

- 触发场景：分析 InnoDB 行级锁到底阻止别人改已有记录、插入间隙，还是同时锁住范围端点。
- 压缩概念：Record Lock 锁已有记录本身；Gap Lock 锁两个索引记录之间的间隙，不包含端点记录；Next-Key Lock = Record Lock + Gap Lock，锁一个左开右闭范围，同时覆盖间隙和右端记录。
- 可用能力：看到 `(3,5)`、`id=5`、`(3,5]` 这类描述时，能判断它主要阻止的是插入新记录、修改已有记录，还是两者都阻止。
- 常见失败信号：把 Gap Lock 说成锁住已有记录；把 Next-Key Lock 说成只有间隙锁；不知道相同范围的 Gap Lock 之间兼容。
- 状态：形成中；第一轮检查通过。学习者能正确区分 `Record Lock(id=5)` 锁已有记录、`Gap Lock(3,5)` 锁间隙并阻止插入 `id=4`、`Next-Key Lock(3,5]` 同时锁间隙和右端点记录。

## 用插入意向锁解释 insert 为什么等待

- 触发场景：分析一个事务插入新记录时，插入位置落在另一个事务持有的 gap lock 或 next-key lock 范围内，为什么 insert 会等待。
- 压缩概念：插入意向锁是插入操作遇到已有间隙锁时生成的等待锁，表示“我要在这个间隙里的某个点插入”。它名字里有“意向锁”，但不是表级意向锁，而是行级的特殊间隙锁；锁结构可先生成，但等待状态不代表已经成功获取锁。
- 可用能力：能解释事务 A 持有 `(3,5)` 间隙锁时，事务 B 插入 `id=4` 会生成等待状态的插入意向锁，并阻塞到 A 提交释放间隙锁。
- 常见失败信号：把插入意向锁当成前面的表级意向锁；认为等待状态就是已经获得锁；认为只要是 insert 就一定不会被 gap lock 阻塞。
- 状态：形成中；修复检查通过。学习者能说出插入意向锁作为等待锁结构会记录“事务 B 在等”和“要插入的位置/间隙”，并能把它和阻塞原因 gap lock 区分开。

## 用执行计划判断 UPDATE 的锁范围和事故半径

- 触发场景：线上执行 `update`、`delete` 或锁定读前，需要判断它是否可能因为没有走有效索引而大面积阻塞业务。
- 压缩概念：`update` 是当前读写操作，会加 X 锁并通常持有到事务结束；InnoDB 行锁锁的是实际扫描到的索引项和间隙。真正决定锁范围的是优化器最终选择的执行计划，而不是 `where` 文本本身。如果走全表扫描，就会对大量索引项加 next-key lock，效果接近锁住整张表，但这不是直接加表锁。
- 可用能力：能解释为什么 `update ... where age = 20` 在 `age` 无索引时不只是慢，还会扩大锁范围；能说明上线前要用 `EXPLAIN` 验证实际计划，并用 `sql_safe_updates`、补索引、改 SQL 或必要时 `force index` 降低事故风险。
- 常见失败信号：说“没索引就是加表锁”；认为 `where` 中出现索引列就一定安全；只看 `possible_keys` 不看实际 `key`/扫描方式；忘记锁直到事务结束才释放。
- 状态：形成中；第四轮检查通过，等待综合口述。学习者能说明 `age` 无索引时可能全表扫描，扫描到的索引项被 next-key lock 覆盖，效果接近锁全表；也能说明即使 `age` 有索引，只要优化器最终仍走全表扫描，大范围加锁风险仍在；能区分 `sql_safe_updates` 是基础防线，不能替代 `EXPLAIN`；能说明 `force index` 的用途和使用前验证条件。需稳定补上“不是直接加表锁”和“锁通常到事务结束释放”。

## 用索引扫描路径推导 InnoDB 行锁范围

- 触发场景：分析 `select ... for update`、`lock in share mode`、`update`、`delete` 在可重复读下会锁哪些记录或间隙。
- 压缩概念：InnoDB 行锁锁的是索引扫描路径；默认基本单位是 next-key lock，再按“能否用更小锁避免幻读”退化成记录锁或间隙锁。唯一索引等值存在可退化成记录锁，不存在可退化成间隙锁；非唯一索引需要继续扫描到第一个不匹配项，并对匹配行的主键索引加记录锁。
- 可用能力：能先判断 SQL 是否加锁、走哪棵索引、扫描到哪些索引边界，再推导 Record Lock、Gap Lock、Next-Key Lock 的具体范围。
- 常见失败信号：只背锁名；把锁直接加在 SQL 条件上；认为不存在的记录也能加记录锁；非唯一索引等值命中后漏掉后方间隙和主键记录锁。
- 状态：形成中；第一轮检查通过。学习者能说出唯一索引等值查询未命中时加 Gap Lock，范围是 `(1,5)`，核心目的是阻止别人插入 `id=2`；已补充精确点：锁挂在主键索引右边界记录 `id=5` 上，但不锁 `id=5` 记录本身。

## 用唯一索引等值命中判断记录锁退化

- 触发场景：分析唯一索引等值查询命中已有记录时，`select ... for update` 会从 next-key lock 退化成什么锁。
- 压缩概念：唯一索引能保证等值条件最多命中一条记录；当目标记录存在时，只要防止该记录被其他事务更新或删除即可。插入同一个唯一值会被唯一约束拒绝，所以不需要额外锁住前面的间隙。
- 可用能力：能解释 `where id = 5 for update` 且 `id=5` 存在时，在主键索引 `id=5` 上加 Record Lock，而不是 `(1,5]` Next-Key Lock。
- 常见失败信号：看到 `for update` 就一律回答 Next-Key Lock；认为为了防幻读必须锁住 `id=5` 前面的间隙；忽略唯一约束本身已经阻止重复插入。
- 状态：形成中；第一轮检查通过。学习者能说明 `id=5` 存在时加主键索引上的 Record Lock，阻止别人更新或删除 `id=5`，不需要锁 `(1,5)` 间隙，因为插入 `id=2/3/4` 不影响 `where id=5` 的结果。

## 用唯一索引范围下界区分大于和大于等于

- 触发场景：分析唯一索引范围查询的下界，例如 `id > 15 for update` 和 `id >= 15 for update`。
- 压缩概念：唯一索引范围查询会沿索引继续扫描。对于 `id > 15`，第一条扫描到的 `id=20` 不是等值命中，通常加 `(15,20]` Next-Key Lock；对于 `id >= 15` 且 `id=15` 存在，第一步包含等值命中，`id=15` 上的 next-key lock 可退化成 Record Lock，后续扫描到的记录仍按范围加 next-key lock。
- 可用能力：能解释为什么 `>=` 命中已有唯一索引边界时，边界记录可以只加记录锁，而 `>` 的第一条结果需要保护前面的间隙。
- 常见失败信号：把 `>` 和 `>=` 的加锁完全说成一样；只看返回结果，不看第一条扫描记录是否等值命中边界。
- 状态：形成中；`id >= 15` 和 `id > 15` 的下界对比检查通过。学习者能说明 `>=15` 扫描到存在的 `id=15` 时像等值命中，只需记录锁；`>15` 扫描到 `id=20` 时需要 `(15,20]` Next-Key Lock，因为要阻止插入 `id=16/17/18/19` 造成幻读。

## 用 supremum 处理唯一索引范围查询的最后开区间

- 触发场景：分析唯一索引范围查询扫到当前最大记录后，为什么还会锁住最大记录之后的范围，例如 `id > 15 for update` 在最大记录为 `id=20` 时。
- 压缩概念：范围查询不是扫到最后一条真实记录就结束保护；InnoDB 用 supremum pseudo-record 表示索引末尾。为了防止以后插入更大的新记录也落入 `id > 15` 的结果集，需要在 supremum 上加 `(20,+∞]` Next-Key Lock，实际效果是阻止插入大于 20 的新记录。
- 可用能力：能解释 `id > 15 for update` 不只锁 `(15,20]`，还要锁 `(20,+∞]`，因为 `id=21/22/...` 也满足查询条件。
- 常见失败信号：只锁到当前最大记录 `id=20`，忘记后续新插入的更大值也会成为幻读；不知道 supremum 是索引末尾的特殊伪记录。
- 状态：形成中；第一轮检查通过。学习者能说明 `(20,+∞]` 主要阻止 `id > 20` 的数据插入；已补充精确点：这些新插入行也满足 `id > 15`，会造成范围当前读的幻读。

## 用唯一索引小于上界判断终止边界退化

- 触发场景：分析唯一索引范围查询的上界，例如 `id < 5 for update`，且边界记录 `id=5` 存在。
- 压缩概念：`id < 5` 扫描到 `id=5` 时，`id=5` 是第一条不满足条件的终止边界记录，不属于结果集；为了防止新插入 `id=2/3/4` 造成幻读，只需要锁住 `(1,5)` 间隙，不需要锁住 `id=5` 记录本身，所以终止边界上的 next-key lock 可退化为 Gap Lock。
- 可用能力：能解释 `id < 5 for update` 为什么在 `id=5` 这个终止边界上锁 `(1,5)` Gap Lock，而不是 `(1,5]` Next-Key Lock。
- 常见失败信号：只因为扫描到 `id=5` 就认为必须锁住 `id=5` 记录；把 `< 5` 和 `<= 5` 的边界处理混为一谈。
- 状态：形成中；第一轮检查通过。学习者能说明 `id < 5` 时 `id=5` 不属于结果集，不需要锁住 `id=5` 本身；真正要防的是插入 `id=2/3/4`，所以终止边界退化为 `(1,5)` Gap Lock。

## 用唯一索引小于等于上界判断端点是否包含

- 触发场景：分析唯一索引范围查询的上界，例如 `id <= 5 for update`，且边界记录 `id=5` 存在。
- 压缩概念：`id <= 5` 包含右端点 `id=5`，所以扫描到 `id=5` 时它是结果集的一部分；为了防止 `id=5` 被更新或删除，同时防止插入 `id=2/3/4` 造成幻读，需要保留 `(1,5]` Next-Key Lock，而不能退化成只锁间隙的 Gap Lock。
- 可用能力：能对比 `id < 5` 与 `id <= 5`：前者不包含 `id=5`，边界可退化为 `(1,5)` Gap Lock；后者包含 `id=5`，需要 `(1,5]` Next-Key Lock。
- 常见失败信号：认为 `<` 和 `<=` 只差返回结果，不影响锁；或者只锁间隙，忘记 `<=5` 还要保护 `id=5` 记录本身。
- 状态：形成中；第一轮检查通过。学习者能说明 `(1,5]` Next-Key Lock 的间隙部分阻止插入 `id=2/3/4`，记录部分阻止更新或删除 `id=5`。

## 用非唯一索引等值未命中判断二级索引间隙锁

- 触发场景：分析非唯一二级索引等值查询未命中时，例如 `age = 25 for update`，且二级索引中没有 `age=25`。
- 压缩概念：非唯一索引等值未命中时，InnoDB 在二级索引树中定位到第一条不符合条件的右边界记录，并在该二级索引记录上把 next-key lock 退化成 Gap Lock；因为没有满足条件的行，所以不会对主键索引加记录锁。
- 可用能力：能解释 `age=25` 不存在、相邻二级索引值是 `22` 和 `39` 时，在二级索引上锁 `(22,39)` Gap Lock，用来阻止插入 `age=23..38`，尤其是 `age=25`。
- 常见失败信号：把二级索引未命中当成主键索引未命中；忘记锁加在 `index_age` 上；认为没有查询结果就不加锁；给不存在的匹配行加主键记录锁。
- 状态：形成中；第一轮检查通过但已补精确点。学习者能说明 `age=25` 不存在，无法加 Record Lock，需要 `(22,39)` Gap Lock；已补充这把锁在二级索引 `index_age` 上，且没有匹配行所以不需要主键索引 Record Lock。

## 用非唯一索引等值命中理解二级索引扫描到不匹配项

- 触发场景：分析非唯一二级索引等值查询命中已有记录时，例如 `age = 22 for update`。
- 压缩概念：非唯一索引允许多个相同值，所以等值命中后不能像唯一索引一样立刻停止。InnoDB 会在二级索引上扫描匹配值，并继续扫到第一条不匹配的二级索引记录；匹配的二级索引记录加 next-key lock，匹配行对应的主键索引加 Record Lock，第一条不匹配记录上退化成 Gap Lock，防止继续插入同值新行造成幻读。
- 可用能力：能解释 `age=22` 存在且下一条不匹配为 `age=39` 时，需要二级索引上的 `(21,22]` Next-Key Lock、主键索引对应行的 Record Lock，以及二级索引上的 `(22,39)` Gap Lock。
- 常见失败信号：认为非唯一索引等值命中后只锁匹配记录；漏掉主键索引记录锁；漏掉后方第一个不匹配项上的 Gap Lock。
- 状态：稳定。学习者能说明只锁 `(21,22]` 不够，因为可能插入新的 `age=22,id=12` 造成幻读；能说明要加 `(22,39)` Gap Lock；也能说明匹配行 `id=10` 还要在主键索引上加 Record Lock。本轮 MCQ 复习再次选择完整锁集合。

## 用二级索引值加主键值判断边界插入是否阻塞

- 触发场景：判断已有二级索引 Gap Lock 时，插入边界值如 `age=22` 或 `age=39` 的新记录是否会被阻塞。
- 压缩概念：InnoDB 二级索引不是只按二级索引列排序；相同二级索引值下，还会按主键值排序。因此判断插入是否被 Gap Lock 阻塞，要用 `(二级索引值, 主键值)` 定位新记录在二级索引树中的位置，再看插入位置的下一条记录是否带有间隙锁。
- 可用能力：能解释为什么已有 `(age=39,id=20)` 边界锁时，插入 `(age=39,id=3)` 会被挡在它前面，而插入 `(age=39,id=21)` 可能落在它后面，不被这把锁挡住。
- 常见失败信号：只看 `age` 值，不看 `id`；认为所有 `age=39` 插入都一样；不能用“插入位置的下一条记录是否有 Gap Lock”判断阻塞。
- 状态：形成中；第一轮检查通过。学习者能说明 `(age=39,id=3)` 会落在带锁边界 `(age=39,id=20)` 前面，下一条记录就是带锁边界，因此可能被阻塞；`(age=39,id=21)` 落在它后面，下一条记录不再是这个带锁边界，所以可能不被这把锁阻塞。

## 用非唯一索引范围查询理解二级 Next-Key 不退化

- 触发场景：分析非唯一二级索引范围查询，例如 `age >= 22 for update`。
- 压缩概念：非唯一索引范围查询时，二级索引上扫描到的记录通常都保持 Next-Key Lock，不像唯一索引那样因为命中边界就退化成 Record Lock。原因是非唯一索引不能证明某个值只有一行，必须用范围锁防止同值或范围内新行插入；匹配到的真实行还要在主键索引上加 Record Lock。
- 可用能力：能解释 `age >= 22` 会在二级索引上锁 `(21,22]`、`(22,39]`、`(39,+∞]`，并对匹配行的主键 `id=10`、`id=20` 加 Record Lock。
- 常见失败信号：把非唯一索引 `age >= 22` 的 `age=22` 边界当成唯一索引 `id >= 15` 一样退化成 Record Lock；漏掉主键索引锁；漏掉 supremum 末尾区间。
- 状态：形成中；第一轮检查通过但已补精确点。学习者能说明保留 `(21,22]` Next-Key Lock，用间隙部分防止新的同值/边界附近记录插入；已补充根因是 `age` 非唯一，不能证明 `age=22` 只有一条，所以不能像唯一索引边界一样退化成 Record Lock。

## 用扫描路径解释无索引锁定语句近似锁全表

- 触发场景：分析 `select ... for update`、`update`、`delete` 的条件没有走有效索引时为什么危险。
- 压缩概念：InnoDB 行锁锁的是扫描到的索引记录和间隙；如果锁定读或写操作没有走索引而是全表扫描，就会扫描大量甚至所有记录，并给扫描到的索引记录加 next-key lock，效果接近锁住整张表，阻塞大量插入、更新、删除。
- 可用能力：能解释为什么线上执行 `update/delete/select ... for update` 前要检查是否走索引；能把“慢查询风险”和“大范围锁阻塞风险”分开说清楚。
- 常见失败信号：认为 InnoDB 行锁天然只锁少量行；只说无索引会慢，漏掉它会扫描并加锁很多记录；不使用 `EXPLAIN` 检查执行计划。
- 状态：形成中；第一轮检查通过。学习者能说明无有效索引时 InnoDB 可能全表扫描，而带锁语句会给扫描到的索引记录加 next-key lock，扫描很多记录就会对很多记录和间隙加锁，效果接近锁全表，阻塞大量插入、更新、删除。

## 用等待图解释幂等校验死锁

- 触发场景：分析业务先用 `select ... for update` 做幂等性校验，查到记录不存在后再 `insert`，为什么高并发下可能死锁。
- 压缩概念：在 RR 下，两个事务分别查询不存在的尾部订单号，都会在非唯一二级索引 `index_order` 上持有 `(1006,+∞]` 的 next-key lock；gap 部分兼容且 supremum 不是实际记录，所以查询阶段可共存。后续 insert 需要插入意向锁，插入点落在对方 gap lock 里，导致 A 等 B、B 等 A 的循环等待。
- 可用能力：能把死锁画成等待图，而不是只说“next-key lock 导致死锁”；能区分查询阶段为什么不阻塞、插入阶段为什么阻塞。
- 常见失败信号：认为两个 `select ... for update` 一定先互相阻塞；不知道 gap lock 之间兼容；看不到插入意向锁与 gap lock 的冲突；不能说出循环等待链。
- 状态：形成中；第一轮检查通过。学习者能说明查询阶段两个尾部 next-key lock 可共存，是因为 Gap Lock 兼容且 supremum 不是真实记录；也能说明 insert 阶段插入意向锁被对方 gap lock 阻塞，形成 A 等 B、B 等 A 的循环等待。

## 用隐式锁解释 insert 正常情况和冲突情况

- 触发场景：分析 `insert` 为什么正常执行时不一定能在 `performance_schema.data_locks` 里看到显式锁，但在 gap lock 或唯一键冲突时又会出现显式等待锁。
- 压缩概念：普通 insert 正常执行时通常不生成显式锁结构，而是靠插入记录上的 `trx_id` 隐藏列形成隐式锁。只有当插入点遇到已有 gap lock，或插入值与已有主键/唯一二级索引冲突时，InnoDB 才把冲突关系显式化，例如生成等待状态的插入意向锁，或把已有未提交记录的隐式锁转成 X 型记录锁。
- 可用能力：能解释“没在 `data_locks` 看到锁”不等于插入记录没有被事务保护；能说明并发唯一值冲突时，后来的事务为什么会等待先插入但未提交的事务。
- 常见失败信号：认为每个 insert 一开始都会生成显式锁；看到 `data_locks` 没显示就认为别的事务能随便覆盖；不知道隐式锁会在冲突时转成显式锁。
- 状态：形成中；第一轮检查通过。学习者能说明 insert 正常情况靠 `trx_id` 隐式锁保护，唯一二级索引重复插入时后来的事务申请 S 型 next-key lock，先插入事务的隐式锁转成显式 X 记录锁，X/S 冲突导致等待。

## 区分主键冲突和唯一二级索引冲突

- 触发场景：分析 `insert` 因重复主键或重复唯一二级索引值失败后，会在已有记录上加哪种 S 型行级锁。
- 压缩概念：重复主键定位在聚簇索引的一条已有记录上，插入失败后加 S 型记录锁，`LOCK_MODE` 类似 `S, REC_NOT_GAP`；重复唯一二级索引值发生在唯一二级索引记录上，插入失败后加 S 型 next-key lock，文章强调不论隔离级别都会加，甚至 RC 下也是少数加间隙锁的场景。
- 可用能力：能区分 `insert id=5` 主键冲突和 `insert order_no=1001` 唯一二级索引冲突，不把两者都泛化成“加 S 锁”或“S 记录锁”。
- 常见失败信号：认为所有唯一约束冲突都只加记录锁；忽略唯一二级索引是在二级索引上加 S 型 next-key lock；看到 `LOCK_TYPE=RECORD` 就误判为 Record Lock。
- 状态：形成中；第一轮检查通过。学习者能说明重复主键失败加 S 型 Record Lock，重复唯一二级索引失败加 S 型 Next-Key Lock，并能区分 S/X 是锁模式、Record/Next-Key 是锁范围。

## 区分事后解除死锁和业务预防死锁

- 触发场景：回答“如何避免死锁”时，需要分清数据库已经发生死锁后的解除机制，以及业务设计上如何减少死锁发生。
- 压缩概念：`innodb_lock_wait_timeout` 通过等待超过阈值后回滚一个事务来释放锁；`innodb_deadlock_detect=on` 通过主动检测等待图中的环并回滚其中一个事务来打破循环等待。它们都是死锁发生后的解除方式。把 `order_no` 设为唯一索引，是把“订单号不能重复”交给数据库唯一约束，减少先查不存在再插入的范围锁流程，属于业务层预防。
- 可用能力：能回答数据库层两种处理策略的区别，也能说明唯一索引不是“死锁检测”，而是业务约束设计。
- 常见失败信号：把唯一索引说成发生死锁后的解除机制；把锁等待超时和主动死锁检测混为一谈；只说“开启检测”而不说会回滚死锁链中的一个事务。
- 状态：形成中；第一轮检查通过。学习者能说明 `innodb_lock_wait_timeout` 是超过阈值后回滚等待事务释放锁，`innodb_deadlock_detect=on` 是主动发现等待图里的环并回滚死锁链中的某个事务；能说明 `order_no` 唯一索引是业务预防，把重复订单交给唯一约束和异常处理。

## 用 data_locks 逆推未命中主键 update 的 Gap Lock 范围

- 触发场景：分析 `show_lock.md` 这类面试题，`update ... where id = 25` 没有命中任何行，却在 `performance_schema.data_locks` 里看到行级锁。
- 压缩概念：`update` 是当前读/写操作，在 RR 下即使主键等值未命中，也要用 Gap Lock 锁住目标值应落入的主键索引间隙，防止别人插入该值。`LOCK_TYPE=RECORD` 只表示行级锁，具体锁种要看 `LOCK_MODE`；`X, GAP` 是 Gap Lock。对 Gap/Next-Key 场景，`LOCK_DATA=30` 可作为右边界，左边界是主键索引中 30 的上一条记录 20，所以锁范围是 `(20,30)`。
- 可用能力：能解释 `Rows matched: 0` 不等于不加锁；能从 `LOCK_TYPE`、`LOCK_MODE`、`LOCK_DATA` 推导出 `(20,30)` Gap Lock。
- 常见失败信号：把 `LOCK_TYPE=RECORD` 当成 Record Lock；只看到未命中就说没有行锁；只说锁 `id=25`，而不能说锁的是主键索引相邻间隙 `(20,30)`。
- 状态：形成中；第一轮检查通过。学习者能说明 `update id=25` 是写操作/当前读，即使命中 0 行也要锁目标值应插入的位置；能用 `LOCK_MODE=X, GAP` 判断是 Gap Lock，并用 `LOCK_DATA=30` 加上一条主键记录 `20` 推出 `(20,30)`。

## 区分 Gap Lock 兼容和插入意向锁冲突

- 触发场景：分析 `show_lock.md` 中为什么事务 A/B 都能先持有 `(20,30)` Gap Lock，但后续分别插入 `25/26` 会被阻塞。
- 压缩概念：Gap Lock 的目的只是阻止其他事务往区间里插入，因此 Gap Lock 与 Gap Lock 之间兼容，即使都是 X 型也不互斥。`insert` 真正要进入该区间时，会生成插入意向锁；插入意向锁与已有 Gap Lock 冲突，所以插入点落在对方 Gap Lock 范围里就会等待。
- 可用能力：能把“update 阶段不冲突”和“insert 阶段冲突”分开解释，而不是笼统说 X 锁都冲突。
- 常见失败信号：认为两个 X 型 Gap Lock 必然冲突；把插入意向锁当成表级意向锁；不能说明插入意向锁是等待请求结构，不表示已经成功插入。
- 状态：形成中；第一轮检查通过。学习者能说明 Gap Lock 与 Gap Lock 兼容、插入意向锁与 Gap Lock 冲突；能解释 A 插入 `id=25` 会生成插入意向锁并被 B 的 `(20,30)` Gap Lock 阻塞。已补精确点：Gap Lock 兼容的根因是它只负责禁止插入区间。

## 用等待图解释 show_lock 死锁

- 触发场景：分析 `show_lock.md` Time 3/4 中两个 insert 都阻塞后，为什么这不是两个孤立等待，而是死锁。
- 压缩概念：事务 A 已持有 `(20,30)` Gap Lock，A 插入 `id=25` 时等待事务 B 释放 `(20,30)` Gap Lock；事务 B 已持有 `(20,30)` Gap Lock，B 插入 `id=26` 时等待事务 A 释放 `(20,30)` Gap Lock。A 等 B 且 B 等 A，形成循环等待，满足死锁条件。
- 可用能力：能画出等待链并解释死锁，而不是只说“insert 被 gap lock 阻塞”。
- 常见失败信号：只描述 A 被 B 阻塞，漏掉 B 也被 A 阻塞；不能把双方等待合成循环等待；把单个等待误称为死锁。
- 状态：形成中；第一轮检查通过。学习者能说明 A 插入 `id=25` 时等 B 的 Gap Lock，B 插入 `id=26` 时等 A 的 Gap Lock，并指出这是循环等待。已补精确点：等待的是对方在主键索引上持有的 `(20,30)` Gap Lock。

## 用三类日志分清 UPDATE 的安全目标

- 触发场景：解释一条 `update` 执行期间为什么同时出现 undo log、redo log、binlog，以及它们分别解决什么问题。
- 压缩概念：undo log 记录旧值或反向操作，服务事务回滚和 MVCC 版本链；redo log 记录 InnoDB 数据页物理修改，服务 WAL 和 crash-safe；binlog 是 Server 层追加日志，服务备份恢复和主从复制。三者不互相替代。
- 可用能力：能把“回滚到旧值”“崩溃后重做到新值”“把变更历史交给备份/从库”分成三个目标，并说出对应日志。
- 常见失败信号：把三类日志都泛化成恢复日志；说 binlog 属于 InnoDB；说 redo log 能恢复任意历史误删；无法解释为什么有 binlog 还需要 redo log。
- 状态：形成中；第一轮检查通过。学习者能按“怕什么 -> 靠哪个日志”说明：事务未提交中途失败要撤回靠 undo log；事务已提交但脏页未刷盘时崩溃恢复靠 redo log + WAL；主从复制和备份恢复知道变更历史靠 Server 层 binlog。

## 用 undo log 区分回滚动作和版本链来源

- 触发场景：解释为什么 InnoDB 在 update/delete/insert 前都要生成 undo log，以及 undo log 为什么不只是 rollback 用。
- 压缩概念：undo log 先记录能够做反向操作的信息：insert 记主键用于回滚删除，delete 记完整旧记录用于回滚插入，update 记被更新列旧值用于改回。每次更新记录还带有 `trx_id` 和 `roll_pointer`，`roll_pointer` 把 undo log 串成版本链，普通快照读可以沿版本链寻找对当前 Read View 可见的旧版本。
- 可用能力：能同时回答“事务回滚时如何用 undo log”和“MVCC 快照读如何用 undo log 找旧版本”，避免把 undo log 只理解成回滚日志。
- 常见失败信号：只说 undo log 记录旧值但说不出插入/删除/更新各自的反向动作；只说用于回滚，漏掉 Read View + undo log 的快照读；把 `roll_pointer` 和 `trx_id` 作用混淆。
- 状态：形成中；第一轮检查通过。学习者先答出回滚记录反向操作信息、MVCC 旧版本来源以及 `trx_id`/`roll_pointer`，经修补后能说明普通快照读用 Read View 判断当前版本不可见时，会沿 `roll_pointer` 到 undo log 版本链里找旧版本。

## 用 Buffer Pool 和 WAL 解释 redo log 的必要性

- 触发场景：解释为什么 InnoDB 已经有 Buffer Pool 缓存页，还需要 redo log；或解释 update 为什么可以不立刻把脏页刷盘。
- 压缩概念：InnoDB 以页为单位读写，更新先修改 Buffer Pool 中的数据页并标记为脏页；脏页不立即写回磁盘可以减少随机 I/O，但内存不可靠，崩溃会丢。redo log 先顺序记录数据页物理修改，提交时保证 redo log 持久化，之后脏页可以由后台线程慢慢刷盘，这就是 WAL。
- 可用能力：能回答“为什么不是每次 update 都同步写数据页到磁盘”，并能说明 redo log 同时提供 crash-safe 和把随机写转成顺序写的性能收益。
- 常见失败信号：认为 update 完成必须立刻把数据页写回磁盘；只说 Buffer Pool 提升性能但不说脏页丢失风险；说 redo log 记录 SQL 语句；忽略 redo log 是为了保护 Buffer Pool 脏页延迟落盘。
- 状态：形成中；第一轮检查通过。学习者能说明数据页直接写磁盘是随机 I/O、成本高，所以 update 先改 Buffer Pool 中的页并标记脏页；也能说明内存不可靠，崩溃时未刷盘脏页会丢，因此 redo log 要先记录数据页物理修改，提交后可用 WAL 重做。

## 用 redo log buffer 和刷盘参数区分 0/1/2

- 触发场景：解释 `innodb_flush_log_at_trx_commit` 为 0、1、2 时，redo log 在事务提交时到底停在哪一层，以及不同崩溃类型会丢什么。
- 压缩概念：redo log 产生后先进入 redo log buffer。参数 1 表示每次提交把 redo log 持久化到磁盘，最安全；参数 0 表示提交时仍留在 redo log buffer，后台线程约每秒 write + fsync，MySQL 进程崩溃也可能丢最近事务；参数 2 表示提交时 write 到 redo log 文件，也就是进入 OS Page Cache，MySQL 进程崩溃通常不丢，但 OS 崩溃或断电可能丢约 1 秒。
- 可用能力：能把 redo log buffer、redo log 文件/Page Cache、磁盘三层分开，并解释安全性 `1 > 2 > 0`、性能 `0 > 2 > 1`。
- 常见失败信号：把 write 到文件当成 fsync 到磁盘；说 0 和 2 风险一样；只背数字不区分 MySQL 进程崩溃、操作系统崩溃、断电。
- 状态：形成中；第一轮检查通过。学习者能按 0/1/2 对应 `redo log buffer / 磁盘 / OS Page Cache`，并能说明 `innodb_flush_log_at_trx_commit=1` 最安全、`0` 性能最好。精确点：`2` 是 write 到 redo log file，即 OS Page Cache，不是 fsync 到磁盘；安全性排序为 `1 > 2 > 0`，性能排序为 `0 > 2 > 1`。

## 用 redo log 和 binlog 区分 crash-safe 与归档复制

- 触发场景：回答“为什么有了 binlog 还要 redo log”或“redo log 和 binlog 有什么区别”。
- 压缩概念：redo log 是 InnoDB 存储引擎层的物理日志，循环写，记录数据页物理修改，服务 crash-safe；binlog 是 MySQL Server 层的追加日志，记录表结构变更和表数据修改，保存全量变更历史，服务备份恢复和主从复制。
- 可用能力：能说明误删库不能靠 redo log 恢复历史，要靠 binlog；也能说明主从复制依赖 binlog，而 InnoDB 崩溃恢复依赖 redo log。
- 常见失败信号：说 binlog 也能替代 redo log 做 crash-safe；说 redo log 可以恢复任意历史误删；分不清物理日志、逻辑/行变化日志、循环写和追加写。
- 状态：形成中；第一轮检查通过。学习者能说明 binlog 是 Server 层追加日志，记录表结构变更和表数据修改，保存全量变更历史，用于备份恢复、主从复制和误删后的历史重放。精确点：redo log 是循环写的 InnoDB 物理日志，主要用于 crash-safe，不是历史账本。

## 用 binlog 格式区分语句重放和行结果重放

- 触发场景：解释 binlog 的 STATEMENT、ROW、MIXED 三种格式，以及为什么某些场景下 ROW 比 STATEMENT 更可靠。
- 压缩概念：STATEMENT 记录修改数据的 SQL，从库按 SQL 重新执行，日志量小但遇到 `uuid()`、`now()` 等非确定性函数或并发时可能无法复现主库结果；ROW 记录每行最终变成什么样，能保留主库实际结果但批量更新时日志量大；MIXED 根据情况在 STATEMENT 和 ROW 之间选择。
- 可用能力：能回答“statement binlog 重放的是 SQL，row binlog 重放的是主库实际行结果”，并能解释为什么 ROW 能避免非确定性函数导致的主从不一致。
- 常见失败信号：以为 STATEMENT 记录每行最终值；以为 ROW 记录原 SQL；只说 ROW 更安全但说不出它记录的是实际行变化。
- 状态：形成中；第一轮检查通过。学习者能说明 STATEMENT 遇到 `uuid()`、`now()` 等非确定性函数时，主库和从库重新执行可能得到不同结果；ROW 记录主库最终行值，因此一致性更强，但批量更新很多行时日志更大。

## 用三阶段解释 MySQL 主从复制

- 触发场景：解释 MySQL 主从复制如何依赖 binlog，以及异步复制为什么可能在主库宕机时丢数据。
- 压缩概念：主从复制分三步：主库写入 binlog 并提交本地事务；从库 I/O 线程连接主库 log dump 线程，把 binlog 拉到从库 relay log；从库回放线程读取 relay log 并执行变更。默认异步复制下，主库提交事务的线程不等待所有从库完成同步就返回客户端。
- 可用能力：能把“写入 binlog -> 同步到 relay log -> 回放 relay log”串成复制链路，并解释同步/异步/半同步的基本取舍。
- 常见失败信号：认为主库提交必须等所有从库回放完成；把 relay log 和 binlog 混成一个文件；只说“主从靠 binlog”但不能说出 I/O 线程和回放线程的分工。
- 状态：形成中；第一轮检查通过。学习者能说明主库执行事务、写 binlog、提交本地事务并返回成功；从库 I/O 线程连接主库 log dump 线程，把主库 binlog 拉到从库 relay log；从库回放线程读取 relay log 并执行变更，使从库数据追上主库。

## 用 binlog cache 和 sync_binlog 区分 write/fsync

- 触发场景：解释 binlog 什么时候刷盘，以及 `sync_binlog=0/1/N` 的性能和风险差异。
- 压缩概念：事务执行过程中先把 binlog 写入每个线程自己的 binlog cache；事务提交时把完整事务从 binlog cache 写入 binlog 文件。这里的 write 只是写到操作系统 Page Cache，不等于持久化到磁盘；fsync 才是真正落盘。`sync_binlog=0` 每次提交只 write、不主动 fsync；`1` 每次提交 write 后立刻 fsync；`N` 每次提交 write，但累计 N 个事务后 fsync。
- 可用能力：能区分 binlog cache、binlog file/Page Cache、磁盘三层，并说明 `sync_binlog=1` 最安全但 I/O 高，`0` 性能最好但主机异常重启风险最大，`N` 是性能和丢失风险的折中。
- 常见失败信号：把 write 当成落盘；认为每个线程最终写自己的 binlog 文件；只说 `sync_binlog=1` 安全但说不出为什么 I/O 高。
- 状态：形成中；第一轮检查通过。学习者能说明 `sync_binlog=0/1/N` 的 write/fsync 频率，并经修补后能区分 write 只是写入 binlog 文件/OS Page Cache，不等于磁盘持久化；fsync 才是把 Page Cache 刷到磁盘。

## 串联一条 UPDATE 的完整执行过程

- 触发场景：回答“执行一条 update 语句期间发生了什么”，需要把执行器、Buffer Pool、undo log、redo log、binlog 和提交流程串成顺序链。
- 压缩概念：执行器按执行计划调用 InnoDB 找到聚簇索引记录；如果数据页不在 Buffer Pool，就先读入；若新旧记录不同，InnoDB 先写 undo log 并为 Undo 页面修改写 redo，再更新 Buffer Pool 中的数据页、标记脏页并写 redo log；语句执行完后 Server 层把 binlog 写入 binlog cache；事务提交时进入两阶段提交协调 redo log 和 binlog。
- 可用能力：能按正确顺序口述一条 `UPDATE t_user SET name='xiaolin' WHERE id=1` 的执行链，而不是把三类日志孤立背诵。
- 常见失败信号：把 binlog 放到 InnoDB 更新前；漏掉 undo 页修改也要 redo；认为脏页立即刷盘；跳过事务提交阶段的两阶段提交。
- 状态：形成中；第一轮检查通过。学习者能按顺序说明：改数据页前写 undo log 保存旧值；修改 Buffer Pool 中的数据页并标记脏页；写 redo log 保证脏页未刷盘时可崩溃重做；Server 层把对应 binlog 先放入 binlog cache。

## 用两阶段提交保持 redo log 与 binlog 一致

- 触发场景：解释事务提交时为什么不能简单地“先刷 redo 再刷 binlog”或“先刷 binlog 再刷 redo”，以及崩溃恢复如何判断提交还是回滚。
- 压缩概念：redo log 影响主库崩溃恢复，binlog 影响备份恢复和从库重放；二者若半成功会导致主从或恢复结果不一致。MySQL 用内部 XA 两阶段提交：prepare 阶段把 XID 写入 redo log 并把 redo log 标为 prepare 后刷盘；commit 阶段把 XID 写入 binlog 并刷盘，再调用引擎提交，把 redo log 标为 commit。崩溃恢复时，若 redo log 处于 prepare，就拿 XID 去 binlog 查：有则提交，无则回滚。
- 可用能力：能分析崩溃点 A/B：redo prepare 后 binlog 未写时崩溃要回滚；binlog 已写但 redo commit 标识未写时崩溃要提交。
- 常见失败信号：认为 redo log prepare 一定代表事务已提交；认为 binlog 写成功后主库还能回滚；不知道 XID 是连接 redo log 和 binlog 的判断依据。
- 状态：稳定。学习者能判断 redo log 处于 prepare 时，要用 XID 去 binlog 查：binlog 无该 XID 则回滚，binlog 有该 XID 则提交；在本轮 MCQ 复习中再次正确选择 binlog 已有同一 XID 时提交。

## 用组提交降低双 1 提交的刷盘成本

- 触发场景：解释 `sync_binlog=1` 且 `innodb_flush_log_at_trx_commit=1` 时为什么提交 I/O 高，以及 MySQL 如何在保证顺序的同时减少 fsync 次数。
- 压缩概念：两阶段提交保证 redo log 与 binlog 一致，但“双 1”下每个事务提交至少有 redo log 和 binlog 两次 fsync，且早期大锁会放大并发竞争。组提交把多个事务组织成队列，由 leader 统一处理一组事务：flush 阶段把多个事务的 binlog 从 cache write 到 binlog 文件但不 fsync，并可支撑 redo log 组提交；sync 阶段把多个事务的 binlog 合并做一次 fsync；commit 阶段按顺序调用 InnoDB commit，把 redo log 标为 commit。
- 可用能力：能解释组提交不是取消刷盘，而是把多事务的刷盘合并，并能区分 `binlog_group_commit_sync_delay` 是故意等待以凑更多事务，`sync_binlog=N` 是改变 fsync 频率且主机掉电可能丢事务 binlog。
- 常见失败信号：认为组提交就是不刷盘；把 flush 阶段当成已经持久化到磁盘；分不清组提交延迟和 `sync_binlog=N` 的风险边界；不知道 sync 阶段才是合并 binlog fsync。
- 状态：形成中；第一轮检查通过。学习者能说明 flush 阶段把多个事务的 binlog 从 binlog cache write 到 binlog 文件但不 fsync，并支撑 redo log 组提交；sync 阶段对 binlog 文件做 fsync，把多个事务合并成一次刷盘；commit 阶段按顺序调用 InnoDB commit，把 redo log 状态改成 commit。

## 综合口述一条 UPDATE 的日志全链路

- 触发场景：面试中被问“执行一条 update 语句期间发生了什么”，需要把本章所有机制按顺序组织成一段完整回答。
- 压缩概念：一条 UPDATE 先由执行器按执行计划调用 InnoDB 找记录；InnoDB 改数据页前写 undo log，改 Buffer Pool 数据页并标记脏页，同时写 redo log 保护 WAL/crash-safe；Server 层把 binlog 放入 binlog cache；事务提交时用两阶段提交协调 redo log prepare、binlog 写入和 redo log commit；高并发下组提交把多事务 binlog fsync 合并，降低双 1 的 I/O 成本。
- 可用能力：能用 8-10 句话口述从“找记录”到“组提交优化”的完整链路，并能说明每种日志解决的风险。
- 常见失败信号：只背三类日志用途但说不出顺序；漏掉 binlog cache 或两阶段提交；把组提交误说成不刷盘；不能解释 redo/binlog 半成功为什么危险。
- 状态：形成中；当前教学 schema。
