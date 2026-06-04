# Weakness Profile

## Topic: MySQL InnoDB Hidden Fields

- Date: 2026-05-31
- Source material: `materials/mysql/base/row_format.md`
- Weakness: Lacks prerequisite schema for `row_id`, `trx_id`, and `roll_pointer`.
- Evidence: User correctly answered `row_id`, then said they still lacked prerequisite knowledge for the three hidden fields.
- Error type: Missing prerequisite.
- Fix strategy: Teach the hidden fields as three jobs: identify a row, identify the creating transaction, and link to the previous version. Delay deeper MVCC details until transaction materials.
- Status: Basic schema repaired; needs review when studying MVCC.

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
