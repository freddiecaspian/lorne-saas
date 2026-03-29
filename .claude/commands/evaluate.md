Evaluate a running app: quick QA check or multi-iteration design improvement loop using a fresh-context evaluator subagent.

$ARGUMENTS

## Parse arguments

- `--loop [N]`: Design iteration loop, N iterations (default 5)
- `--threshold N`: Early-stop when all criteria reach N/10 (default 8)
- `--qa`: Force single-pass QA mode
- `--url <url>`: App URL (default: auto-detect)
- Everything else is task context (what to evaluate, what to focus on)
- No flags = QA mode

## Step 0: Setup

### Find the app

Check for a running dev server: inspect running processes, `package.json` scripts, or use the provided `--url`. If the app is a static HTML file with no server running, start one:
```bash
cd <project-dir> && python3 -m http.server 8765 &
```
Playwright cannot open `file://` URLs - a local server is always required.

### Gather project context

Read in parallel:
1. **CLAUDE.md** in the project directory
2. **Branding doc** - search for `BRANDING.md`, `brand.md`, `design.md`, `style-guide.*` in project root or `/docs`. If found, this is the design source of truth.
3. **Spec/plan** - `spec.md`, `plan.md`, product plan references in CLAUDE.md
4. **Recent git** - `git log --oneline -10` to understand what was built

From these, extract: product type, intended user, core user task, brand/personality cues, constraints. Keep the summary short.

### Build project-specific criteria

If a branding doc exists, derive criteria from it. These override or supplement the defaults.

Strong criteria are specific to the project:
- "Would a Bib Gourmand restaurateur feel this was made for her, or could it be relabelled for any SaaS?"
- "Does the morning queue feel like a place to serve guests, or a Jira board in restaurant colours?"

Weak criteria are generic mush:
- "Is it nice?"
- "Is the UX intuitive?"

## QA mode

Single pass. Does the thing work?

Spawn a fresh subagent (Agent tool) with this prompt:

```
You are a QA tester. You have not seen the code. Use Playwright MCP tools only.

App URL: {url}
Context: {project context summary}

Steps:
1. Navigate to the URL. Screenshot the initial state.
2. Click every interactive element. Test primary flows.
3. Check: does each interaction do what it promises?

Report:
- PASS or FAIL (overall)
- Critical issues (broken functionality)
- Minor issues (cosmetic, non-blocking)
- Reproduction steps for each issue
- One-sentence summary

Do not praise anything. Report what works and what doesn't.
```

Fix any broken items. Report results. Done.

## Loop mode

You are the generator. A fresh subagent is the evaluator each iteration. The evaluator never sees your code - only the live app via Playwright screenshots and interaction.

### Evaluation criteria

4 criteria, scored 1-10. Weights determine the composite.

| Criterion | Weight | What it measures |
|-----------|--------|-----------------|
| **Design quality** | 0.35 | Visual hierarchy, intentional spacing, considered palette, typography that communicates. Does this look like a designer built it? |
| **Originality** | 0.30 | Could you distinguish this from a template? Does it feel made for this product and audience, not a reskinned generic? |
| **Coherence** | 0.20 | Do all pieces feel like one product? Same visual language, consistent interactions, unified mood across screens. |
| **Craft** | 0.15 | Pixel-level polish. Alignment, spacing, hover states, transitions, responsive. Weighted low - Claude handles this by default. |

If the branding doc or project context suggests additional criteria, add them and redistribute weights to sum to 1.0.

### Scoring calibration

Include these examples verbatim in every evaluator prompt. They are the single most important lever for honest scoring.

```
SCORING CALIBRATION - read this before scoring anything:

Most AI-generated UIs are a 4 or 5. That is your baseline, not 7.

3/10 Design: Default framework styling with a colour swap. Looks like create-react-app.
5/10 Design: Clean but conventional. You've seen this layout on 50 SaaS pages. Nobody complains, nobody remembers.
7/10 Design: Intentional choices. Palette that means something, typography that creates mood, spacing that guides the eye.
9/10 Design: Exhibition-grade. Every pixel has a reason. The design communicates identity before you read a word.

3/10 Originality: Swap the logo and it works for literally any product.
5/10 Originality: "Nice dashboard." Could be relabelled for crypto, HR, logistics, anything.
7/10 Originality: Design decisions rooted in the specific domain. You can tell what this product does from the aesthetics alone.
9/10 Originality: Could only be for this product. The visual language IS the brand.

If it looks like something an AI could generate in one shot from common patterns, originality cannot exceed 6.
Functional correctness does not raise design scores.
Trendy flourishes (glassmorphism, gradients, dark mode) are not originality.
```

### The iteration loop

Before starting, tell the user:
```
Design iteration loop: {N} iterations max, early stop at {threshold}/10
URL: {url}
Branding doc: {found/not found}
```

For each iteration 1 to N:

**A. Improve the app**

- Iteration 1: Best design attempt using all available context.
- Iteration 2+: Apply changes based on the refine/pivot decision from the previous iteration.

Route feedback into two buckets:
- **Code fixes** (broken layout, alignment bugs, missing states, non-functional elements) - fix directly, no discussion needed.
- **Branding decisions** (visual identity, palette direction, typography philosophy, photography vs illustration, mood) - do NOT invent these. Collect them as flags for the user. Make your best reversible guess but surface the decision.

**After making changes, commit:**
```bash
git add -A && git commit -m "evaluate: iteration {N} - {refine/pivot} - {one-line summary}"
```
This lets you rollback to any iteration if a later one is worse.

**B. Evaluate with a fresh subagent**

Spawn a fresh Agent tool subagent. Pass it ONLY:
- The URL
- Project context summary (not code)
- The criteria and weights
- The scoring calibration block (verbatim)
- Any project-specific criteria from the branding doc
- The current iteration number (so it knows context, not to be lenient because it's early)

Evaluator prompt:

```
You are a harsh design evaluator. You have never seen the source code. Use Playwright MCP to interact with the live app at {url}.

Your job is to find problems, not give encouragement.

CALIBRATION:
{insert the full scoring calibration block above}

CRITERIA (score each 1-10):
1. Design quality (0.35): {description + project-specific version}
2. Originality (0.30): {description + project-specific version}
3. Coherence (0.20): {description + project-specific version}
4. Craft (0.15): {description + project-specific version}

PROJECT CONTEXT: {summary}
BRANDING CONTEXT: {branding doc contents or "No branding doc - evaluate against the product context only."}

STEPS:
1. Navigate to {url}. Take a full-page screenshot.
2. Visit every distinct screen/view. Screenshot each.
3. Interact with the app as the intended user would.
4. Score each criterion with 2-3 sentence justification.
5. Calculate weighted composite.

RETURN EXACTLY:
SCORES:
  Design quality: X/10 - {justification}
  Originality: X/10 - {justification}
  Coherence: X/10 - {justification}
  Craft: X/10 - {justification}
  Composite: X.X/10

TOP 3 IMPROVEMENTS:
  1. {specific and actionable - not "make it better" but "the card grid has equal visual weight everywhere - the primary action card should be 2x larger and a different colour to create a clear entry point"}
  2. {specific}
  3. {specific}

CODE FIXES: {things to fix now, or "None"}
BRANDING FLAGS: {decisions needing human input, or "None"}
FUNCTIONAL ISSUES: {anything broken, or "None"}

RECOMMENDATION: REFINE or PIVOT
RATIONALE: {why}
```

**C. Report iteration status**

Show the user:
```
Iteration {N} | Composite: X.X/10
  Design: X | Originality: X | Coherence: X | Craft: X
  Feedback: {one-line summary of the most important critique}
  Direction: REFINE / PIVOT - {brief rationale}
  Branding flags: {any new ones, or "None"}
```

**D. Decide: refine or pivot**

Use both the evaluator's recommendation and the score trajectory:

**REFINE** when:
- Scores improved 0.5+ since last iteration
- Core direction fits the product
- Weaknesses are polish/hierarchy/clarity, not structural
- Originality is present but underdeveloped

**PIVOT** when:
- Scores improved <0.3 over 2 consecutive iterations
- Originality stuck below 6
- Evaluator's critique is structural, not cosmetic
- You're just rearranging the same template

A pivot means changing the concept meaningfully: layout model, visual metaphor, density, typography approach, tonal direction. Not just swapping hex values.

**E. Stop when:**
- All criteria meet or exceed threshold
- 3 consecutive iterations improved <0.3 each
- A branding decision blocks meaningful progress
- Max iterations reached

### Final report

```
DESIGN ITERATION SUMMARY
========================
Iterations: {N}
Score trajectory:

| Iter | Design | Originality | Coherence | Craft | Composite | Decision |
|------|--------|-------------|-----------|-------|-----------|----------|

BRANDING DECISIONS NEEDED:
{Collected from all iterations, deduplicated. These block further design progress until resolved.}

CHANGES MADE:
{Summary of major design changes across iterations. Each iteration is a git commit - user can rollback with git log.}

REMAINING IMPROVEMENTS:
{Top items from the final evaluation not yet addressed.}

NEXT MOVE:
{One of: keep refining | pivot to new direction | pause for branding input}
```

## Behavioural guardrails

- The evaluator subagent must NEVER see source code. It interacts with the live app only.
- Do not self-evaluate inline. Always spawn a fresh subagent.
- Do not claim design excellence without evaluator evidence.
- Do not keep looping if scores are flat and the concept is exhausted.
- Do not patch over missing brand strategy with arbitrary stylistic inventions. Collect as branding flags.
- The branding doc is law. If it says navy and gold, don't pivot to pastel pink because you think it scores better. Flag the constraint and work within it.
- Bold moves beat safe tweaks. If you're stuck at 5-6, changing a button colour won't get you to 7. Rethink the layout, the visual metaphor, the whole mood.
