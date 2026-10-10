# Apify Actors by Digitální produkty pro život

Small, reliable, pay-per-result tools for SEO, web data, documents and AI pipelines. They run in the cloud on [Apify](https://apify.com): no servers, no API keys, no subscriptions. Use them from the web UI, on a schedule, via API, from Make/Zapier/n8n, or as tools for AI agents through the Apify MCP server.

Failed items are never charged.

## SEO & websites

| Tool | What it does | Price |
|---|---|---|
| [Sitemap URL Extractor](https://apify.com/digitalni.produkty.pro.zivot/sitemap-url-extractor) | Every URL from a website's sitemaps: robots.txt discovery, nested indexes, .gz, lastmod filter, hreflang | $0.50 / 1,000 URLs |
| [Broken Link Checker](https://apify.com/digitalni.produkty.pro.zivot/broken-link-checker) | Crawl a site, find 404s, dead domains and timeouts with the page and anchor text where each appears | $2 / 1,000 pages |
| [PageSpeed & Lighthouse Audit](https://apify.com/digitalni.produkty.pro.zivot/pagespeed-audit) | Bulk Lighthouse scores, Core Web Vitals and top fixes, mobile and desktop | $0.025 / audit |
| [Website Screenshot](https://apify.com/digitalni.produkty.pro.zivot/website-screenshot) | Full-page, mobile or PDF screenshots, cookie banners hidden | $0.005 / screenshot |
| [Domain WHOIS & DNS Lookup](https://apify.com/digitalni.produkty.pro.zivot/domain-whois-lookup) | Registrar, expiry, domain age, DNS, SPF/DMARC and SSL expiry for thousands of domains | $2 / 1,000 domains |

## Images & e-commerce

| Tool | What it does | Price |
|---|---|---|
| [remove.bg API Alternative](https://apify.com/digitalni.produkty.pro.zivot/removebg-api-dropin) | Drop-in replacement for the remove.bg API (ends Dec 1, 2026): same endpoint, parameters and responses. [Migration guide](removebg-api-alternative/) | $0.005 / image |
| [AI Background Remover](https://apify.com/digitalni.produkty.pro.zivot/background-remover) | Bulk background removal: transparent PNG/WebP, white or any color, masks, crop to subject | $0.005 / image |
| [Product Photo Prep](https://apify.com/digitalni.produkty.pro.zivot/product-photo-prep) | Supplier photos to marketplace-ready images: background removal, white background, square, presets for Shopify, Amazon and Etsy | $0.01 / photo |
| [AI Image Upscaler](https://apify.com/digitalni.produkty.pro.zivot/ai-image-upscaler) | 2x/4x Real-ESRGAN upscaling for photos and product images, no GPU | $0.01 / image |
| [App Icon & Screenshot Generator](https://apify.com/digitalni.produkty.pro.zivot/app-icon-generator) | Every app icon size from one image (Xcode, Android adaptive, PWA) plus store screenshot sizes | $0.03 / icon set |

## Documents & AI

| Tool | What it does | Price |
|---|---|---|
| [PDF Text Extractor](https://apify.com/digitalni.produkty.pro.zivot/pdf-text-extractor) | Text, Markdown and RAG-ready chunks from PDF URLs, or every PDF linked from a page | $0.003 / PDF |
| [E-book Converter](https://apify.com/digitalni.produkty.pro.zivot/ebook-converter) | Word, Google Docs, Markdown or HTML manuscript to a print-ready PDF and an EPUB with cover and table of contents | $0.05 / document |

## Monitoring & data feeds

| Tool | What it does | Price |
|---|---|---|
| [RSS Feed Reader & Monitor](https://apify.com/digitalni.produkty.pro.zivot/rss-feed-reader) | Any RSS/Atom/podcast feed, auto-discovery from a website, only-new-items monitoring | $0.003 / run + $1 / 1,000 items |
| [ATS Jobs](https://apify.com/digitalni.produkty.pro.zivot/ats-jobs) | All open jobs from Greenhouse, Lever, Ashby, SmartRecruiters, Recruitee and Personio boards via official APIs | $1.50 / 1,000 jobs |
| [App Store Reviews](https://apify.com/digitalni.produkty.pro.zivot/app-store-reviews) | iOS app reviews from 50+ countries, filters and new-review monitoring | $0.15 / 1,000 reviews |

## Quick start (Python)

```python
from apify_client import ApifyClient

client = ApifyClient("<YOUR_API_TOKEN>")  # Apify Console → Settings → Integrations
run = client.actor("digitalni.produkty.pro.zivot/sitemap-url-extractor").call(
    run_input={"startUrls": ["https://crawlee.dev"], "maxUrls": 1000}
)
for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item["url"])
```

## Quick start (Node.js)

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: '<YOUR_API_TOKEN>' });
const run = await client.actor('digitalni.produkty.pro.zivot/pdf-text-extractor').call({
    urls: ['https://arxiv.org/pdf/1706.03762'],
    includeMarkdown: true,
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items[0].markdown);
```

## Use with AI agents

Add the [Apify MCP server](https://mcp.apify.com) to Claude, ChatGPT, Cursor or any MCP client and these Actors become tools your agent can call, e.g. "extract all URLs from example.com's sitemap" or "summarize the PDFs linked on this page".

## Feedback

Found a bug or need a feature? Open an issue on the Actor's page (Issues tab). I usually fix things within a day.
