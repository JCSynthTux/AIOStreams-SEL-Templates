# Profile: German Usenet

A SEL-driven filtering and sorting profile for [AIOStreams](https://github.com/Viren070/AIOStreams).
It keeps **only Usenet releases** with **German** audio and prefers **HDR** over SDR.

## Requirements

| Rule | Behaviour |
| --- | --- |
| Audio (primary) | Streams with **German** audio only |
| Stream type | **Usenet only** |
| Resolution | Only **2160p, 1080p, 720p** |
| 720p gating | 720p shown **only when fewer than 5 x 1080p** streams exist |
| Subtitles | Not required / not filtered |
| HDR | Allowed and **preferred over SDR** |

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
- **Add English as a fallback**: swap the audio ESE for the dual-audio variant from the
  [German + English Usenet profile](german-english-usenet.md), or add
  `language(streams, 'English')` conditions.
- **Add subtitles**: add `subtitle(streams, 'German')` conditions to the ESE or Preferred
  expressions, or configure the Subtitle filters in the UI.
- **Prefer 1080p over 2160p**: reorder the `resolution` sort direction or adjust the sort keys.
