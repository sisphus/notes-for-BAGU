# Review Schedule

## 2026-05-31

- Topic: Variable-length field length list.
  - Reason: Earlier answer described length metadata as helping "search data"; repaired to field-boundary parsing.
  - Next review: 2026-06-01.
  - Prompt: Why does InnoDB need length metadata for `VARCHAR` fields?
  - Status: Needs short recall.

- Topic: NULL value list bit vs byte.
  - Reason: Earlier answer said one fewer NULL means one fewer bit; repaired to whole-byte allocation.
  - Next review: 2026-06-01.
  - Prompt: Why might reducing nullable columns from 3 to 2 not reduce physical row size?
  - Status: Needs short recall.

- Topic: InnoDB hidden fields.
  - Reason: User reported missing prerequisite knowledge for `row_id`, `trx_id`, and `roll_pointer`; basic schema repaired.
  - Next review: When starting MVCC.
  - Prompt: Match `row_id`, `trx_id`, and `roll_pointer` to their jobs.
  - Status: Review with transaction materials.

- Topic: Storage engine, page, and B+Tree bridge.
  - Reason: User reported missing prerequisite schema before learning MySQL index interview questions.
  - Next review: 2026-06-01.
  - Prompt: In one sentence each, explain what the storage engine, page, and B+Tree do in an indexed lookup.
  - Status: Passed immediate fill-in; needs delayed recall.

- Topic: Index classification dimensions.
  - Reason: User correctly labeled a unique key as secondary index but could not explain the four classification dimensions conceptually.
  - Next review: 2026-06-01.
  - Prompt: Classify `UNIQUE KEY uk_email(email)` using the four questions: structure, physical storage, field constraint, field count.
  - Status: Passed immediate classification; needs delayed recall.

- Topic: Covering index.
  - Reason: User correctly explained that `select age where name='Tom'` can avoid back-to-table lookup when using `idx_name_age(name, age)`.
  - Next review: 2026-06-04.
  - Prompt: With `KEY idx_name_age(name, age)`, compare whether `select age where name='Tom'` and `select * where name='Tom'` need back-to-table lookup.
  - Status: New schema; needs delayed recall.
