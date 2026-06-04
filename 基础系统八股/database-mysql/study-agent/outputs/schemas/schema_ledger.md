# Schema Ledger

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

## Storage Engine Page B+Tree Bridge

- Trigger situation: Preparing to explain why InnoDB indexes reduce disk I/O.
- Compressed concepts: The storage engine owns physical data and indexes; InnoDB reads and writes data mainly by page; a B+Tree organizes key ranges so MySQL can reach the target leaf page through a few page reads.
- What the learner can do after acquiring it: Explain an indexed lookup as "executor asks storage engine; storage engine walks B+Tree pages; leaf page leads to the row or primary key."
- Common failure signal: Treats B+Tree as an in-memory abstract tree only, or thinks querying one row means reading exactly one row from disk.
- Status: Forming.

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
- Status: Forming; first check answered correctly.

## Covering Index

- Trigger situation: Explaining when a secondary-index query can avoid back-to-table lookup.
- Compressed concepts: If all columns needed by the query are available in the secondary index leaf record, InnoDB can return the result from that index without fetching the clustered-index row.
- What the learner can do after acquiring it: Decide that `select age from product where name = 'Tom'` can be covered by `idx_name_age(name, age)`.
- Common failure signal: Thinks every secondary-index query must return to the clustered index, or ignores that secondary index records also contain the primary key.
- Status: Forming; first check answered correctly.
