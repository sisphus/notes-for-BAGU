# Error Log

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
