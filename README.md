# AIOStreams SEL Templates

A collection of reusable **Stream Expression Language (SEL)** templates and documentation for
[AIOStreams](https://github.com/Viren070/AIOStreams). Each profile is a self-contained
filtering + sorting setup that can be imported into AIOStreams with a single URL.

SEL lets you write custom logic to filter, rank and select streams. This repository stores
ready-to-import profiles so you don't have to re-derive the expressions yourself.

## Profiles

| Profile | Description | Import URL |
| --- | --- | --- |
| [German + English Usenet (Dual Audio)](profiles/german-english-usenet.md) | Usenet-only; prioritises dual-audio (German + English) over single-audio, HDR over SDR; 2160p/1080p/720p (720p gated behind 5 x 1080p). | [`templates/german-english-usenet.json`](templates/german-english-usenet.json) |

## Quick start

1. Open your AIOStreams instance → **Save & Install → Import Template**.
2. Paste the profile's import URL (from the table above), or the aggregate URL to import all profiles:
   `https://raw.githubusercontent.com/JCSynthTux/AIOStreams-SEL-Templates/main/templates.json`
3. Confirm import, then review **Filters** to make sure no native filters conflict with the profile.

> ⚠️ Only import templates from sources you trust.

These profiles are **Usenet-only** and require no debrid service. Ensure your Usenet add-ons
(Newznab/Torznab indexers) are already configured — the templates only apply filtering and
sorting logic.

## Repository structure

```
AIOStreams-SEL-Templates/
├── templates/                 # Full importable templates (metadata + config)
│   └── german-english-usenet.json
├── templates.json             # Aggregate: all templates, importable via a single URL
├── sel/                       # Raw SEL expression lists (synced-URL format)
│   └── german-english-usenet/
│       ├── excluded-stream-expressions.json
│       ├── preferred-stream-expressions.json
│       └── ranked-stream-expressions.json
├── profiles/                  # Detailed documentation per profile
│   └── german-english-usenet.md
├── docs/                      # General guides
│   └── import-guide.md
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Documentation

- [Import guide](docs/import-guide.md) — importing templates and using synced SEL URLs.
- [Profile: German + English Usenet](profiles/german-english-usenet.md) — detailed explanation
  of the first profile's expressions and how to customise them.
- Official SEL reference: [docs.aiostreams.viren070.me](https://docs.aiostreams.viren070.me/reference/stream-expressions/)

## Contributing

Add a new profile by creating:

1. `templates/<profile>.json` — full importable template.
2. `sel/<profile>/*.json` — the raw expression lists.
3. `profiles/<profile>.md` — documentation.
4. A row in the profile table above.
5. Add the template to `templates.json`.

## License

[WTFPL](LICENSE) — Do What The Fuck You Want To Public License.
