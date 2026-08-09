# Review Schedule

## 2026-08-10 - Receiver Mutation Semantics

- Topic: Value receiver copies versus pointer receiver mutation.
- Reason: Learner correctly predicted the original counter changes only through the pointer receiver.
- Review timing: 2026-08-13.
- Review task: Compare one value-receiver mutation and one pointer-receiver mutation on the same struct.
- Status: Scheduled.

## 2026-08-10 - Variadic Slice Expansion

- Topic: Existing slice expansion and backing-array sharing.
- Reason: Learner correctly predicted propagation through `s...` and identified the shared backing array.
- Review timing: 2026-08-13.
- Review task: Compare `f(1, 2)` with `f(s...)` when the variadic function mutates its first element.
- Status: Scheduled.

## 2026-08-10 - Defer and Return Slots

- Topic: Named result slots versus ordinary local variables during deferred execution.
- Reason: Learner correctly traced both functions and identified which object each defer modified.
- Review timing: 2026-08-13.
- Review task: Compare one named-result function with one unnamed-result function whose defer mutates a local variable.
- Status: Scheduled.

## 2026-08-10 - Closure Capture Environments

- Topic: Persistent captured state and independence across factory calls.
- Reason: Learner correctly traced repeated calls to one closure and a separate closure on the first transfer.
- Review timing: 2026-08-13.
- Review task: Predict interleaved calls to two counters created by separate factory invocations.
- Status: Scheduled.

## 2026-08-10 - Struct Alignment and Padding

- Topic: Field order, internal padding, and tail padding.
- Reason: Learner correctly estimated the 24-byte and 16-byte layouts and located their padding.
- Review timing: 2026-08-13.
- Review task: Compare two field orders containing `byte`, `int64`, and `bool`, then estimate total size.
- Status: Scheduled.

## 2026-08-10 - Embedding versus Inheritance

- Topic: Promoted methods, interface satisfaction, and concrete type identity.
- Reason: Learner correctly distinguished interface satisfaction from concrete `Logger` assignment on the first comparison.
- Review timing: 2026-08-13.
- Review task: Compare a promoted method call, interface assignment, and assignment to the embedded concrete type.
- Status: Scheduled.

## 2026-08-10 - JSON Visibility and Omitempty

- Topic: Export eligibility versus omission of an empty exported field.
- Reason: Learner correctly handled field visibility but did not apply `omitempty` or state the final encoded object.
- Review timing: Same-day targeted repair; if correct, review again on 2026-08-13.
- Review task: Predict JSON for one exported empty field with `omitempty` and one unexported field with a tag.
- Status: Repaired; delayed review scheduled for 2026-08-13.

## 2026-08-10 - Struct Copy with Reference Fields

- Topic: Outer struct value copy versus shared nested slice storage.
- Reason: Learner correctly separated a reassigned string field from a mutated shared slice field on the first transfer.
- Review timing: 2026-08-13.
- Review task: Copy a struct containing one scalar and one map or slice field, then predict both mutations.
- Status: Scheduled.

## 2026-08-10 - Map Role Selection

- Topic: Choosing set, counter, or index map shapes.
- Reason: Learner correctly selected and implemented `map[string]struct{}` for membership-only storage.
- Review timing: 2026-08-13.
- Review task: Choose map value shapes for membership, frequency counting, and ID-to-object lookup.
- Status: Scheduled.

## 2026-08-10 - Stable Map Traversal

- Topic: Deterministic output from an unordered map.
- Reason: Learner correctly selected `collect keys -> sort -> lookup` on the first transfer.
- Review timing: 2026-08-13.
- Review task: Repair a flaky test that directly compares map range output order.
- Status: Scheduled.

## 2026-08-10 - Map Struct Value Write-Back

- Topic: Local struct copy versus the value stored in a map.
- Reason: Learner treated mutation of the local copy as an implicit write-back to the map.
- Review timing: Same-day targeted repair; if correct, review again on 2026-08-13.
- Review task: Predict the local field and map field before and after an explicit `m[key] = value` assignment.
- Status: Repaired; delayed review scheduled for 2026-08-13.

## 2026-08-09 - Concurrent Map Access

- Topic: Data race and shared locking discipline for ordinary maps.
- Reason: Unsafe classification was correct, but the mechanism was described vaguely as dirty data rather than an unsynchronized data race with possible runtime fatal error.
- Review timing: Repaired on 2026-08-10; delayed review on 2026-08-13.
- Review task: Diagnose a map where writers lock but readers bypass the lock.
- Status: Repaired; delayed review scheduled.

## 2026-08-09 - Map Key Comparability

- Topic: Recursive comparability of map key types.
- Reason: First struct-key transfer was correct; delayed recall should confirm that every struct field must be comparable.
- Review timing: 2026-08-12.
- Review task: Compare a struct containing an array field with one containing a slice field, then choose which can be a map key.
- Status: Scheduled.

## 2026-08-09 - Nil Map Operations

- Topic: Read, delete, and write boundaries on a nil map.
- Reason: Lookup and write failure were recognized, but delete was marked unsafe and key absence was confused with missing writable storage.
- Review timing: Same-day targeted repair; if correct, review again on 2026-08-12.
- Review task: Predict `delete` before initialization, then initialize with `make` and perform one increment.
- Status: Repaired; delayed review scheduled for 2026-08-12.

## 2026-06-05

- Topic: Breaking Go import cycles.
- Reason: User can identify the cycle but needs concrete refactoring procedure.
- Review timing: Same day after the next successful package-structure exercise.
- Review task: Given `api -> service -> repo -> service`, choose whether to extract `model`, extract `ports`, or move logic upward.
- Status: First follow-up correct; review once more after variables/zero-value section.

## 2026-08-08

- Topic: Go zero values.
- Reason: Newly forming schema; recall check was correct, application boundary still needs testing.
- Review timing: 2026-08-11.
- Review task: Predict which operations are safe on a nil slice and a nil map, then explain the difference.
- Status: Scheduled.

## 2026-08-08 - Chapter 02

- Topic: Slice queue backing-array lifetime.
- Reason: Newly introduced schema; learner has not yet demonstrated the distinction between logical dequeue and memory release.
- Review timing: Same day after the first repair or successful check; then 2026-08-11 if correct.
- Review task: Diagnose a long-lived `[]*Job` queue whose length shrinks while memory stays high, and choose between clearing slots, periodic compaction, and a ring buffer.
- Status: Pending initial check.

## 2026-08-08 - Shadowing

- Topic: `:=` shadowing across nested scopes.
- Reason: Learner predicted nil but confused the surviving outer `err` with the expired inner `err`.
- Review timing: Same day repair and next-day review on 2026-08-09 because the boundary was missed again.
- Review task: Diagnose a function that logs an inner error but accidentally returns an outer nil error.
- Status: Repaired; delayed review scheduled for 2026-08-11.

## 2026-08-08 - Constants

- Topic: Typed versus untyped constants.
- Reason: Learner incorrectly extended untyped contextual adaptation to a typed `int` constant.
- Review timing: Same-day repair; if correct, review again on 2026-08-11.
- Review task: Compare two constants that differ only by an explicit type and decide whether conversion is required.
- Status: Repaired; delayed review scheduled for 2026-08-11.

## 2026-08-08 - Iota

- Topic: Zero-based `iota` sequence and skipped identifiers.
- Reason: Learner started at 1 and did not preserve the `_` line's increment.
- Review timing: Same-day repair; if correct, review again on 2026-08-11.
- Review task: Predict values in a block that explicitly discards `iota == 0` before naming constants.
- Status: Repaired; delayed review scheduled for 2026-08-11.

## 2026-08-08 - Enum Compatibility

- Topic: Reading persisted enum numbers after changing `iota` order.
- Reason: Learner understands the business risk but followed Paid's new number instead of the unchanged stored integer.
- Review timing: Same-day repair; if correct, review again on 2026-08-11.
- Review task: Trace one old stored integer through an updated enum mapping and name the status new code observes.
- Status: Repaired; delayed review scheduled for 2026-08-11.

## 2026-08-08 - UTF-8 Strings

- Topic: Byte length versus rune count.
- Reason: Newly forming schema; first mixed ASCII/Chinese prediction was correct.
- Review timing: 2026-08-11 after the indexing boundary is checked.
- Review task: Predict `len`, `range` count, and the meaning of `s[0]` for a mixed UTF-8 string.
- Status: Scheduled.

## 2026-08-08 - String and Byte Conversion

- Topic: Immutable string versus mutable `[]byte`.
- Reason: Newly forming schema; mutation prediction was correct.
- Review timing: 2026-08-11.
- Review task: Predict whether modifying a converted byte slice changes the original string and name the likely conversion cost.
- Status: Scheduled.

## 2026-08-08 - Builder versus Buffer

- Topic: Selecting a text builder or byte buffer.
- Reason: Newly forming tool-choice schema; first scenario was correct.
- Review timing: 2026-08-11.
- Review task: Choose between `strings.Builder` and `bytes.Buffer` for one text-only case and one I/O byte-stream case.
- Status: Scheduled.

## 2026-08-08 - Array Value Semantics

- Topic: Array assignment copies all elements.
- Reason: Newly forming schema; first copy prediction was correct.
- Review timing: 2026-08-11.
- Review task: Predict whether a function receiving an array by value can modify the caller's array.
- Status: Scheduled.

## 2026-08-08 - Slice Sharing

- Topic: Slice descriptor and shared backing-array mutation.
- Reason: Newly forming schema; direct mutation prediction was correct.
- Review timing: Same-day repair; if correct, review again on 2026-08-11.
- Review task: Compare array assignment, slice reslicing, and append with available capacity.
- Status: Both branches repaired; delayed review scheduled for 2026-08-11.

## 2026-08-08 - Nil versus Empty Slice

- Topic: Zero length, nil identity, and JSON representation.
- Reason: Newly formed schema; length, identity, and JSON checks were correct.
- Review timing: 2026-08-11.
- Review task: Predict len, nil comparison, append safety, and JSON output for nil and empty slices.
- Status: Scheduled.

## 2026-08-08 - Slice Copy

- Topic: Direct assignment versus `make + copy`.
- Reason: Learner incorrectly propagated a dst mutation back to src after independent allocation.
- Review timing: Same-day repair; if correct, review again on 2026-08-11.
- Review task: Choose which of two copy patterns shares the top-level backing array, then predict both slices after mutation.
- Status: Top-level separation and nested shallow-copy boundary repaired; delayed comparison review scheduled for 2026-08-11.

## 2026-08-08 - Three-Index Slice

- Topic: Restricting append reuse with `s[low:high:max]`.
- Reason: Learner correctly predicted allocation when the appended length exceeded `max-low`.
- Review timing: 2026-08-11.
- Review task: Compare `a[1:3]` with `a[1:3:3]`, then predict whether one append changes `a[3]`.
- Status: Scheduled.

## 2026-08-09 - Slice Deletion

- Topic: Ordered deletion through `append(s[:i], s[i+1:]...)`.
- Reason: Learner correctly distinguished the shortened result descriptor from a longer alias observing the mutated backing array.
- Review timing: 2026-08-12.
- Review task: Delete one middle element while another slice aliases the same array; predict both visible slices and the duplicated tail slot.
- Status: Scheduled.

## 2026-08-09 - Zero-Length Reslicing

- Topic: `s[:0]` retains the start pointer and capacity.
- Reason: Learner reported not understanding how an empty-looking slice can overwrite the original array during filtering.
- Review timing: Same-day prerequisite repair; schedule the next review after a correct independent prediction.
- Review task: Trace one append to a zero-length reslice before returning to a filtering loop.
- Status: Prerequisite and parent filtering transfer repaired; delayed review scheduled for 2026-08-12.

## 2026-08-09 - In-Place Filtering Invariant

- Topic: `writeIndex <= readIndex` during left-compacting filtering.
- Reason: Learner correctly explained the invariant without relying only on a concrete trace.
- Review timing: 2026-08-16.
- Review task: Explain in one sentence why writing retained elements back into the same array cannot overwrite unread elements.
- Status: Stable; weekly review scheduled.

## 2026-08-09 - Pointer Slice Tail Cleanup

- Topic: Residual references after shortening an in-place filtered slice.
- Reason: Learner correctly distinguished the shortened result from a longer alias that still contains the filtered pointer in its tail.
- Review timing: 2026-08-12.
- Review task: Predict the backing-array tail before and after `clear`, then explain why object collection is still conditional on other references and GC timing.
- Status: Scheduled.
