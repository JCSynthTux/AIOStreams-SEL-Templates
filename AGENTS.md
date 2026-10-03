# AGENTS.md

Guidance for AI agents (and humans) working in this repository.

## Project overview

This repo stores reusable **Stream Expression Language (SEL)** filter/sort profiles for the
[AIOStreams](https://github.com/Viren070/AIOStreams) Stremio addon. Each profile is a
self-contained, ready-to-import setup: users paste a raw GitHub URL and the addon applies the
profile's filtering and ranking logic. There are no debrid dependencies — every profile is
**Usenet-only**.

The repo is **JSON + Markdown only**. There is **no build step, no test suite, no linter, no
CI, and no git hooks**. Nothing will catch a mistake for you: agents must self-validate every
JSON and Markdown change before committing (see [Validation](#validation-before-commit)).

## Repo layout

```
templates/<slug>.json        Full importable template (metadata + config)
templates.json               Aggregate array of every template — importable via one URL
sel/<slug>/excluded-stream-expressions.json
sel/<slug>/preferred-stream-expressions.json
sel/<slug>/ranked-stream-expressions.json
profiles/<slug>.md           Detailed per-profile documentation
docs/import-guide.md         End-user import instructions
README.md                    Profile table + repo overview
CHANGELOG.md                 Keep a Changelog-style entries, newest on top
AGENTS.md                    Agent guidance: project facts, SEL rules, commit policy
LICENSE                      WTFPL
```

The five existing profiles (all Usenet-only):

- `german-english-usenet` — German and/or English audio, dual-audio preferred
- `german-usenet` — German audio required
- `russian-ukrainian-usenet` — Russian or Ukrainian audio
- `japanese-korean-chinese-usenet` — CJK audio **plus** required subtitles
- `english-usenet` — English audio required

## Adding a profile — the six touch points

Adding or renaming a profile requires **all six** of these, or the repo is left inconsistent:

1. `templates/<slug>.json` — the full importable template.
2. `sel/<slug>/{excluded,preferred,ranked}-stream-expressions.json` — the three raw expression lists.
3. `profiles/<slug>.md` — per-profile documentation.
4. A row in the README profile table (and any other profile enumerations).
5. Append the matching object to `templates.json`.
6. A `CHANGELOG.md` entry under `## Unreleased`.

**Critical:** the object appended to `templates.json` must be an **exact copy** of the
`templates/<slug>.json` object — same `metadata` and same `config`, byte-for-byte after
formatting. `templates.json` is just an array of those objects. Metadata IDs are namespaced
`jcsynthtux.<slug>` and must stay unique; bump when adding.

## SEL semantics (get these exactly right)

- **Excluded expressions are REMOVED by the addon.** A hard requirement is therefore written as
  the **complement** of the desired set: `negate(language(streams, 'German'), streams)` (streams
  NOT matching German) — **not** a bare `language(streams, 'German')`. Inverting this deletes the
  streams you want to keep. See the git history for the past AV1 inversion bug that motivated
  this warning.
- **AV1 exclusion uses the removal form** `encode(streams, 'AV1')` (drops AV1 encodes), **not**
  `negate(encode(streams, 'AV1'), streams)`.
- **`ranked-*` files are always `[]`** in this repo.
- **Language detection is title-parse based.** `MULTi` / untagged releases are filtered out.
  Optionally merging `language(streams, 'Multi')` and/or `language(streams, 'Dual Audio')` trades
  false positives for extra coverage.
- **Subtitle filtering** (`subtitle(streams, 'Lang')`) currently exists **only** in the CJK
  profile; every other profile filters audio only.
- **Preferred (ranking) tiers** wrap the same predicates in
  `visualTag(streams, 'HDR', 'DV', 'HDR10', 'HDR10+', 'HLG')` to split HDR from SDR. The sort
  convention is `streamExpressionMatched desc, resolution desc, quality desc`.
- **Native AIOStreams "Filters" apply in addition to SEL.** After import, users must review them
  for contradictions (documented in `docs/import-guide.md`).

## Validation before commit

Run these **before every commit**:

- `python3 -m json.tool <file>` on each touched JSON file — a parse failure is fatal.
- `python3 -m json.tool templates.json` and confirm it has one object per profile (currently 5)
  with unique `metadata.id` values.
- **Mirror-check:** diff the new/changed profile against its closest source profile with only the
  language string swapped; this reliably exposes typos and missing/duplicated expressions.
- `git status` — confirm no unintended files were changed or added.

## Git & commit policy

Agents are **authorized to commit directly to `master`**. This is a small, docs/JSON repository:
there are no pull requests, no feature branches, and no branch dance. Do not push to `origin`
unless explicitly instructed.

All commits follow **[Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/#specification)**:

- Format: `<type>[optional scope][!]: <description>`.
- Common types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`.
  `feat` maps to MINOR, `fix` maps to PATCH.
- Spec-compliant messages may use any casing; this repo additionally requires the description to
  be lowercase, imperative, and without a trailing period.
- An optional body (one or more paragraphs) explains **what** and **why**; optional footers follow.
- A breaking change is marked with `!` before the colon **and/or** a `BREAKING CHANGE:` footer.
  The footer token `BREAKING-CHANGE:` is synonymous with `BREAKING CHANGE:` (spec §16).
- Keep one logical change per commit so history stays atomic.

Example messages matching this repo's style:

```
feat(profiles): add french-usenet profile
fix(profiles): correct inverted AV1 exclusion
docs: update import guide
feat(templates)!: rename template metadata id scheme
```

Run the validation steps above **before** committing, and commit after each logical change
(atomic commits).

## Maintaining this file

`AGENTS.md` is maintained like any other document: when the repo's structure, conventions, or
SEL rules change, update this file in the same commit (or a follow-up `docs:` commit).
