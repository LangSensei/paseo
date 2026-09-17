# Diff Find on current main

Feature commit: `d4796922c9f00211d4405283f29235237231d36d`.
Base: `047f40e62356f091a081be6b4b9db32872957d0c`.

This is the shared PaneFind implementation rebased onto main after #4589, #4650 and #4765 merged. The feature is one commit; the old replay stack and local test-runner configuration are not included.

## Verified

- All-workspace typecheck and lint passed; formatting checked on the restored, publishable tree.
- 97 tests passed in seven targeted files: Diff Find model, diff document geometry, hit testing, range geometry, web painting, native hit testing and locale resources.
- 16 real browser scenarios passed across runs: seven Diff Find flows (including file/chat/terminal coexistence), commit diff layout/Find, two original clipboard/review flows, four file Find regressions, one original chat Find flow and one original terminal Find flow.
- The final batch recorded 15 passes and a first-worker setup timeout before its scenario ran. That scenario then passed unchanged on a targeted rerun. See both logs below.

## Raw command output

Only local checkout paths have been replaced with `$WORKTREE`/`$DELIVERY`. These are not synthetic test summaries.

- [Build, lint, typecheck, 97 unit tests](static-and-unit.txt)
- [Final 16-case browser batch, including setup timeout](browser-batch.txt)
- [Unchanged targeted rerun of the remaining scenario](browser-rerun.txt)

Local startup used a temporary optional harness patch for a longer Metro warmup; it was removed from the publishable branch. Functional assertions were unchanged. Cold prewarming ran separately from the browser tests. The test service had a 3GiB group hard cap; final runs recorded no cgroup OOM kills.

## Screenshots / recording

All content below comes from generated test workspaces.

### Working diff

![Working diff Find](working-diff.png)

### Independent Diff and chat Find

![Diff and chat Find](diff-chat-find.png)

[Browser recording](diff-chat-find.webm)

The test explicitly focuses the chat content host after activating that pane; existing composer autofocus on chat activation is not changed by this PR.

### Independent Diff and terminal Find

![Diff and terminal Find](diff-terminal-find.png)

## Scope and limitations

Runtime verification was Linux Chromium, including narrow browser layout. Actual Electron, macOS/Windows and native devices were not exercised. Native Find is not added. The shared native hit-testing unit tests pass.

Find searches canonical diff text already delivered to the client, not full-file context or filenames. Matching is literal, case-insensitive and single-line; only the first 10,000 occurrences are navigable. Binary/oversized files are excluded with a visible notice. No regex, replace, Viewed markers or context expansion.

A prior matching-only fixture (21.2M characters / 53,250 lines / 213 files) was rerun on the unchanged search engine during rebase validation: approximately 279ms cold including debounce, 2.7ms cached update, with a longest callback of 21ms on the constrained host. This is not a whole-renderer benchmark. The four-millisecond scheduler check is a cooperative budget, not a guarantee against individual long operations or OS descheduling.
