# Weakness Profile

## Topic: Map struct value copy and write-back

- Date: 2026-08-10.
- Source material: Chapter 01 - Go 语言核心模型, Map.
- Weakness: Treats a struct value copied out of a map as if field changes automatically update the map entry.
- Evidence: Predicted `users["a"].Name == "new"` despite the explicit absence of `users["a"] = u`.
- Error type: Concept misunderstanding.
- Fix strategy: Trace two named values, local `u` and the map entry, and require an explicit arrow only when assignment writes the copy back.
- Status: Improving. Immediate repair correctly separated the local copy from the stored map value; delayed transfer remains necessary.

## Topic: Go package organization and import cycle

- Date: 2026-06-05.
- Source material: Chapter 01 - Go 语言核心模型, Package / Import / Init.
- Weakness: Can identify an import cycle but does not yet know the concrete refactoring steps to break `service -> repo -> service`.
- Evidence: User answered `service->repo->service` and suggested extracting shared types, then said they did not know how to operate concretely.
- Error type: Procedure confusion.
- Fix strategy: Re-teach with a tiny before/after package layout and one extraction decision at a time.
- Status: Improving. Follow-up check correct: learner chose `model/domain` as the better package for shared pure type `User`.

## Topic: Go short declarations and `err` shadowing

- Date: 2026-08-08.
- Source material: Chapter 01 - Go 语言核心模型, 变量 / 常量 / 零值.
- Weakness: Predicts the final nil value but confuses the inner error value with the outer variable referenced by a return statement after the block.
- Evidence: User answered `nil` and then said the inner `err` was returned.
- Error type: Boundary confusion.
- Fix strategy: Track each variable by scope and lifetime, then complete a `:=` to `=` repair.
- Status: Improving. Learner correctly identified the outer `err` after the second targeted repair; delayed review remains necessary.

## Topic: Typed versus untyped Go constants

- Date: 2026-08-08.
- Source material: Chapter 01 - Go 语言核心模型, 变量 / 常量 / 零值.
- Weakness: Applies the representability rule to constants that already have an explicit type.
- Evidence: User said `const timeout int = 10` could adapt directly to `int64`.
- Error type: Boundary confusion.
- Fix strategy: Use side-by-side declarations and ask which one requires explicit conversion.
- Status: Improving. Side-by-side repair answered correctly; delayed review remains.

## Topic: `iota` zero-based sequence

- Date: 2026-08-08.
- Source material: Chapter 01 - Go 语言核心模型, 变量 / 常量 / 零值.
- Weakness: Uses a one-based mental model for `iota` and skips the `_` line when counting.
- Evidence: User predicted `A=1, B=2, D=4` for `A = iota; B; _; D`.
- Error type: Concept misunderstanding.
- Fix strategy: Number every ConstSpec line from zero, then map names onto those line values.
- Status: Improving. Learner correctly handled explicit skip-zero after repair; delayed review remains.

## Topic: Persisted enum compatibility

- Date: 2026-08-08.
- Source material: Chapter 01 - Go 语言核心模型, `iota` compatibility boundary.
- Weakness: Confuses a constant's new numeric value with the meaning assigned to an unchanged integer already stored in the database.
- Evidence: User answered `3` while correctly describing that old Paid data would be displayed incorrectly.
- Error type: Boundary confusion.
- Fix strategy: Trace `stored integer -> new enum map -> interpreted status` as a three-step data flow.
- Status: Improving. Learner correctly traced the unchanged stored integer through the new mapping after repair.

## Topic: Slice len/cap and append reuse

- Date: 2026-08-08.
- Source material: Chapter 01 - Go 语言核心模型, Array / Slice.
- Weakness: Direct shared mutation is understood, but len/cap is miscomputed before append, leading to the wrong allocation branch.
- Evidence: User answered len/cap as `4,2` for `a[1:3]` and predicted the original slice stayed unchanged.
- Error type: Procedure confusion.
- Fix strategy: Always compute `high-low` and `cap(base)-low`, then compare append's required length with cap.
- Status: Improving. Both capacity-sufficient and capacity-exhausted branches were correct after repair; delayed review remains.

## Topic: Slice copy top-level separation

- Date: 2026-08-08.
- Source material: Chapter 01 - Go 语言核心模型, Array / Slice copy.
- Weakness: Treats `make + copy` as if it were direct slice assignment and still shared the top-level backing array.
- Evidence: User said src became `[9,2,3]` after only dst was modified.
- Error type: Concept misunderstanding.
- Fix strategy: Contrast two descriptors pointing to one array with two descriptors pointing to two arrays.
- Status: Active.

## Topic: Zero-length reslicing for in-place filtering

- Date: 2026-08-09.
- Source material: Chapter 01 - Go 语言核心模型, Array / Slice filtering.
- Weakness: The learner does not yet have the prerequisite model that `s[:0]` keeps the same start pointer and capacity even though its visible length is zero.
- Evidence: Learner explicitly reported that the `s[:0]` in-place filtering explanation was not understood before attempting the prediction.
- Error type: Missing prerequisite.
- Fix strategy: Remove the loop and predicate; trace one `append` as a write at index `len(out)` into the shared backing array.
- Status: Repaired. Learner correctly predicted both the one-append bridge and the complete filtering result, including the longer alias's unchanged tail; delayed review remains.
