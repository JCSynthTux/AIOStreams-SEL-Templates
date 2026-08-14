# Profile: German + English Usenet (Dual Audio)

A SEL-driven filtering and sorting profile for [AIOStreams](https://github.com/Viren070/AIOStreams).
It keeps **only Usenet releases** with **German and/or English audio**, prioritises **dual-audio**
(German + English) over single-audio, and prefers **HDR** over SDR.

## Requirements

| Rule | Behaviour |
| --- | --- |
| Audio (primary) | Prefer streams with **both German and English** audio |
| Audio (fallback) | Streams with **German or English** audio |
| Stream type | **Usenet only** |
| Resolution | Only **2160p, 1080p, 720p** |
| 720p gating | 720p shown **only when fewer than 5 x 1080p** streams exist |
| Subtitles | Not required / not filtered |
| HDR | Allowed and **preferred over SDR** |

## Priority order

Streams are tiered in the following order (highest first). Each stream matches only the
**first** Preferred Stream Expression it satisfies.

1. **Dual Audio + HDR**
2. **Dual Audio + SDR**
3. **Single Audio + HDR**
4. **Single Audio + SDR**

## How it works

### Excluded Stream Expressions (hard filters)

| Expression | Purpose |
| --- | --- |
| `negate(type(streams, 'usenet', 'stremio-usenet'), streams)` | Remove every non-Usenet stream |
| `negate(resolution(streams, '2160p', '1080p', '720p'), streams)` | Remove every resolution except 2160p / 1080p / 720p |
| `negate(merge(language(streams, 'German'), language(streams, 'English')), streams)` | Remove streams with neither German nor English audio |
| `count(resolution(streams, '1080p')) >= 5 ? resolution(streams, '720p') : []` | Remove 720p results when there are already 5+ x 1080p results |

### Preferred Stream Expressions (priority tiers)

- **Dual audio** is expressed by nesting the language filter:
  `language(language(streams, 'German'), 'English')` → streams that have **both** languages.
- **Single audio** is the complement: `negate(<dual>, streams)`.
- **HDR** is any of `HDR`, `DV`, `HDR10`, `HDR10+`, `HLG`; **SDR** is its complement.
- A stream with an unknown/missing visual tag is treated as SDR.

### Sort

`sortCriteria.global` sorts by `streamExpressionMatched` (the priority tier), then
`resolution` (2160p → 1080p → 720p), then `quality`.

## Files

- `../../templates/german-english-usenet.json` — full importable template (metadata + config)
- `../../sel/german-english-usenet/excluded-stream-expressions.json` — ESE array (synced-URL format)
- `../../sel/german-english-usenet/preferred-stream-expressions.json` — PSE array (synced-URL format)
- `../../sel/german-english-usenet/ranked-stream-expressions.json` — RSE array (empty)

## Customising

- **HDR tags**: add or remove tags from the `visualTag(...)` lists in the PSE expressions
  (e.g. remove `'DV'` if your device has no Dolby Vision support).
- **720p threshold**: change `5` in the last ESE expression.
- **Add subtitles**: add `subtitle(streams, 'German')` / `subtitle(streams, 'English')`
  conditions to the ESE or Preferred expressions, or configure the Subtitle filters in the UI.
- **Prefer 1080p over 2160p**: reorder the `resolution` sort direction or adjust the sort keys.
- **Language fallback order**: swap the nesting to `language(language(streams, 'English'), 'German')`
  if you want English-first rather than German-first dual detection (the result is identical for
  dual-audio detection; the order only matters for readability).
