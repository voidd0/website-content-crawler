# Website Content Crawler: export to CSV or JSON with Python, Node.js or curl

Crawls any public website from a start URL, follows internal links up to a set depth, and extracts title, breadcrumbs, clean text body, and word count. Ideal for RAG ingestion and site audits.

This repository has ready scripts for the [Website Content Crawler: Pages to Clean Text](https://apify.com/v0iddo/website-content-crawler) Actor on Apify. The Actor does the
fetching, paging and cleaning; the scripts here run it with your input and save the results as a CSV or JSON file.

## Quick start

1. Create an Apify account and copy your API token from
   [Console > Settings > Integrations](https://console.apify.com/settings/integrations).
2. Install the client and run the script with the example input:

```bash
pip install "apify-client>=2"
export APIFY_TOKEN=your_token
python run.py input.json -o results.csv --max-items 50
```

Edit `input.json` for your own run. You can also run the Actor in the browser on its
[Apify page](https://apify.com/v0iddo/website-content-crawler), schedule it there, and connect its output to Google Sheets, Slack, Zapier or Make.

## Input

The example input (`input.json`):

```json
{
  "startUrls": [
    "https://docs.python.org/3/library/"
  ],
  "maxDepth": 1,
  "maxPagesPerUrl": 15,
  "maxItems": 20
}
```

| Field | Type | What it does |
| --- | --- | --- |
| `startUrls` | array | One or more page URLs to begin crawling from. |
| `maxDepth` | integer | How many link-hops from the start URL to follow. |
| `maxPagesPerUrl` | integer | Hard cap on pages fetched per start URL, independent of the global maxItems limit. |
| `includeUrlPattern` | string | Optional regex. Only URLs matching this pattern are crawled. |
| `excludeUrlPattern` | string | Optional regex. URLs matching this pattern are skipped. |
| `maxItems` | integer | The run stops after this many result rows. |

## Output

One row per result. A real row from a run of the example input (more in
[`examples/sample-output.json`](examples/sample-output.json)):

```json
{
  "url": "https://docs.python.org/3/library/",
  "title": "The Python standard library",
  "h1": "The Python standard library",
  "breadcrumbs": [],
  "description": "This library reference manual describes the standard library distributed with Python. It also describes some of the optional components that are commonly included in Python distributions. Elsewhere...",
  "textContent": "The Python standard library  This library reference manual describes the standard library\ndistributed with Python. It also describes some of the optional\ncomponents that are commonly included in Python distributions. Elsewhere, The Python Language Reference describes the exact...",
  "wordCount": 1779,
  "depth": 0
}
```

Fields: `url`, `title`, `h1`, `breadcrumbs`, `description`, `textContent`, `wordCount`, `depth`.

## Node.js

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });
const run = await client.actor('v0iddo/website-content-crawler').call({"startUrls": ["https://docs.python.org/3/library/"], "maxDepth": 1, "maxPagesPerUrl": 15, "maxItems": 20});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
```

Full example: [`examples/node/run.mjs`](examples/node/run.mjs).

## Any language: one HTTP request

```bash
curl -X POST "https://api.apify.com/v2/acts/v0iddo~website-content-crawler/run-sync-get-dataset-items" \
  -H "Authorization: Bearer $APIFY_TOKEN" -H 'Content-Type: application/json' \
  -d '{"startUrls": ["https://docs.python.org/3/library/"], "maxDepth": 1, "maxPagesPerUrl": 15, "maxItems": 20}'
```

The response is the JSON array of results.

## Cost

The Actor is paid per result: **$2.55 per 1,000 results**, nothing per run. `maxItems` caps the rows, and so the cost, of one run. A new Apify account gets $5 of platform credit every month.

## License

MIT for the code in this repository. Made by [vøiddo](https://voiddo.com).
