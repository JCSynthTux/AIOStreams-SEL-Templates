# Profile: German Usenet

A SEL-driven filtering and sorting profile for [AIOStreams](https://github.com/Viren070/AIOStreams).
It keeps **only Usenet releases** that include **German** audio (additional audio languages are allowed) and prefers **HDR** over SDR, excluding **AV1** encodes.

## Requirements

| Rule | Behaviour |
| --- | --- |
| Audio (primary) | Streams with **German** audio — required; other audio languages allowed |
| Stream type | **Usenet only** |
| Resolution | Only **2160p, 1080p, 720p** |
| 720p gating | 720p shown **only when fewer than 5 x 1080p** streams exist |
| Subtitles | Not required / not filtered |
| HDR | Allowed and **preferred over SDR** |
| Encode | **AV1 excluded** (unsupported by clients) |

## Priority order

Streams are tiered in the following order (highest first). Each stream matches only the
**first** Preferred Stream Expression it satisfies.

1. **German + HDR**
2. **German + SDR**

## How it works

### Excluded Stream Expressions (hard filters)

| Expression | Purpose |
| --- | --- |
| `negate(type(streams, 'usenet', 'stremio-usenet'), streams)` | Remove every non-Usenet stream |
| `negate(resolution(streams, '2160p', '1080p', '720p'), streams)` | Remove every resolution except 2160p / 1080p / 720p |
| `negate(language(streams, 'German'), streams)` | Remove streams without German audio |
| `encode(streams, 'AV1')` | Remove AV1 encodes (unsupported by clients) |
| `count(resolution(streams, '1080p')) >= 5 ? resolution(streams, '720p') : []` | Remove 720p results when there are already 5+ x 1080p results |

### Preferred Stream Expressions (priority tiers)

- **HDR** is any of `HDR`, `DV`, `HDR10`, `HDR10+`, `HLG`; **SDR** is its complement.
- A stream with an unknown/missing visual tag is treated as SDR.

### Sort

`sortCriteria.global` sorts by `streamExpressionMatched` (the priority tier), then
`resolution` (2160p → 1080p → 720p), then `quality`.

## Files

- `../../templates/german-usenet.json` — full importable template (metadata + config)
- `../../sel/german-usenet/excluded-stream-expressions.json` — ESE array (synced-URL format)
- `../../sel/german-usenet/preferred-stream-expressions.json` — PSE array (synced-URL format)
- `../../sel/german-usenet/ranked-stream-expressions.json` — RSE array (empty)

## Customising

- **HDR tags**: add or remove tags from the `visualTag(...)` lists in the PSE expressions
  (e.g. remove `'DV'` if your device has no Dolby Vision support).
- **720p threshold**: change `5` in the last ESE expression.
- **AV1 exclusion**: remove the `encode(streams, 'AV1')` ESE
  if your clients gain AV1 support.
- **Add English as a fallback**: swap the audio ESE for the dual-audio variant from the
  [German + English Usenet profile](german-english-usenet.md), or add
  `language(streams, 'English')` conditions.
- **Add subtitles**: add `subtitle(streams, 'German')` conditions to the ESE or Preferred
  expressions, or configure the Subtitle filters in the UI.
- **Prefer 1080p over 2160p**: reorder the `resolution` sort direction or adjust the sort keys.
- **Language detection caveat**: releases whose titles do not explicitly mention `German` (e.g.
  those labelled `MULTi`, or with no parseable language) are not detected as German and are
  filtered out. To include them anyway, merge `language(streams, 'Multi')` and/or
  `language(streams, 'Dual Audio')` into the audio exclusion — at the cost of admitting some
  releases without a German track. Native Filters are applied in addition to the profile's SEL
  expressions, so review them after importing and make sure they don't contradict the profile
  rules; see the [import guide](../docs/import-guide.md).
