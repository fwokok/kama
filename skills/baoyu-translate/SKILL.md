---
name: baoyu-translate
description: Translates articles and documents between languages with three modes - quick (direct), normal (analyze then translate), and refined (analyze, translate, review, polish). Supports custom glossaries and terminology consistency via EXTEND.md. Use when user asks to "translate", "翻译", "精翻", "translate article", "translate to Chinese/English", "改成中文", "改成英文", "convert to Chinese", "localize", "本地化", or needs any document translation. Also triggers for "refined translation", "精细翻译", "proofread translation", "快速翻译", "快翻", "这篇文章翻译一下", or when a URL or file is provided with translation intent.
version: 1.59.0
metadata:
  openclaw:
    homepage: https://github.com/JimLiu/baoyu-skills#baoyu-translate
    requires:
      anyBins:
        - bun
        - npx
---

# Translator

Three-mode translation skill: **quick** for direct translation, **normal** for analysis-informed translation, **refined** for full publication-quality workflow with review and polish.

## 0. READ THE SOURCE (Mandatory First Step)

Before choosing mode or strategy, read the source and output a one-line interpretation:

> **"Reading this as: [text type] about [domain], [tone/voice], target audience [audience], key challenge: [main difficulty]."**

Example reads:
- *"Reading this as: technical blog post about distributed systems, conversational-expert voice, target audience engineers, key challenge: metaphor translation + terminology consistency."*
- *"Reading this as: corporate earnings report, formal-institutional voice, target audience investors, key challenge: financial jargon precision + number formatting."*
- *"Reading this as: personal essay about parenting, intimate-humorous voice, target audience general readers, key challenge: preserving emotional tone across cultural idioms."*

**This read determines**: mode choice, style preset, glossary priorities, and which translation principles to emphasize.

If the source type is ambiguous, ask ONE clarifying question. Do not guess.

## 0.A. THREE TRANSLATION DIALS

After the source read, set three dials. Every translation decision flows from these.

| Dial | 1-3 | 4-7 | 8-10 |
|------|-----|-----|------|
| **FORMALITY** | Casual/slang OK | Standard register | Academic/legal precision |
| **CREATIVITY** | Literal/strict | Idiomatic adaptation | Transcreation/free rendering |
| **CONCISENESS** | Full exposition | Balanced | Compressed/telegraphic |

**Default: 5 / 5 / 5** — balanced translation. Adjust per source read.

| Source type | FORMALITY | CREATIVITY | CONCISENESS |
|-------------|-----------|------------|-------------|
| Technical docs | 8 | 2 | 5 |
| Blog post / essay | 4 | 7 | 5 |
| News article | 6 | 3 | 7 |
| Marketing copy | 3 | 9 | 7 |
| Academic paper | 9 | 2 | 4 |
| Social media post | 2 | 8 | 8 |
| Legal / contract | 10 | 1 | 4 |
| Literary / poetry | 5 | 10 | 3 |
| Corporate report | 8 | 2 | 6 |

**How dials drive output**:
- High FORMALITY → no slang, no contractions (EN), proper honorifics (ZH)
- High CREATIVITY → rewrite idioms as target-culture equivalents, restructure sentences freely
- Low CREATIVITY → preserve source structure, translate terms directly
- High CONCISENESS → merge short paragraphs, cut filler, remove redundant modifiers
- Low CONCISENESS → preserve all detail, expand where needed for clarity

## Script Directory

Scripts in `scripts/` subdirectory. `{baseDir}` = this SKILL.md's directory path. Resolve `${BUN_X}` runtime: if `bun` installed → `bun`; if `npx` available → `npx -y bun`; else suggest installing bun. Replace `{baseDir}` and `${BUN_X}` with actual values.

| Script | Purpose |
|--------|---------|
| `scripts/main.ts` | CLI entry point. Default action splits markdown into chunks; also supports explicit `chunk` subcommand |
| `scripts/chunk.ts` | Markdown chunking implementation used by `main.ts` and kept compatible for direct invocation |

## Preferences (EXTEND.md)

Check EXTEND.md existence (priority order):

```bash
# macOS, Linux, WSL, Git Bash
test -f .baoyu-skills/baoyu-translate/EXTEND.md && echo "project"
test -f "${XDG_CONFIG_HOME:-$HOME/.config}/baoyu-skills/baoyu-translate/EXTEND.md" && echo "xdg"
test -f "$HOME/.baoyu-skills/baoyu-translate/EXTEND.md" && echo "user"
```

```powershell
# PowerShell (Windows)
if (Test-Path .baoyu-skills/baoyu-translate/EXTEND.md) { "project" }
$xdg = if ($env:XDG_CONFIG_HOME) { $env:XDG_CONFIG_HOME } else { "$HOME/.config" }
if (Test-Path "$xdg/baoyu-skills/baoyu-translate/EXTEND.md") { "xdg" }
if (Test-Path "$HOME/.baoyu-skills/baoyu-translate/EXTEND.md") { "user" }
```

| Path | Location |
|------|----------|
| `.baoyu-skills/baoyu-translate/EXTEND.md` | Project directory |
| `$HOME/.baoyu-skills/baoyu-translate/EXTEND.md` | User home |

| Result | Action |
|--------|--------|
| Found | Read, parse, apply settings. On first use in session, briefly remind: "Using preferences from [path]. You can edit EXTEND.md to customize glossary, audience, etc." |
| Not found | **MUST** run first-time setup (see below) — do NOT silently use defaults |

**EXTEND.md Supports**: Default target language | Default mode | Target audience | Custom glossaries (inline or file path) | Translation style | Chunk settings

Schema: [references/config/extend-schema.md](references/config/extend-schema.md)

### First-Time Setup (BLOCKING)

**CRITICAL**: When EXTEND.md is not found, you **MUST** run the first-time setup before ANY translation. This is a **BLOCKING** operation.

Full reference: [references/config/first-time-setup.md](references/config/first-time-setup.md)

Use `AskUserQuestion` with all questions (target language, mode, audience, style, save location) in ONE call. After user answers, create EXTEND.md at the chosen location, confirm "Preferences saved to [path]", then continue.

## Defaults

All configurable values in one place. EXTEND.md overrides these; CLI flags override EXTEND.md.

| Setting | Default | EXTEND.md key | CLI flag | Description |
|---------|---------|---------------|----------|-------------|
| Target language | `zh-CN` | `target_language` | `--to` | Translation target language |
| Mode | `normal` | `default_mode` | `--mode` | Translation mode |
| Audience | `general` | `audience` | `--audience` | Target reader profile |
| Style | `storytelling` | `style` | `--style` | Translation style preference |
| Chunk threshold | `4000` | `chunk_threshold` | — | Word count to trigger chunked translation |
| Chunk max words | `5000` | `chunk_max_words` | — | Max words per chunk |

## Modes

| Mode | Flag | Steps | When to Use |
|------|------|-------|-------------|
| Quick | `--mode quick` | Translate | Short texts, informal content, quick tasks |
| Normal | `--mode normal` (default) | Analyze → Translate | Articles, blog posts, general content |
| Refined | `--mode refined` | Analyze → Translate → Review → Polish | Publication-quality, important documents |

**Default mode**: Normal (can be overridden in EXTEND.md `default_mode` setting).

**Style presets** — control the voice and tone of the translation (independent of audience):

| Value | Description | Effect |
|-------|-------------|--------|
| `storytelling` | Engaging narrative flow (default) | Draws readers in, smooth transitions, vivid phrasing |
| `formal` | Professional, structured | Neutral tone, clear organization, no colloquialisms |
| `technical` | Precise, documentation-style | Concise, terminology-heavy, minimal embellishment |
| `literal` | Close to original structure | Minimal restructuring, preserves source sentence patterns |
| `academic` | Scholarly, rigorous | Formal register, complex clauses OK, citation-aware |
| `business` | Concise, results-focused | Action-oriented, executive-friendly, bullet-point mindset |
| `humorous` | Preserves and adapts humor | Witty, playful, recreates comedic effect in target language |
| `conversational` | Casual, spoken-like | Friendly, approachable, as if explaining to a friend |
| `elegant` | Literary, polished prose | Aesthetically refined, rhythmic, carefully crafted word choices |

Custom style descriptions are also accepted, e.g., `--style "poetic and lyrical"`.

**Auto-detection**:
- "快翻", "quick", "直接翻译" → quick mode
- "精翻", "refined", "publication quality", "proofread" → refined mode
- Otherwise → default mode (normal)

**Upgrade prompt**: After normal mode completes, display:
> Translation saved. To further review and polish, reply "继续润色" or "refine".

If user responds, continue with review → polish steps (same as refined mode Steps 4-6 in refined-workflow.md) on the existing output.

## 0.B. TRANSLATION TELLS (Hard Bans)

These patterns are AI translation signatures. They MUST NOT appear in the final output. If detected during review, rewrite.

### Chinese Output Bans

| Ban | Reason | Fix |
|-----|--------|-----|
| `赋能` as default verb | #1 AI Chinese translation tell | Use concrete alternatives: 帮助/支持/让……能/提升 |
| `在当今时代` / `随着……发展` opener | Generic AI article opening | Cut entirely; start with substance |
| `首先……其次……最后……` list structure | LLM default enumeration | Natural transitions; not every list needs signposts |
| `总而言之` / `综上所述` closer | AI conclusion reflex | End naturally; strong last sentence beats formula |
| `值得注意的是` | Overused AI emphasis marker | State the point; if it's noteworthy, it speaks for itself |
| `不仅……而且……` chains > 1 per paragraph | LLM parallelism addiction | Break into separate sentences |
| Em-dash `——` as separator | Visual AI signature | Comma, period, or colon |
| `**粗体**` on > 2 random words per paragraph | AI fake emphasis | Bold only genuine key terms |
| `（注：……）` parenthetical notes | AI insecurity | Integrate or cut |
| Fake-balanced `一方面……另一方面……` | AI hedging | Pick a position or present as alternatives |

### English Output Bans

| Ban | Reason | Fix |
|-----|--------|-----|
| `In today's world` / `In the era of` opener | AI article cliché | Cut; start with substance |
| `It is worth noting that` | AI emphasis crutch | State the point directly |
| `Furthermore` + `Moreover` + `Additionally` cascade | AI stacking filler | Pick one transition max per paragraph |
| `This is because` after every claim | AI over-explaining | Implicit causation; trust reader |
| `In conclusion` / `To sum up` closer | AI reflex ending | Strong last point; no formula |
| `Not only… but also…` chains | Same as Chinese | Break or cut half |

### General Output Bans

| Ban | Reason |
|-----|--------|
| `TODO` / `FIXME` / placeholder text | Incomplete work |
| `[insert translation here]` | Never deliver unfilled slots |
| Untranslated source text fragments | Consistency failure |
| Mix of translated + original terms for same concept | Terminology drift |
| Over-explaining metaphors the target reader already knows | AI doesn't trust readers |

## Usage

```
/translate [--mode quick|normal|refined] [--from <lang>] [--to <lang>] [--audience <audience>] [--style <style>] [--glossary <file>] <source>
```

- `<source>`: File path, URL, or inline text
- `--from`: Source language (auto-detect if omitted)
- `--to`: Target language (from EXTEND.md or default `zh-CN`)
- `--audience`: Target reader profile (from EXTEND.md or default `general`)
- `--style`: Translation style (from EXTEND.md or default `storytelling`)
- `--glossary`: Additional glossary file to merge with EXTEND.md glossary

**Audience presets**:

| Value | Description | Effect |
|-------|-------------|--------|
| `general` | General readers (default) | Plain language, more translator's notes for jargon |
| `technical` | Developers / engineers | Less annotation on common tech terms |
| `academic` | Researchers / scholars | Formal register, precise terminology |
| `business` | Business professionals | Business-friendly tone, explain tech concepts |

Custom audience descriptions are also accepted, e.g., `--audience "AI感兴趣的普通读者"`.

## Workflow

### Step 1: Load Preferences

1.1 Check EXTEND.md (see Preferences section above)

1.2 Load built-in glossary for the language pair if available:
- EN→ZH: [references/glossary-en-zh.md](references/glossary-en-zh.md)

1.3 Merge glossaries: EXTEND.md `glossary` (inline) + EXTEND.md `glossary_files` (external files, paths relative to EXTEND.md location) + built-in glossary + `--glossary` file (CLI overrides all)

### Step 2: Materialize Source & Create Output Directory

Materialize source (file as-is, inline text/URL → save to `translate/{slug}.md`), then create output directory: `{source-dir}/{source-basename}-{target-lang}/`. Detect source language if `--from` not specified.

Full details: [references/workflow-mechanics.md](references/workflow-mechanics.md)

**Output directory contents** (all intermediate and final files go here):

| File | Mode | Description |
|------|------|-------------|
| `translation.md` | All | Final translation (always this name) |
| `01-analysis.md` | Normal, Refined | Content analysis (domain, tone, terminology) |
| `02-prompt.md` | Normal, Refined | Assembled translation prompt |
| `03-draft.md` | Refined | Initial draft before review |
| `04-critique.md` | Refined | Critical review findings (diagnosis only) |
| `05-revision.md` | Refined | Revised translation based on critique |
| `chunks/` | Chunked | Source chunks + translated chunks |

### Step 3: Assess Content Length

Quick mode does not chunk — translate directly regardless of length. Before translating, estimate word count. If content exceeds chunk threshold (default 4000 words), proactively warn: "This article is ~{N} words. Quick mode translates in one pass without chunking — for long content, `--mode normal` produces better results with terminology consistency." Then proceed if user doesn't switch.

For normal and refined modes:

| Content | Action |
|---------|--------|
| < chunk threshold | Translate as single unit |
| >= chunk threshold | Chunk translation (see Step 3.1) |

**3.1 Long Content Preparation** (normal/refined modes, >= chunk threshold only)

Before translating chunks:

1. **Extract terminology**: Scan entire document for proper nouns, technical terms, recurring phrases
2. **Build session glossary**: Merge extracted terms with loaded glossaries, establish consistent translations
3. **Split into chunks**: Use `${BUN_X} {baseDir}/scripts/main.ts <file> [--max-words <chunk_max_words>] [--output-dir <output-dir>]`
   - Parses markdown blocks (headings, paragraphs, lists, code blocks, tables, etc.)
   - Splits at markdown block boundaries to preserve structure
   - If a single block exceeds the threshold, falls back to line splitting, then word splitting
4. **Assemble translation prompt**:
   - Main agent reads `01-analysis.md` (if exists) and assembles shared context using Part 1 of [references/subagent-prompt-template.md](references/subagent-prompt-template.md) — inlining: target style, content background, merged glossary, and translation challenges
   - Save as `02-prompt.md` in the output directory (shared context only, no task instructions)
5. **Draft translation via subagents** (if Agent tool available):
   - Spawn one subagent **per chunk**, all in parallel (Part 2 of the template)
   - Each subagent reads `02-prompt.md` for shared context, receives chunk position info (chunk N of M + brief context of where it sits in the argument), translates its chunk, saves to `chunks/chunk-NN-draft.md`
   - Consistency is guaranteed by the shared `02-prompt.md` (glossary, figurative language mapping, comprehension challenges, source voice, and translation challenges from analysis)
   - If no chunks (content under threshold): spawn one subagent for the entire source file
   - If Agent tool is unavailable, translate chunks sequentially inline using `02-prompt.md`
6. **Merge**: Once all subagents complete, combine translated chunks in order. If `chunks/frontmatter.md` exists, prepend it. Save as `03-draft.md` (refined) or `translation.md` (normal)
7. All intermediate files (source chunks + translated chunks) are preserved in `chunks/`

**After chunked draft is merged**, return control to main agent for critical review, revision, and polish (Step 4).

### Step 4: Translate & Refine

**Translation principles** (apply to all modes):

- **Rewrite, not translate**: Rewrite content into natural, engaging target language as if a skilled native writer composed it from scratch. Quality test: "Does this read like it was originally written in the target language?"
- **Accuracy first**: Facts, data, and logic must match the original exactly
- **Natural flow**: Use idiomatic target language word order. Break long source sentences into shorter, natural ones. Interpret metaphors and idioms by intended meaning, not word-for-word
- **Terminology**: Use standard translations consistently. First occurrence of specialized terms: annotate with original in parentheses
- **Preserve format**: Keep all markdown formatting (headings, bold, italic, images, links, code blocks)
- **Proactive interpretation**: For jargon or concepts the target audience may lack context for, add concise explanations in **bold parentheses** `（**解释**）`. Keep annotations few — only where genuinely needed for comprehension
- **Frontmatter**: If source has YAML frontmatter, rename source-metadata fields with `source` prefix (camelCase: `url`→`sourceUrl`, `title`→`sourceTitle`, etc.), add translated values as new top-level fields (skip `title` if body has H1), keep other fields as-is

#### Quick Mode

Translate directly → save to `translation.md`. Apply all translation principles above.

#### Normal Mode

1. **Analyze** → `01-analysis.md` (domain, tone, terminology, translation challenges)
2. **Assemble prompt** → `02-prompt.md` (translation instructions with context, glossary, challenges)
3. **Translate** (following `02-prompt.md`) → `translation.md`

After completion, prompt user: "Translation saved. To further review and polish, reply **继续润色** or **refine**."

If user continues, proceed with critical review → revision → polish (same as refined mode Steps 4-6 below), saving `03-draft.md` (rename current `translation.md`), `04-critique.md`, `05-revision.md`, and updated `translation.md`.

#### Refined Mode

Full workflow for publication quality. See [references/refined-workflow.md](references/refined-workflow.md) for detailed guidelines per step.

The subagent (if used in Step 3.1) only handles the initial draft. All subsequent steps (critical review, revision, polish) are handled by the main agent, which may delegate to subagents at its discretion.

Steps and saved files (all in output directory):
1. **Analyze** → `01-analysis.md` (domain, tone, terminology, translation challenges)
2. **Assemble prompt** → `02-prompt.md` (translation instructions with inlined context)
3. **Draft** → `03-draft.md` (initial translation with translator's notes; from subagent if chunked)
4. **Critical review** → `04-critique.md` (diagnosis only: accuracy, Europeanized language, strategy execution, expression issues)
5. **Revision** → `05-revision.md` (apply all critique findings to produce revised translation)
6. **Polish** → `translation.md` (final publication-quality translation)

Each step reads the previous step's file and builds on it.

### Step 5: Output

Final translation is always at `translation.md` in the output directory.

After the final translation is written, do a lightweight image-language pass:

1. Collect image references from the translated article
2. Identify likely text-heavy images such as covers, screenshots, diagrams, charts, frameworks, and infographics
3. If any image likely contains a main text language that does not match the translated article language, proactively remind the user
4. The reminder must be a list only. Do not automatically localize those images unless the user asks

Reminder format (use whatever image syntax the article already uses — standard markdown or wikilink):
```text
Possible image localization needed:
- ![example cover](attachments/example-cover.png): likely still contains source-language text while the article is now in target language
- ![example diagram](attachments/example-diagram.png): likely text-heavy framework graphic, check whether labels need translation
```

### Step 6: Pre-Flight Quality Check

Before delivering, run through the output quality checklist. Reference `baoyu-output-quality` skill for full checklist. Minimum check:

- [ ] **Completeness**: No placeholder text, no truncated sections, last paragraph reads complete
- [ ] **Tells scan**: Zero banned patterns from Section 0.B detected in output
- [ ] **Consistency**: Same term = same translation throughout; tone matches dial settings
- [ ] **Readability**: Paragraph length varied; no 5+ consecutive sentences with identical structure
- [ ] **Format preservation**: All markdown elements intact (headings, code blocks, links, images)
- [ ] **Number/date check**: All numbers and dates faithfully transferred; formats localized if needed

If any check fails → fix → re-check from that point. Do not deliver until all pass.

Display summary:
```
**Translation complete** ({mode} mode) — Dial: F{formality}/C{creativity}/B{brevity}

Source: {source-path}
Languages: {from} → {to}
Output dir: {output-dir}/
Final: {output-dir}/translation.md
Glossary terms applied: {count}
```

If mismatched image-language candidates were found, append a short note after the summary telling the user that some embedded images may still need image-text localization, followed by the candidate list.

## Anti-Rationalization Table

> Pattern from agent-skills: common rationalizations that lead to bad translations.

| Rationalization | Reality |
|---|---|
| "I know this domain, I can skip the source read" | Without explicit read, mode/dial/glossary decisions are guesses. Bad guesses cascade. |
| "Quick mode doesn't need dials" | Dials apply to ALL modes. Even quick translation benefits from explicit FORMALITY/CREATIVITY/CONCISENESS. |
| "This term is common, no need to glossary it" | Consistency across 5000 words depends on glossary. One undocumented term = potential drift. |
| "The draft looks fine, I can skip critical review" | Self-review catches ~30% of issues; structured critique catches ~70%. Skip it for publications at your peril. |
| "One 赋能 won't hurt" | One tolerated ban word breeds ten. The bans exist because these are AI translation signatures. |
| "I'll just translate literally, it's safer" | Literal translation is NOT safer — it produces 翻译腔 (translationese), which is worse than a creative miss. |
| "The chunked subagents handled consistency" | Subagents follow the prompt, but merge artifacts are real. Always audit the merged output for terminology drift. |
| "Image language mismatch is the user's problem" | Flagging image localization needs is part of the job. Don't deliver half-translated content silently. |
| "This paragraph is too hard, I'll keep source structure" | Hard paragraphs need MORE rewriting, not less. Restructure until it reads as native target language. |
| "The user didn't set EXTEND.md, I'll use defaults" | First-time setup is BLOCKING by design. Never silently default — ask once, configure forever. |

## Extension Support

Custom configurations via EXTEND.md. See **Preferences** section for paths and supported options.
