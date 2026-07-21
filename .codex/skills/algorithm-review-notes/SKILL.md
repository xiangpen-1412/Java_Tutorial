---
name: algorithm-review-notes
description: Create or revise balanced Chinese algorithm review notes from a problem, solution attempt, discussion, or existing note. Use when recording LeetCode or other algorithm problems, condensing overly long notes without hollowing them out, preserving reusable reasoning and examples, extracting lessons for variants, identifying conceptual mistakes, or maintaining algorithm notes and a separate errors log.
---

# Algorithm Review Notes

Write as a teacher preparing material for future review. Condense repetition, not understanding. Preserve enough context and reasoning that the learner can reconstruct the idea months later without reopening the original explanation.

## Workflow

1. Identify the platform and exact problem number and title. Verify them from reliable context or an authoritative source when available. Never guess; mark missing information clearly.
2. Read the entire existing note and the learner's attempt before editing. First classify material as keep, condense, move to Errors, or delete.
3. Preserve the learning chain: what the problem asks, what clue identifies the pattern, how the problem is transformed, why the core property works, how a variant changes the reasoning, and what the learner misunderstood.
4. Remove repetition, multiple examples proving the same point, line-by-line code narration, generic encouragement, and routine syntax slips. Do not remove unique explanations merely to shorten the file.
5. Re-read the result from the perspective of the learner returning months later. If a bullet is only a slogan and no longer explains why it is useful, restore enough context.

## Output template

```markdown
## [平台] 题号. 题目名称

### 问题
[用一个完整段落说明输入、目标、关键条件，以及容易误读的地方。]

### 核心收获
- **识别信号**：[看到哪些条件应联想到什么模型，并说明理由。]
- **核心转换**：[问题如何被转成更熟悉的结构；保留必要的推导或小例子。]
- **通用方法**：[状态、不变量、边界或遍历含义，以及它为何成立。]

### 变种与注意点
- [条件变化后，原方法的哪部分还能复用、哪部分必须改变。]
- [真正重要的边界、复杂度取舍或实现风险。]

### 我的思维误区
- [本次真实出现的错误认知 → 为什么错 → 下次应如何判断。]
```

Treat this template as an organizational guide, not a quota. Add or merge sections and use as many points as the actual problem requires. Merge `变种与注意点` into `核心收获` when the note is simple. Omit `我的思维误区` when there is no evidenced conceptual mistake. Never fabricate one.

## Content rules

- Give the exact number/title and a self-contained problem statement highest priority.
- Preserve one useful example, diagram, formula, counterexample, or short pseudocode fragment when it materially helps recall the reasoning. Keep more only when they teach different ideas.
- Explain the core property or invariant in enough detail to answer “why can this be reused?” Do not reduce reasoning to labels such as “use sliding window” or “do inorder traversal.”
- Preserve learner-specific reasoning, questions, failed approaches, and corrections when they reveal a conceptual gap. Describe each mistake as a faulty belief or decision, why it fails, and the replacement judgment.
- Put serious, reusable language or API misunderstandings in the user's separate Errors document or section. Exclude routine slips such as missing imports, forgetting `new`, ordinary uninitialized containers, typos, or missed assignments unless explicitly requested.
- Do not turn the note into a model answer. Usually omit full solution code and line-by-line narration, but retain a small structural snippet or pseudocode when it is the clearest way to preserve an invariant or state transition.
- Mention complexity when it explains a meaningful tradeoff, exposes repeated work, or distinguishes two reusable approaches.
- Do not impose any line count, word count, retention percentage, section count, or bullet count. Let the problem and the learner's actual experience determine the length. Include every point that materially helps future understanding, recognition, transfer, or error correction; remove only repetition, irrelevant detail, and content with no review value. Length is a consequence of the content, never a target.
- Never treat “shorter” as automatically better. A note fails if the learner can name the algorithm but cannot recover the recognition clue, reasoning, invariant, or adaptation to a variant.

## Compression test

Before finalizing, verify that the note lets the learner answer all of these without reopening the old version:

1. What exactly is the problem asking, including decisive constraints?
2. What clues reveal the underlying pattern?
3. What is the core transformation or invariant, and why does it work?
4. What changes in a nearby variant?
5. What genuine mistake did the learner make, if any?

If any answer disappeared during editing, the note was over-compressed.

## Editing files

- Inspect and preserve the document's existing naming, links, useful diagrams, formulas, examples, and surrounding organization. Keep algorithm insights in the algorithm note and qualifying language/API misunderstandings in the existing Errors location.
- Prefer focused edits over deleting and recreating the whole note. Never discard unique content solely to force every note into an identical size or bullet count.
- For a request affecting more than five existing notes, first make one simple and one complex representative sample. Show the user the resulting level of detail and wait for explicit approval before applying it to the remaining files.
- Before bulk editing, confirm that the original content is recoverable through version control or another safe mechanism. Afterward, compare file counts, links, and representative diffs; do not declare success based only on headings or line counts.
- Do not reorganize unrelated notes without permission.
