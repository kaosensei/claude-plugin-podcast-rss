# claude-plugin-podcast-rss

A Claude Code plugin that extracts RSS feed URLs from Apple Podcasts links — no third-party tools or authentication required.

## How It Works

Apple exposes podcast RSS feed URLs through their public [iTunes Lookup API](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/iTuneSearchAPI/). This plugin uses that API to retrieve the original RSS feed from any Apple Podcasts link.

```
Apple Podcasts URL → extract ID → iTunes Lookup API → RSS feed URL
```

## Installation

```bash
claude plugin install github:kaosensei/claude-plugin-podcast-rss
```

## Usage

Just paste an Apple Podcasts URL and ask for the RSS feed:

```
get rss https://podcasts.apple.com/us/podcast/the-daily/id1200361736
```

Or just say "get rss" after sharing a link in the conversation.

**Example output:**
```
**The Daily** RSS Feed:

https://feeds.simplecast.com/Sl5CSM3S
```

## Requirements

- macOS (uses `curl` and `python3`, both available by default)
- An Apple Podcasts URL (`podcasts.apple.com/...`)

## Limitations

- **Apple Podcasts only** — Spotify-exclusive shows have no public RSS feed
- Requires network access to call the iTunes Lookup API

## License

MIT
