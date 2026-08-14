# Import Guide

There are two ways to use the SEL templates in this repository:

1. **Import the full template** (recommended) — applies filtering, sorting and expressions in one go.
2. **Use the SEL JSON files directly** — copy/paste or sync individual expression lists.

## 1. Import the full template

In AIOStreams, open **Save & Install → Import Template** and paste the URL of a template file:

```
https://raw.githubusercontent.com/JCSynthTux/AIOStreams-SEL-Templates/main/templates/german-english-usenet.json
```

Alternatively, use a deep link (replace the host with your own instance):

```
https://your-aiostreams.example.com/stremio/configure?template=https://raw.githubusercontent.com/JCSynthTux/AIOStreams-SEL-Templates/main/templates/german-english-usenet.json
```

> ⚠️ Only import templates from sources you trust.

Because this profile is Usenet-only, no debrid service is required. Make sure your Usenet
add-ons (Newznab/Torznab indexers) are already configured on your instance — the template
only applies the filtering/sorting logic and does not add add-ons.

### Important: keep your native filters clean

This template applies its filtering entirely through the Stream Expression Language. If your
instance already has conflicting native filters (e.g. excluded resolutions, required languages,
stream-type filters), review them under **Filters** after importing and make sure they do not
contradict the profile rules.

## 2. Use the SEL JSON files directly

Each profile in `sel/<profile>/` contains the raw expression lists in AIOStreams' synced-URL
format (`[{ "expression": "...", "enabled": true }]`):

- `excluded-stream-expressions.json`
- `preferred-stream-expressions.json`
- `ranked-stream-expressions.json`

You can paste the expressions into the corresponding fields under
**Filters → Stream Expressions**, or self-host them as synced URLs.

### Self-hosted synced URLs

If you self-host AIOStreams, enable `SEL_SYNC_ACCESS=all` in your `.env` and add the raw
file URLs to the relevant sync fields (or via the UI). Synced expressions update automatically
when you push changes to the repository.
