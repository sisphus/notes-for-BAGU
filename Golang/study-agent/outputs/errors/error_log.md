# Error Log

## 2026-08-10 - Exported JSON field omitted by `omitempty`

- Topic: Struct visibility, JSON tags, and omission rules.
- Question: Predict `json.Marshal(User{})` for exported ``Name string `json:"name,omitempty"` `` and unexported `age int`.
- User answer: Correctly identified that `Name` is exported and `age` is unexported, but omitted the final JSON result and the `omitempty` decision for `Name`.
- Correct reasoning: `age` is ignored because it is unexported. `Name` is eligible for encoding, but its value is the empty string, so `omitempty` omits it. The result is `{}`.
- Error type: Boundary confusion.
- Fix strategy: Evaluate visibility first, then independently evaluate `omitempty` for every eligible exported field.
- Next repair boundary: Decide whether an exported empty string tagged with `omitempty` appears in JSON.
- Follow-up: Repaired. Learner correctly explained that `Name == ""` is omitted by `omitempty` even though the field is exported.

## 2026-08-10 - Map struct value copy without write-back

- Topic: Updating a struct value stored in a map.
- Question: After `u := users["a"]; u.Name = "new"`, what does `users["a"].Name` return if `users["a"] = u` is not executed?
- User answer: `"new"`, with the explanation that the modified copy had already been written back.
- Correct reasoning: Map indexing copies the stored struct value into local variable `u`. Changing `u.Name` changes only that local copy. Without the explicit assignment `users["a"] = u`, the map entry remains `"old"`.
- Error type: Concept misunderstanding.
- Fix strategy: Display the local value and map value side by side before introducing the write-back line.
- Next repair boundary: Predict `u.Name` and `users["a"].Name` separately after modifying only `u`.
- Follow-up: Repaired. Learner correctly predicted local `u.Name == "new"` and unchanged map value `users["a"].Name == "old"` without write-back.

## 2026-08-09 - Concurrent map risk mechanism

- Topic: Map concurrent-access boundary.
- Question: Is one goroutine incrementing a map while another reads it safe without synchronization?
- User answer: Correctly said unsafe, but described the result only as dirty or unexpected data.
- Correct reasoning: The overlapping read and write have no synchronization relationship and therefore form a data race. Ordinary map internals also do not support concurrent read/write and the runtime may terminate with `concurrent map read and map write`; absence of that fatal error does not make the execution safe.
- Error type: Surface-level memorization.
- Fix strategy: Replace the vague outcome model with `overlapping access + at least one write + no shared synchronization -> data race`, then check whether both readers and writers participate in the same locking discipline.
- Next repair boundary: A writer uses a mutex but the reader does not; decide whether the map is protected.
- Follow-up: Repaired on 2026-08-10. Learner correctly stated that writer-only locking is insufficient and that the reader must participate in the locking discipline.

## 2026-08-09 - Nil map delete versus write

- Topic: Map initialization boundary.
- Question: Predict lookup, delete, and increment behavior on `var counts map[string]int`.
- User answer: Correctly gave `v=0`, `ok=false`, and predicted panic for `counts["go"]++`, but said `delete` was unsafe and explained the panic as accessing a nonexistent item.
- Correct reasoning: Reading a missing key from a nil map returns the value type's zero value; comma-ok reports false. Deleting from a nil map is a safe no-op. The increment panics because it includes a write to a nil map, not because the key is absent.
- Error type: Boundary confusion.
- Fix strategy: Separate key absence from storage initialization, then contrast safe read/delete with a write before and after `make`.
- Next repair boundary: Predict one sequence containing `delete(nilMap)`, `make`, and a subsequent increment.
- Follow-up: Repaired. Learner correctly predicted no panic and final value `1` after safe delete, initialization with `make`, and increment.

## 2026-06-05 - Go package organization and import cycle

- Topic: Package / Import / Init.
- Question: If `handler` imports `service`, `service` imports `repo`, and `repo` imports `service`, where is the cycle and how should it be split?
- User answer: `service->repo->service`, extract the shared types to a third lower-level package, but does not know concrete steps.
- Correct reasoning: The cycle is `service -> repo -> service`. Break it by moving shared data shapes or narrow interfaces to a lower-level package, or by inverting the dependency so `repo` does not import `service`.
- Error type: Procedure confusion.
- Fix strategy: Use a minimal before/after example showing what moves from `service` into `model` or `ports`.
- Follow-up: Learner correctly answered that shared `service.User` should move to `model/domain`.

## 2026-08-08 - Multi-return `err` shadowing

- Topic: Variables / short declarations / scope.
- Question: When inner `result, err := load()` receives `load failed`, does the function return the error or nil, and which `err` is returned?
- User answer: `nil`, but said the inner `err` is returned.
- Correct reasoning: The inner `err` contains the error but stops being visible after the `if` block. The final `return err` resolves to the outer `err`, whose zero value is still nil.
- Error type: Boundary confusion.
- Fix strategy: Draw nested scopes, then repair the code by declaring `result` and using assignment `=` so the outer `err` is updated.
- Repair attempt: Learner changed `:=` to `=`, but wrote `var result var err`; the second `var` is invalid and redeclaring inner `err` would preserve the original bug.
- Next repair boundary: Declare only `result`; reuse the already-declared outer `err`.
- Follow-up: Learner correctly identified that `result, err = load()` updates the outer `err`.

## 2026-08-08 - Typed versus untyped constant

- Topic: Variables / constants / zero values.
- Question: Can `const timeout int = 10; var seconds int64 = timeout` compile directly?
- User answer: Yes, because a representable value can adapt to the target type.
- Correct reasoning: Automatic contextual adaptation applies to an untyped constant such as `const timeout = 10`. The explicit `int` makes this a typed constant, so assigning it to `int64` requires `int64(timeout)`.
- Error type: Boundary confusion.
- Fix strategy: Compare two declarations that differ only by the explicit `int`, then require a compile/no-compile prediction.
- Follow-up: Learner correctly answered that the untyped declaration compiles directly while the typed `int` declaration requires explicit conversion.

## 2026-08-08 - `iota` starting value

- Topic: Variables / constants / zero values.
- Question: Predict A, B, and D in `A = iota; B; _; D`.
- User answer: `A=1, B=2, D=4`.
- Correct reasoning: Each `const` block starts with `iota == 0`; every ConstSpec line increments it, including `_`. Therefore A=0, B=1, `_` receives 2, and D=3.
- Error type: Concept misunderstanding.
- Fix strategy: Label declaration lines 0, 1, 2, 3 before evaluating names; then contrast default zero start with `_ = iota` used to skip zero.
- Follow-up: Learner correctly predicted `A=1, B=2` after `_ = iota` explicitly discarded zero.

## 2026-08-08 - Persisted `iota` compatibility

- Topic: Variables / constants / backend compatibility.
- Question: Old database value `2` meant Paid; after inserting Reviewing before Paid, what does new code read and what problem occurs?
- User answer: `3`; correctly noted that old data would be misinterpreted and Paid would display incorrectly.
- Correct reasoning: The database still stores `2`. In the new mapping, `2` names Reviewing, while Paid has moved to `3`; therefore the old paid row is read as Reviewing.
- Error type: Boundary confusion.
- Fix strategy: Trace the unchanged stored integer through the new mapping instead of following the renamed business constant.
- Follow-up: Learner correctly identified stored integer `2` as `Reviewing` under the new mapping.

## 2026-08-08 - Slice len/cap before append

- Topic: Array / Slice.
- Question: For `b := a[1:3]` where `a` has len and cap 4, compute len/cap and predict `b = append(b, 99)`.
- User answer: Gave len/cap as `4,2` and predicted `a` remained `[1,2,3,4]`.
- Correct reasoning: `len(b)=high-low=2`; `cap(b)=cap(a)-low=3`. Appending one element makes len 3, which fits cap 3, so the original backing array is reused and `a[3]` becomes 99.
- Error type: Procedure confusion.
- Fix strategy: Compute len and cap with formulas before evaluating the append branch.
- Follow-up: Learner correctly computed len=2, cap=3 for `a[2:4]` over a five-element backing slice and predicted reuse with the appended value visible in `a`.
- Second follow-up: Learner correctly predicted allocation when len equaled cap and correctly kept later `b` mutation isolated from `a`.

## 2026-08-08 - Slice copy top-level separation

- Topic: Array / Slice copy.
- Question: After allocating dst, copying src, and setting `dst[0]=9`, what is src?
- User answer: `[9,2,3]`.
- Correct reasoning: `make` allocates a new backing array for dst and `copy` copies element values into it. The source remains `[1,2,3]`; only direct descriptor assignment would share the same backing array.
- Error type: Concept misunderstanding.
- Fix strategy: Compare descriptor-to-array diagrams for `dst := src` and `make + copy`, then choose which write propagates.
