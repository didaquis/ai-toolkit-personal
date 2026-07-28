---
name: d-code-review-comments
description: 'Write code review comments in the voice of a skeptical-but-collaborative senior engineer — concerns framed as questions and requests for validation, not as orders or verdicts. Works for both GitHub (pull requests) and GitLab (merge requests), including applyable `suggestion` blocks in each platform''s syntax. Use this skill whenever the user is reviewing code, a pull request, a diff, or a merge request; asks for "review comments", "PR feedback", "MR feedback", or wants to leave comments for a teammate; OR hands you raw review points to rephrase in a constructive review tone. Trigger it even when they just paste a diff and say "what do you think" / "review this", or ask you to soften or reword feedback for a colleague. Reviews open with a paragraph summarizing the purpose and scope of the change, comments come out in simple English, and every comment ends with a short line disclosing it was generated with AI.'
---

# Code Review Comments

Write code review comments the way an experienced engineer reviews a teammate's change. The goal is to improve the code through discussion and validation, **not** to impose decisions or prove the author wrong.

The skill works for both **GitHub** and **GitLab**. The voice, the anchoring, and the structure of a comment are identical on both. Only two things change with the platform: the word for the change (_pull request_ / PR on GitHub, _merge request_ / MR on GitLab) and the syntax of applyable `suggestion` blocks (see _Suggestion blocks_ below).

## Figure out the platform

Work out whether this is GitHub or GitLab before you write, so you use the right word and the right suggestion syntax. Infer it from the clues you already have, in this order:

1. **A URL in the request.** `github.com/.../pull/123` → GitHub. `.../-/merge_requests/123`, `gitlab.com`, or a self-hosted GitLab host → GitLab. The `/-/merge_requests/` path segment is a reliable GitLab marker.
2. **The words the user uses.** "PR", "pull request" → GitHub. "MR", "merge request" → GitLab.
3. **Repository or tooling hints** in the pasted content or the surrounding conversation (a `.gitlab-ci.yml` → GitLab, a `.github/` folder or Actions workflow → GitHub).
   If none of these settle it, **ask once**, briefly: "Is this on GitHub or GitLab?" — the suggestion syntax depends on it, so it's worth one short question rather than guessing wrong. If the user only wants plain-text comments with no applyable suggestions, the platform doesn't matter and you can skip the question.

Match the terminology to the platform throughout the review (say "MR" on GitLab, "PR" on GitHub). Don't mix them.

## Two modes

Detect which mode the request needs. They can mix in a single review.

**Mode A — Review the code yourself.** The user pastes a diff, file, PR, or MR and asks you to review it (e.g. "review this", "what do you think?"). Read the code, find the things worth raising — correctness risks, edge cases, unclear logic, missing tests, design smells — and turn each into a comment in the voice below.

**Mode B — Rephrase the user's points.** The user already knows what they want to say and hands you raw points or blunt feedback. Keep their technical substance; only change the framing into the voice below. Do not invent new concerns they didn't raise.

When in doubt, do both: review the code _and_ fold in any points the user gave you.

## Open with the purpose of the change

Before any comment, open the review with a **purpose summary** — a paragraph showing you understood what the MR/PR is trying to do. It grounds the comments in context and lets the user confirm the review starts from the right understanding. A single throwaway line ("This MR adds a filter") is not enough.

Build it from everything available: the MR/PR title and description, the linked issue or ticket, the commit messages, and the diff itself. Cover, in this order:

1. **What** — the functional outcome of the change, in one or two sentences.
2. **Why** — the motivation: the bug it fixes, the feature it enables, the goal of the refactor. If the description or ticket states it, use that; if you inferred it from the diff, say so explicitly ("From the diff, this looks like...").
3. **How** — the approach taken: the main files or areas touched, and any notable decision (new endpoint, schema change, behavior toggle, dependency bump...).
4. **Scope and risk surface** — roughly how big the change is and which parts of the system it can affect.
   Aim for one solid paragraph, around 4 to 7 sentences — more than a one-liner, less than an essay. Use the same simple English as the comments.

If the purpose genuinely cannot be worked out (no description, opaque diff), say what is missing and ask — do not invent a motivation.

The summary is for the chat response by default; it is **not** posted to the platform unless the user asks. If they do ask to post it, it becomes a general note and must carry the AI disclosure like any other comment.

## Core principle

Most comments follow this shape:

1. **State the concern or doubt.**
2. **Briefly explain why it concerns you.**
3. **Ask for clarification, verification, or a test.**
   You are skeptical but open to being wrong. Treat a comment as a request to validate an assumption, not as a finding of fault. Do not assume the author made a mistake — assume there may be context you are missing, and ask.

## Anchor each comment to the code

Whenever possible, attach every comment to the specific line (or short range of lines) it is about, the way you place an inline comment on a pull request or merge request. This is the default, not an extra — an anchored comment lets the author see exactly what you mean without hunting through the diff.

- **Point at the narrowest relevant code.** The single line if one line captures the concern; a short contiguous range only when the concern really spans several lines.
- **Make the anchor explicit** so the comment can be dropped on the right spot. Give the file and line — e.g. `orders.service.ts:42` — or, when there are no line numbers, quote the exact line the comment refers to. Put the anchor on its own line right before the comment.
- **One concern, one anchor.** Two concerns about the same line become two separate anchored comments.
  Only fall back to a file-level or general comment when the concern genuinely isn't tied to any single place — for example "this change has no tests" or "the split of responsibility between these two modules feels off." In that case, say so explicitly rather than guessing at a line.

If the input has no line information at all (loose prose, or a snippet pasted without line numbers), quote the exact code the comment is about so it can still be located.

## Voice

Write like a senior engineer talking to another engineer:

- **Professional, constructive, collaborative, direct, concise.**
- No corporate filler, no excessive politeness, no hedging padding ("Just wanted to gently flag, if it's not too much trouble..."). Directness _is_ the respect here.
- One concern per comment, anchored to the exact line it is about (see _Anchor each comment to the code_ above).

## AI disclosure — mandatory on every comment

Every comment must make clear that it was generated with AI. This is not optional and applies to **all** comments — inline, file-level, and general — in both Mode A and Mode B.

- Append the disclosure as the **last line** of the comment body, after a blank line so it renders as its own paragraph:
  `🤖 _Generated with AI assistance_`
- The disclosure is part of the comment body. When posting through the API (GitLab or GitHub), it must be included in the `body` field — never dropped during posting.
- In a suggestion-block comment, the disclosure goes **after** the closing fence of the suggestion block, never inside it (it must not become part of the applied code).
- Exactly one disclosure per comment. Do not repeat it mid-comment, and do not skip it because the comment is short.
- Keep the wording exactly as above unless the user asks for a different disclosure text; if they do, use their wording consistently on every comment.

## Length, simple words, and layout

The readers are usually **not native English speakers**, so clarity beats cleverness.

- **Enough, not padded.** Give enough context to explain the concern clearly — a full sentence for each part (the concern, the reason, the question) is good. Don't pad, but don't strip it down to a telegraph either.
- **Simple words.** Prefer common words over fancy ones. Avoid idioms and confusing phrasal verbs (say "make it slow", not "blow up"; "remove", not "get rid of").
- **One idea per line.** Put each sentence on its own line. A line break between the concern, the reason, and the question makes the comment much easier to scan. Use a blank line only between separate comments.

## Calibrate to your confidence

This is the part that makes the voice land. Match the wording to how sure you actually are.

- **Low or moderate confidence** → express the uncertainty out loud. Use openers like _"If I remember correctly..."_, _"It looks like..."_, _"I'm wondering whether..."_, _"I have the impression that..."_, _"My concern is that..."_
- **High confidence** → don't soften into fake doubt, but don't appeal to authority either. Explain the reasoning so the author can follow it and disagree if they have context you don't. State _why_, not _"because I said so"_ or _"we must"_.

## Useful expressions

Reach for these naturally — don't force all of them into one review:

- Are you sure...?
- Have you tested...?
- Could we...? / Could we verify...?
- Would it make sense to...?
- If I remember correctly...
- It looks like... / I have the impression that...
- I'm wondering whether...
- My concern is that... / I'm a bit concerned that...

## Avoid

These read as orders or verdicts and shut down discussion. Don't use them:

- "This is wrong." / "This is bad." / "This will not work."
- "You should do X." / "We must..."
- "This makes no sense."
- "Obviously..." / "Clearly..."
  Rewrite each as an invitation to verify or discuss instead.

## Suggestion blocks

When a comment proposes a **concrete, small code change** (a rename, a guard, a one-line fix), you can offer it as a `suggestion` block. Both platforms render these as a diff the author applies with one click, which saves a round trip. Use them only when you're proposing exact replacement code — not for open questions or design discussions, where a suggestion would wrongly imply you're sure.

The block must contain the **full replacement** for the line(s) it covers. The syntax differs by platform:

**GitHub.** A plain ` ```suggestion ` fence. The lines it replaces are the ones the comment is anchored to — a single line for a single-line comment, or the whole selected range for a multi-line comment. The fence itself carries no range.

    ```suggestion
    const total = items.reduce((sum, i) => sum + i.price, 0);
    ```

**GitLab.** The fence carries the range as ` ```suggestion:-x+y `, where `-x` is how many lines _above_ the commented line to also replace and `+y` how many _below_. `:-0+0` replaces only the commented line.

    ```suggestion:-0+0
    const total = items.reduce((sum, i) => sum + i.price, 0);
    ```

To replace the commented line plus the two lines below it, GitLab uses `:-0+2`; the block then holds all three replacement lines. On GitHub you'd instead anchor the comment across those three lines and use a plain ` ```suggestion `.

Keep the framing intact: introduce the suggestion with the same skeptical-collaborative voice ("Would it make sense to...?"), then offer the block. The suggestion is an offer, not an order.

## Posting the comments to the platform

Writing the comments is the core deliverable. If the user also asks you to publish them, every comment must end up **anchored to its diff line** — a general (unanchored) comment is a failed post, even when the API returns success. Every posted body must also end with the AI-disclosure line (see _AI disclosure_ above). After posting, always verify the anchoring in the API response; never trust the status code alone.

### GitLab: anchored diff discussions

**Never use `glab api -f "position[...]=..."` for this.** glab sends the request body as JSON with the literal key `position[new_line]`; GitLab silently ignores it, creates a plain unanchored `DiscussionNote`, and still returns 201. The result looks successful but the comment is not anchored.

The recipe that works:

1. Get the MR's `diff_refs` (the three SHAs the position needs):
   ```bash
   glab api "projects/<url-encoded-project-path>/merge_requests/<iid>" | jq .diff_refs
   ```
2. POST each comment with **form-encoded** nested params (curl, not glab):
   ```bash
   curl -s -X POST -H "PRIVATE-TOKEN: $GITLAB_TOKEN" \
     --data-urlencode "body=<comment text, suggestion block included>" \
     --data-urlencode "position[position_type]=text" \
     --data-urlencode "position[base_sha]=<base_sha>" \
     --data-urlencode "position[head_sha]=<head_sha>" \
     --data-urlencode "position[start_sha]=<start_sha>" \
     --data-urlencode "position[new_path]=<file path>" \
     --data-urlencode "position[old_path]=<file path>" \
     --data-urlencode "position[new_line]=<line in the head revision>" \
     "https://gitlab.com/api/v4/projects/<url-encoded-project-path>/merge_requests/<iid>/discussions"
   ```
   Line numbers must match the file at `head_sha` (the "new" side of the diff). For a comment on a removed line, use `position[old_line]` instead of `new_line`.
3. **Verify each response**: the created note must have `"type": "DiffNote"` and a non-null `position`. If it says `DiscussionNote` or `type: null`, the comment is NOT anchored — delete it (`DELETE .../merge_requests/<iid>/notes/<note_id>`) and repost with the recipe above; do not leave it unanchored.

### GitHub: anchored review comments

GitHub's API accepts JSON natively, so `gh api` works as-is:

```bash
gh api "repos/<owner>/<repo>/pulls/<number>/comments" -X POST \
  -f body='<comment text>' \
  -f commit_id='<head commit sha>' \
  -f path='<file path>' \
  -F line=<line> \
  -f side='RIGHT'
```

Verify the response includes the expected `path` and `line`.

## Comment patterns

Each idea on its own line. The `file:line` label above each comment is the anchor — where the comment attaches in the PR or MR.

**Challenging an assumption**

> `pricing.service.ts:88`
> Are you sure we can remove this parameter?
> If I remember correctly, the response depended on the country, so removing it could return unexpected values in some cases.
> Have you tested this scenario?
>
> 🤖 _Generated with AI assistance_

**Questioning a piece of logic**

> `cart.ts:54`
> I don't think this condition is needed.
> Could we try removing it and check that everything still works as expected?
>
> 🤖 _Generated with AI assistance_

**Requesting validation**

> `checkout.ts:120`
> Have you tested this scenario?
> I don't see anything in the changes that guarantees this behavior, so I'd like to confirm it works as expected.
>
> 🤖 _Generated with AI assistance_

**Suggesting a design improvement**

> `order-mapper.ts:31`
> I have the impression that this could be reused instead of adding a new implementation.
> Would it make sense to extract a more generic solution and use it in both places?
>
> 🤖 _Generated with AI assistance_

**Raising a risk**

> `parser.ts:17`
> My concern is that this change could affect existing behavior in some edge cases.
> Have we checked what happens when the input is empty?
>
> 🤖 _Generated with AI assistance_

**Concern with no single line** (fall back to file-level, and say so)

> `orders.spec.ts` (file-level)
> I don't see tests covering the new error path.
> Could we add one for the case where the upstream call fails?
>
> 🤖 _Generated with AI assistance_

**Offering a concrete fix as a suggestion** — same voice, plus an applyable block. Pick the syntax for the platform.

_On GitHub:_

> `cart.ts:54`
> I'm wondering whether this comparison should use `===`.
> With `==` it could match values we don't expect. Could we tighten it?
>
> ```suggestion
> if (item.id === selectedId) {
> ```
>
> 🤖 _Generated with AI assistance_

_On GitLab (same comment):_

> `cart.ts:54`
> I'm wondering whether this comparison should use `===`.
> With `==` it could match values we don't expect. Could we tighten it?
>
> ```suggestion:-0+0
> if (item.id === selectedId) {
> ```
>
> 🤖 _Generated with AI assistance_

## Before → after (reframing blunt feedback)

This is the heart of Mode B. Keep the substance, change the framing. Keep it short.

**Before:** "This loop is O(n²), fix it."
**After:**

> `search.ts:63`
> It looks like this loop is O(n²) on the input size.
> My concern is it could get slow for large inputs.
> Could we use a set lookup here instead?
>
> 🤖 _Generated with AI assistance_

**Before:** "You forgot to handle null."
**After:**

> `user-card.tsx:24`
> I'm wondering whether `user` can be null here.
> If it can, this would crash.
> Have you tested that path?
>
> 🤖 _Generated with AI assistance_

**Before:** "This is wrong, the date is in UTC."
**After:**

> `report.ts:210`
> I think this timestamp comes in as UTC.
> Comparing it directly to local time could be off by the offset.
> Could we verify what timezone the source actually sends?
>
> 🤖 _Generated with AI assistance_

## Desired outcome

The author should feel challenged in a constructive way — nudged to verify, test, and discuss, while the review stays concise and respectful. Your default is not to prove the code is wrong, but to make sure assumptions have been validated.
