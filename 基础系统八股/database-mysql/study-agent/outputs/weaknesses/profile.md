# Weakness Profile

## Topic: MySQL insertion intention lock purpose

- Date: 2026-07-07
- Source material: `materials/mysql/lock/mysql_lock.md`
- Weakness: Understands that insert waits when its target point is inside another transaction's gap lock, but does not yet understand why InnoDB represents that wait as an insertion intention lock.
- Evidence: User answered that transaction B blocks and that the insertion intention lock is a row-level lock, then said "我无法理解插入意向锁的意思，感觉没有也不影响吧".
- Error type: Concept misunderstanding.
- Fix strategy: Teach insertion intention lock as a waiting request record: it records who is waiting and which gap/point they want to insert into; it does not mean the insert succeeded or add a new reason for blocking.
- Status: Repaired once on 2026-07-07. User can now say the insertion intention lock records that transaction B is waiting and records the insert position/gap. Needs delayed review to keep it distinct from the blocking gap lock.

## Topic: MySQL binlog replication prerequisite

- Date: 2026-07-07
- Source material: `materials/mysql/lock/mysql_lock.md`
- Weakness: Missing prerequisite schema for why statement-format binlog replays SQL while row-format binlog replays actual row changes.
- Evidence: User explicitly said "missing prerequisite" and did not understand why statement binlog cannot reproduce the primary's concurrent insert timing, while row binlog can avoid AUTO-INC id inconsistency by recording actual row values.
- Error type: Missing prerequisite.
- Fix strategy: Teach the smallest bridge first: statement = redo the SQL in log order; row = apply the concrete row results generated on the primary. Then reconnect to `innodb_autoinc_lock_mode = 2`.
- Status: Repaired once on 2026-07-07. User can now distinguish that statement binlog replays original SQL, while row binlog records the primary's final changed rows and actual values. Needs delayed review when reconnecting to AUTO-INC mode 2.

## Topic: MySQL InnoDB Hidden Fields

- Date: 2026-05-31
- Source material: `materials/mysql/base/row_format.md`
- Weakness: Lacks prerequisite schema for `row_id`, `trx_id`, and `roll_pointer`.
- Evidence: User correctly answered `row_id`, then said they still lacked prerequisite knowledge for the three hidden fields.
- Error type: Missing prerequisite.
- Fix strategy: Teach the hidden fields as three jobs: identify a row, identify the creating transaction, and link to the previous version. Delay deeper MVCC details until transaction materials.
- Status: MVCC bridge repaired. User can now state that `trx_id` records the transaction id that generated or last modified the record version, and `roll_pointer` points through undo log to older versions.

## Topic: MySQL Index Prerequisites

- Date: 2026-05-31
- Source material: `materials/mysql/index/index_interview.md`
- Weakness: Missing prerequisite schema linking storage engine, page-based I/O, and B+Tree index lookup.
- Evidence: User reported needing review of "存储引擎 / B+Tree / 页" before continuing index interview questions.
- Error type: Missing prerequisite.
- Fix strategy: Re-teach as a three-part bridge: storage engine owns data and indexes; page is InnoDB's disk I/O unit; B+Tree reduces page reads by organizing keys in a shallow ordered tree.
- Status: Basic bridge repaired; needs delayed recall.

## Topic: MySQL Index Classification Dimensions

- Date: 2026-05-31
- Source material: `materials/mysql/index/index_interview.md`
- Weakness: Can choose the correct label for an index, but lacks the conceptual anchors for what each classification dimension asks.
- Evidence: User answered that `UNIQUE KEY uk_email(email)` is a secondary index, then said they did not know the reason and could not distinguish the four dimensions conceptually.
- Error type: Missing schema / surface-level memorization.
- Fix strategy: Reframe index classification as four questions: what structure stores it, where the row data lives, what constraint the field has, and how many fields are included.
- Status: Basic schema repaired; needs delayed recall.

## Topic: MySQL B+Tree vs Hash Index

- Date: 2026-06-04
- Source material: `materials/mysql/index/index_interview.md`
- Weakness: Initially identified storage space as the main limitation of Hash indexes instead of loss of order.
- Evidence: User first guessed Hash may need too much storage space, then correctly answered that `where id = 5` is more suitable for Hash because it is exact positioning.
- Error type: Concept priority issue.
- Fix strategy: Anchor the comparison on ordered vs unordered structures: B+Tree keeps keys ordered and supports range/order scans; Hash maps directly for equality but loses order.
- Status: Immediate repair passed; needs delayed recall.
