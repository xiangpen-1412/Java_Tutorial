---
name: algorithm-review-notes
description: Create or revise concise Chinese algorithm review notes from a problem, solution attempt, discussion, or existing note. Use when recording LeetCode or other algorithm problems, summarizing what was learned, extracting reusable reasoning for variants, identifying conceptual mistakes, or maintaining algorithm notes and a separate errors log.
---

# Algorithm Review Notes

Write as a teacher preparing material for future review. Optimize for recognition and transfer, not for preserving a full solution explanation.

## Workflow

1. Identify the platform and exact problem number and title. Verify them from reliable context or an authoritative source when available. Never guess; mark missing information clearly.
2. State the problem in plain Chinese: input, required output, and decisive constraints or properties. Make it understandable without reopening the original page.
3. Read the learner's attempt before diagnosing mistakes. Do not infer a mistake merely because another implementation is shorter.
4. Extract knowledge that survives a changed surface form: recognition cues, invariants, ordering properties, state definitions, traversal meaning, proof intuition, and an approach to variants.

## Output template

```markdown
## [平台] 题号. 题目名称

### 问题
[输入是什么、要求什么、关键条件是什么]

### 核心收获
- [可迁移到同类题或变种的识别信号与思考角度]
- [核心性质、通用策略，以及为什么可以这样判断]
- [必要的边界条件、复杂度意识或实现注意点]

### 我的思维误区
- [本次真实出现的思维、建模、算法选择或边界理解错误]
```

Omit `我的思维误区` when there is no evidenced conceptual mistake. Never fabricate one.

## Content rules

- Give the exact number/title and a self-contained problem statement highest priority.
- Describe mistakes as a faulty belief or decision and its correction.
- Put serious, reusable language or API misunderstandings in the user's separate Errors document or section.
- Exclude routine slips such as missing imports, forgetting `new`, ordinary uninitialized containers, typos, or missed assignments unless explicitly requested.
- Do not turn the note into a model answer. Omit full code, line-by-line explanation, and explanations of why an official answer has its exact form unless requested.
- Mention complexity only when it teaches a meaningful tradeoff or is central to the pattern.
- Prefer 2–4 dense bullets under `核心收获` and 1–3 under `我的思维误区`. Remove repetition, filler, generic definitions, and details that do not help solve a future variant.

## Editing files

Inspect and preserve the document's existing formatting, naming, links, and organization. Keep algorithm insights in the algorithm note and qualifying language/API misunderstandings in the existing Errors location. Do not reorganize unrelated notes without permission.
