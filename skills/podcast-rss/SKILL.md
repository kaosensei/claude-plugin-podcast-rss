---
name: podcast-rss
description: Extract RSS feed URL from an Apple Podcasts link. Use when the user says "podcast rss", "get rss", "find rss feed", or provides an Apple Podcasts URL and wants the RSS feed.
argument-hint: "[Apple Podcasts URL]"
allowed-tools: [Bash]
---

# Podcast RSS Extractor (Apple Podcasts)

Extract the original RSS feed URL from an Apple Podcasts link.

## How It Works

Apple Podcasts URL format:
```
https://podcasts.apple.com/us/podcast/show-name/id123456789
```

The iTunes Lookup API (public, no auth required) exposes the RSS feed URL:
```
https://itunes.apple.com/lookup?id=123456789
```

The JSON response contains `results[0].feedUrl` — the original RSS URL.

## Steps

### Step 1: Get the URL

- If `$ARGUMENTS` is provided, use it directly
- Otherwise, find the Apple Podcasts URL from the conversation

### Step 2: Extract Podcast ID

Use regex to extract the numeric ID from `/id<number>` in the URL.

### Step 3: Query iTunes Lookup API

Call `https://itunes.apple.com/lookup?id=<PODCAST_ID>` and parse the JSON response.

### Step 4: Output Result

On success:
```
**<Podcast Name>** RSS Feed:

<RSS Feed URL>
```

On failure (feedUrl missing):
```
Could not find RSS Feed.

Possible reasons:
- Spotify-exclusive show (no public RSS)
- Invalid Podcast ID
- iTunes API temporarily unavailable
```

## Full Script

Execute this Bash command when the skill is invoked:

```bash
PODCAST_URL="$ARGUMENTS"
PODCAST_ID=$(echo "$PODCAST_URL" | grep -oE '/id([0-9]+)' | grep -oE '[0-9]+')

if [ -z "$PODCAST_ID" ]; then
  echo "Error: Could not find Podcast ID in URL. Please provide a valid Apple Podcasts link."
  exit 1
fi

RESULT=$(curl -s "https://itunes.apple.com/lookup?id=$PODCAST_ID")
FEED_URL=$(echo "$RESULT" | python3 -c "import sys, json; data=json.load(sys.stdin); results=data.get('results',[]); print(results[0].get('feedUrl','NOT_FOUND') if results else 'NOT_FOUND')")
PODCAST_NAME=$(echo "$RESULT" | python3 -c "import sys, json; data=json.load(sys.stdin); results=data.get('results',[]); print(results[0].get('trackName','Unknown') if results else 'Unknown')")

if [ "$FEED_URL" = "NOT_FOUND" ]; then
  echo "RSS Feed not found (possibly a Spotify-exclusive show with no public RSS)"
else
  echo "**${PODCAST_NAME}** RSS Feed:"
  echo ""
  echo "$FEED_URL"
fi
```

## Notes

- Only works with Apple Podcasts URLs (`podcasts.apple.com`)
- Spotify-exclusive shows have no public RSS and cannot be handled by this skill
- The iTunes Lookup API is public and requires no authentication
- Depends on: `curl`, `python3` (both available by default on macOS)
