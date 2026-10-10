# remove.bg API alternative: migrate before December 1, 2026

The remove.bg self-service API stops on **December 1, 2026** (background removal moves to Leonardo.Ai, and remove.bg API keys stop working). This page shows how to keep existing code working by pointing it at a **remove.bg-compatible endpoint**. Most integrations only need to change two lines.

The examples use [remove.bg API Alternative](https://apify.com/digitalni.produkty.pro.zivot/removebg-api-dropin), an endpoint I built and run on Apify ($0.005 per image, no subscription). The request format is the standard remove.bg one, so the same changes work with any other service that copies that API.

```diff
- POST https://api.remove.bg/v1.0/removebg                     X-Api-Key: <REMOVE_BG_KEY>
+ POST https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg
+                                                              Authorization: Bearer <APIFY_TOKEN>
```

Get the token from a free [Apify account](https://console.apify.com/sign-up) (Settings → API & Integrations). You can also append `?token=<APIFY_TOKEN>` to the URL instead of sending the header.

## curl

```bash
curl -H "Authorization: Bearer $APIFY_TOKEN" \
     -F "image_file=@shoe.jpg" \
     -F "size=auto" \
     -F "bg_color=ffffff" \
     -o shoe-white.png \
     https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg
```

## Python (requests)

```python
import os
import requests

ENDPOINT = "https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg"

with open("shoe.jpg", "rb") as f:
    r = requests.post(
        ENDPOINT,
        files={"image_file": f},
        data={"size": "auto", "crop": "true", "crop_margin": "5%"},
        headers={"Authorization": f"Bearer {os.environ['APIFY_TOKEN']}"},
    )
r.raise_for_status()
open("shoe.png", "wb").write(r.content)
print(r.headers["X-Width"], r.headers["X-Height"])
```

## Python (`removebg` package)

A [pull request](https://github.com/brilam/remove-bg/pull/31) adds `api_endpoint` and `headers` arguments to the popular `removebg` package. Until it is merged and released, use `requests` as shown above. After the release:

```python
from removebg import RemoveBg

rmbg = RemoveBg(
    "unused",
    "error.log",
    api_endpoint="https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg",
    headers={"Authorization": "Bearer <APIFY_TOKEN>"},
)
rmbg.remove_background_from_img_url("https://example.com/shoe.jpg")
```

## Node.js (fetch, Node 18+)

```js
import { writeFile } from 'node:fs/promises';

const form = new FormData();
form.append('image_url', 'https://example.com/shoe.jpg');
form.append('size', 'full');
form.append('format', 'png');

const res = await fetch('https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg', {
    method: 'POST',
    headers: { Authorization: `Bearer ${process.env.APIFY_TOKEN}` },
    body: form,
});
if (!res.ok) throw new Error(JSON.stringify(await res.json()));
await writeFile('shoe.png', Buffer.from(await res.arrayBuffer()));
```

## Node.js (`remove.bg` package)

A [pull request](https://github.com/EddyVerbruggen/remove.bg/pull/15) adds `apiEndpoint` and `headers` options to the `remove.bg` npm package. After it is released:

```js
import { removeBackgroundFromImageUrl } from 'remove.bg';

await removeBackgroundFromImageUrl({
    url: 'https://example.com/shoe.jpg',
    apiKey: 'unused',
    apiEndpoint: 'https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg',
    headers: { Authorization: `Bearer ${process.env.APIFY_TOKEN}` },
    size: 'regular',
    outputFile: 'shoe.png',
});
```

## PHP / Laravel (`mtownsend/remove-bg`)

No library change is needed, because the endpoint is a public property:

```php
use Mtownsend\RemoveBg\RemoveBg;

$removebg = new RemoveBg('unused');
$removebg->endpoint = 'https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg';
$removebg->header('Authorization', 'Bearer ' . getenv('APIFY_TOKEN'))
         ->url('https://example.com/shoe.jpg')
         ->save('shoe.png');
```

## n8n, Make and Zapier

Replace the remove.bg step with a generic HTTP request:

| Setting | Value |
|---|---|
| Method | `POST` |
| URL | `https://digitalni-produkty-pr--removebg-api-dropin.apify.actor/v1.0/removebg` |
| Header | `Authorization: Bearer <APIFY_TOKEN>` |
| Body | multipart form data: `image_url` (or a file in `image_file`), `size` = `full`, plus any other remove.bg field |
| Response | a file (binary image), or JSON with `data.result_b64` if you add the header `Accept: application/json` |

- **n8n:** HTTP Request node → Send Body → Body Content Type *Form-Data*. Under Options → Response, set Response Format to *File*.
- **Make:** HTTP → *Make a request* (multipart/form-data body), then pass the returned file to the next module.
- **Zapier:** Webhooks by Zapier → *Custom Request*, or Code by Zapier with the Python example above.

## Supported parameters

| remove.bg parameter | Status |
|---|---|
| `image_file`, `image_file_b64`, `image_url` | ✅ |
| `size` (`preview` default, `small`, `regular`, `medium`, `hd`, `full`, `4k`, `auto`) | ✅ up to 25 MP |
| `format` (`auto`, `png`, `jpg`, `webp`, `zip`) | ✅ |
| `channels` (`rgba`, `alpha`) | ✅ |
| `bg_color`, `bg_image_url`, `bg_image_file` | ✅ |
| `crop`, `crop_margin`, `roi`, `scale`, `position` | ✅ |
| `type`, `type_level`, `add_shadow`, `semitransparency` | accepted, ignored |

Responses keep the remove.bg shape: the image with `X-Width`, `X-Height`, `X-Foreground-*`, `X-Type` and `X-Credits-Charged` headers, JSON with `result_b64` on `Accept: application/json`, errors as `{"errors": [{"title", "code"}]}` and HTTP 402 when your spending limit is reached. `GET /v1.0/account` is available too.

## Bulk jobs

For thousands of images, the batch tool [AI Background Remover](https://apify.com/digitalni.produkty.pro.zivot/background-remover) takes a list of image URLs (or the dataset of a product scraper) and returns transparent PNGs, white backgrounds or masks, at the same $0.005 per image.

## Notes

- Quality: the endpoint uses the open-source ISNet model at full resolution. It works best on products and objects. remove.bg's own model is more polished on fine hair and glass, so test a sample of your own images first.
- Not affiliated with remove.bg, Kaleido AI, Canva or Leonardo.Ai. "remove.bg" is a trademark of its owner and is used here only to describe API compatibility.
- Questions or an integration that behaves differently from remove.bg: open an issue in this repository.
