---
name: d-resolve-merge-conflicts
description: "Resolve an in-progress git merge, rebase or cherry-pick conflict. Use when the user says they have conflicts, that a merge/rebase/pull won't finish, that files are 'both modified', 'deleted by us/them', or asks to unblock or finish a rebase already in progress — including when they just paste git's CONFLICT output. Not for planning a merge that hasn't started yet, and not for undoing a merge that already landed."
---

## Workflow

1. **Assess the state.** Start with `git status`: it names the operation in progress ("You are currently rebasing branch X onto Y"), lists every unmerged path, and — unlike conflict markers — also surfaces non-textual conflicts (modify/delete, rename, binary). For a machine-readable list of unmerged paths, use `git diff --name-only --diff-filter=U`. If you need to double-check which operation is running, the `.git` directory is authoritative:
   - `.git/MERGE_HEAD` → merge
   - `.git/rebase-merge/` or `.git/rebase-apply/` → rebase
   - `.git/CHERRY_PICK_HEAD` → cherry-pick

   The operation determines what the `--ours`/`--theirs` flags mean and which `--continue` verb finishes the job — get this right before anything else.

2. **Inspect the three sides of each conflict.** The index holds all three versions as stages: `:1:` is the common ancestor (base), `:2:` is ours, `:3:` is theirs.

   ```
   git show :1:<file>              # base version
   git diff :1:<file> :2:<file>    # what OUR side actually changed vs base
   git diff :1:<file> :3:<file>    # what THEIR side actually changed vs base
   ```

   Diffing each side against the base is usually more decisive than reading the merged markers, because it shows what each side deliberately changed. If it helps to see the base inline, re-materialize the file with base context: `git checkout --conflict=diff3 -- <file>` (or recommend `git config merge.conflictStyle zdiff3` for future merges).

3. **Find the intent behind each side.** Use `git log -p --merge -- <file>`, which narrows history to the commits that actually caused the conflict. Note: during a rebase or cherry-pick, `--merge` requires Git ≥ 2.38; on older versions fall back to `git log -p HEAD...REBASE_HEAD -- <file>` (or `CHERRY_PICK_HEAD` accordingly). Read the commit messages. If the PR/MR or ticket is reachable, read it too; if not, git history is the source of truth — do not guess.

4. **Resolve each conflict according to the intent found in step 3** — not according to a fixed preference order. The usual outcomes are:
   - **Keep both changes**, but only when they compose semantically. Watch for silent breakage: duplicated functions, repeated imports, two additions that each assume they are the only one.
   - **Keep one side unchanged**, when the other side's change was superseded or belongs elsewhere.
   - **Rewrite only the conflicting section**, when both intents must survive but their literal texts can't.

   Never invent new behaviour. If the two intents are semantically incompatible and the correct outcome isn't derivable from the history, **stop and ask** rather than choosing.

5. **Handle non-textual conflicts explicitly** (they have no markers and won't appear in a marker grep):
   - **Modify/delete**: decide from intent whether the file should live or die. Keep it with `git checkout --ours|--theirs -- <file>` then `git add <file>`; drop it with `git rm <file>`.
   - **Rename/rename or add/add on the same path**: pick or merge the content, `git add` the surviving path, `git rm` the other.
   - **Binary files**: pick a whole side with `git checkout --ours|--theirs -- <file>` and `git add`, or regenerate the file from source. Never attempt a textual merge.

6. **Verify.** Run `git diff --check`, which catches leftover conflict markers — including the `|||||||` base marker from diff3 style that a naive grep misses. Then run the project's own checks — typically typecheck, then tests, then format — and fix only what the merge broke.

7. **Finish.** Stage only the paths that were in conflict, confirm nothing unmerged remains (`git diff --name-only --diff-filter=U` prints nothing), then continue with the verb matching step 1: `git merge --continue`, `git rebase --continue`, or `git cherry-pick --continue`. For a rebase, repeat from step 1 for each remaining commit.

## When to stop instead of resolving

Don't abort just because a conflict is hard. Do stop and hand it back to the user when:

- The merge or rebase itself looks wrong (wrong base, wrong target branch, wrong commit range).
- Resolving would require a product decision, not a code decision.
- The conflicts suggest the branch should be re-based on a different commit, or re-created.

When handing back, leave the in-progress state exactly as it is — do not abort on your own initiative. Summarize what you found, and remind the user that `git merge --abort`, `git rebase --abort`, or `git cherry-pick --abort` will return the worktree to its pre-operation state if they'd rather start over.

## Make the smallest correct change

- Modify only the lines required to resolve the conflict.
- No opportunistic refactors, renames, reformatting of unrelated code, or fixes to unrelated bugs.
- Leave surrounding code exactly as it was.
- Stage the conflicted paths explicitly. Never stage the whole worktree.

## `--ours` / `--theirs` mean opposite things in rebase

- **Merge:** `--ours` = the branch you are on, `--theirs` = the branch being merged in.
- **Rebase / cherry-pick:** `--ours` = the upstream branch you are replaying onto, `--theirs` = your own commits.

Confirm which operation is in progress (step 1) before using either flag.

## Handle generated files correctly

Never hand-resolve conflicts in generated or derived files. Instead: resolve the corresponding source files, delete the artifact if needed, and regenerate it with the project's normal tooling.

Applies to lockfiles, generated API clients, generated code and docs, and build artifacts.

## Prefer the project's own tooling

If the repo configures a mergetool, a wrapper script, or a VCS other than plain git, use that instead of the raw commands above.
