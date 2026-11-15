---
name: aeo
description: "Use when content should be cited by ChatGPT, Perplexity, Claude or Gemini (answer engine optimization), before rewriting it. Scores E-E-A-T and structure with aeo_audit.py, rewrites with aeo_optimizer.py and logs citations with citation_tracker.py."
---

# Answer Engine Optimization (AEO)

**Get your content cited by ChatGPT, Perplexity, Claude, Gemini, and Mistral as the authoritative source.**

AEO is the practice of optimizing content for **citation** in LLM-generated responses — distinct from SEO, which optimizes for search rankings. This skill audits, optimizes, and tracks AEO performance.

## Distinct From SEO

| | SEO | AEO |
|---|---|---|
| **Optimizes for** | Click-through rankings | Being cited as authoritative source |
| **Audience** | Humans browsing search results | LLMs answering questions |
| **Success metric** | Position 1-10, organic traffic | Citation count across LLMs |
| **Key signals** | Backlinks, keywords, page speed | E-E-A-T, structured data, factual density |
| **Update cadence** | Weeks-to-months | Days-to-weeks (LLM training cycles) |

Both can coexist — the same content can rank #1 on Google AND get cited by Perplexity. But the techniques differ: SEO rewards keyword density + backlinks; AEO rewards primary-source signals + structured facts.

## When To Use

- Planning a new content piece for an AI-first audience
- Auditing existing content for E-E-A-T gaps before AI Overview rollout
- Tracking which pages get cited by which LLM (citation ledger)
- Researching what queries LLMs cite sources for (vs. what they answer from training)
- Benchmarking against competitors' citation rates
- Building a long-term AEO strategy aligned with traditional SEO

## When NOT To Use

- Pure click-through SEO without LLM-citation intent — use `seo-audit` instead
- Brand-voice content with no factual claims — citations require facts to cite
- Content for a topic where LLMs already have strong training signal (e.g., elementary math) — citation upside is minimal
- Time-sensitive content (breaking news) — LLM training lag means citations come months later

## Core Capabilities

### 1. Content audit + E-E-A-T scoring

The auditor (`aeo_audit.py`) scores content across 4 dimensions:

- **Experience**: First-person evidence, dated examples, case studies, "We ran X in 2026" claims
- **Expertise**: Author bio, credentials, citations to peer-reviewed sources, technical depth
- **Authoritativeness**: External backlinks from authority domains, schema.org markup, structured data
- **Trustworthiness**: HTTPS, contact info, transparent corrections, factual density (number of verifiable claims per 1000 words)

Composite score 0-100 with per-dimension breakdown. Output: markdown report with specific fix recommendations.

Industry-aware thresholds: healthcare/finance/legal require higher composite scores due to YMYL implications.

Run from your skill directory:

```bash
python3 scripts/aeo_audit.py --input path/to/post.md --industry b2b
python3 scripts/aeo_audit.py --url https://yoursite.com/post --output json
python3 scripts/aeo_audit.py --sample  # demo run
```

Supported industries: `saas`, `healthcare`, `finance`, `legal`, `ecommerce`, `b2b`, `media`, `education`.

### 2. Content optimization

The optimizer (`aeo_optimizer.py`) generates AEO-improved variants:

- **Structure rewrite** — H2/H3 hierarchy optimized for LLM parsing
- **Citation density boost** — insert [N]-style markers after verifiable claims
- **Schema.org JSON-LD injection** — Article + FAQPage (auto-detected from headings)
- **Corrections footer** — automated disclosure/corrections policy block
- **Fact-first lede** — moves first verifiable fact closer to the top

Three modes: `conservative` (schema only), `balanced` (+ structure + citations), `aggressive` (full restructure).

```bash
python3 scripts/aeo_optimizer.py --input post.md --mode balanced --output post-aeo.md
python3 scripts/aeo_optimizer.py --input post.md --mode aggressive --industry healthcare
python3 scripts/aeo_optimizer.py --sample
```

### 3. Citation tracking

The tracker (`citation_tracker.py`) maintains a local ledger of when and where your content gets cited by each LLM. No telemetry, all local-first at `~/.aeo-data/citations.json`.

```bash
python3 scripts/citation_tracker.py --action add --url https://yoursite.com/post \
    --llm perplexity --query "what is AEO" --date 2026-05-17
python3 scripts/citation_tracker.py --action list --url https://yoursite.com/post
python3 scripts/citation_tracker.py --action report --url https://yoursite.com/post
python3 scripts/citation_tracker.py --action export --output citations.csv
python3 scripts/citation_tracker.py --sample  # populate sample data
```

Verdict scale: `EARLY` → `EMERGING` → `STRONG` based on cross-LLM coverage.

## Workflow

### New content creation

1. Draft content using copywriting/content-strategy skills
2. Run `aeo_audit.py --input draft.md --industry <your-industry>` before publishing
3. Address all `top_fixes` flagged items
4. Run `aeo_optimizer.py --input fixed.md --mode balanced` for structural polish
5. Publish; add canonical URL to schema via `--url` flag
6. Log initial test citations with `citation_tracker.py --action add`

### Existing content optimization

1. Run `aeo_audit.py --url https://yoursite.com/post` to audit live page
2. Compare score against industry threshold
3. For pages below threshold: run `aeo_optimizer.py` in `balanced` mode, review output, publish revised version
4. Track weekly: manual testing on Perplexity/ChatGPT/Claude/Gemini, log observed citations
5. Re-audit monthly; track velocity in `citation_tracker.py report`

## Trigger Phrases

- "AEO audit", "AEO check"
- "optimize for ChatGPT / Perplexity / Claude / Gemini"
- "get cited by [LLM]"
- "is my content AEO-ready?"
- "why am I not getting cited in AI answers"

## References

| File | What's in it |
|---|---|
| `references/aeo_eeat_canon.md` | E-E-A-T scoring canon — exact rubric used by the auditor |
| `references/aeo_vs_seo.md` | Detailed comparison table with strategic guidance |
| `references/bot_access_and_monitoring.md` | AI crawler matrix (robots.txt rules) + monitoring protocols |
| `references/extractable_content_patterns.md` | Ready-to-use block templates (definitions, FAQs, tables, stats) |
| `references/llm_citation_patterns.md` | How each major LLM chooses sources + empirical triggers |

## Related Skills

- `marketing-skill/skills/content-strategy` — broader content planning
- `marketing-skill/skills/copywriting` — voice + tone
- `marketing-skill/skills/schema` — structured data implementation
- `marketing-skill/skills/ai-seo` — overlapping domain; this is AEO-specific
- `marketing-skill/skills/seo-audit` — traditional SEO foundation

---

**Version:** 2.7.3
**Source:** Ported from [`alirezarezvani/aeo-box`](https://github.com/alirezarezvani/aeo-box) (`answer-engine-optimization/` skill, 2,464 LOC across 9 modules). This port distills the 9-module Python toolkit into 3 stdlib CLI tools per the claude-skills convention; preserves the E-E-A-T scoring methodology, citation-tracking schema, and industry-aware thresholds verbatim.
