# Profile: Russian + Ukrainian Usenet

A SEL-driven filtering and sorting profile for [AIOStreams](https://github.com/Viren070/AIOStreams).
It keeps **only Usenet releases** with **Russian or Ukrainian** audio and prefers **HDR** over SDR, excluding **AV1** encodes.

## Requirements

| Rule | Behaviour |
| --- | --- |
| Audio (primary) | Streams with **Russian or Ukrainian** audio |
| Stream type | **Usenet only** |
| Resolution | Only **2160p, 1080p, 720p** |
| 720p gating | 720p shown **only when fewer than 5 x 1080p** streams exist |
| Subtitles | Not required / not filtered |
| HDR | Allowed and **preferred over SDR** |
| Encode | **AV1 excluded** (unsupported by clients) |

## Priority order

Streams are tiered in the following order (highest first). Each stream matches only the
**first** Preferred Stream Expression it satisfies.

1. **Russian/Ukrainian + HDR**
2. **Russian/Ukrainian + SDR**

## How it works

### Excluded Stream Expressions (hard filters)

| Expression | Purpose |
| --- | --- |
| `negate(type(streams, 'usenet', 'stremio-usenet'), streams)` | Remove every non-Usenet stream |
| `negate(resolution(streams, '2160p', '1080p', '720p'), streams)` | Remove every resolution except 2160p / 1080p / 720p |
| `negate(merge(language(streams, 'Russian'), language(streams, 'Ukrainian')), streams)` | Remove streams with neither Russian nor Ukrainian audio |
| `negate(encode(streams, 'AV1'), streams)` | Remove AV1 encodes (unsupported by clients) |
| `count(resolution(streams, '1080p')) >= 5 ? resolution(streams, '720p') : []` | Remove 720p results when there are already 5+ x 1080p results |

### Preferred Stream Expressions (priority tiers)

- **Russian or Ukrainian audio** is expressed by merging the two language filters:
  `merge(language(streams, 'Russian'), language(streams, 'Ukrainian'))`.
- **HDR** is any of `HDR`, `DV`, `HDR10`, `HDR10+`, `HLG`; **SDR** is its complement.
- A stream with an unknown/missing visual tag is treated as SDR.

### Sort

`sortCriteria.global` sorts by `streamExpressionMatched` (the priority tier), then
`resolution` (2160p → 1080p → 720p), then `quality`.

## Files

- `../../templates/russian-ukrainian-usenet.json` — full importable template (metadata + config)
- `../../sel/russian-ukrainian-usenet/excluded-stream-expressions.json` — ESE array (synced-URL format)
- `../../sel/russian-ukrainian-usenet/preferred-stream-expressions.json` — PSE array (synced-URL format)
- `../../sel/russian-ukrainian-usenet/ranked-stream-expressions.json` — RSE array (empty)

## Customising

- **HDR tags**: add or remove tags from the `visualTag(...)` lists in the PSE expressions
  (e.g. remove `'DV'` if your device has no Dolby Vision support).
- **720p threshold**: change `5` in the last ESE expression.
- **AV1 exclusion**: remove the `negate(encode(streams, 'AV1'), streams)` ESE
  if your clients gain AV1 support.
- **Dual-audio prioritisation**: to prefer releases with both Russian *and* Ukrainian audio,
  add nested-language tiers like in the
  [German + English Usenet profile](german-english-usenet.md):
  `language(language(streams, 'Russian'), 'Ukrainian')`.
- **Add subtitles**: add `subtitle(streams, 'Russian')` / `subtitle(streams, 'Ukrainian')`
  conditions to the ESE or Preferred expressions, or configure the Subtitle filters in the UI.
- **Prefer 1080p over 2160p**: reorder the `resolution` sort direction or adjust the sort keys.
