# Scanned PDF to text with OCR (Python, tested example)

A scanned PDF has no text layer, so `pdftotext`, PyPDF and most extractors return an empty string. This example turns a scanned PDF into clean text (and Markdown, per-page text or RAG chunks) with the [PDF Text Extractor & OCR](https://apify.com/digitalni.produkty.pro.zivot/pdf-text-extractor) Actor on Apify, which uses Tesseract OCR only for pages that need it.

Disclosure: I maintain this Actor. This page was written with AI assistance; the example below was run and checked on October 13, 2026.

## Example

```bash
pip install apify-client
```

```python
from apify_client import ApifyClient

client = ApifyClient("<APIFY_TOKEN>")  # free account token from console.apify.com
run = client.actor("digitalni.produkty.pro.zivot/pdf-text-extractor").call(run_input={
    "urls": ["https://raw.githubusercontent.com/ocrmypdf/OCRmyPDF/main/tests/resources/skew.pdf"],
    "ocrMode": "auto",       # OCR only pages without a text layer
    "ocrLanguages": "eng",   # e.g. "de", "fr, en", "cs" (19 languages)
    "includePages": True,    # text per page, useful for citations
})
doc = next(client.dataset(run["defaultDatasetId"]).iterate_items())
print(doc["ocrPages"], doc["wordCount"])  # [1] 693
print(doc["text"][:60])                    # The LinnSequencer / 32 Track MIDI Sequence Recorder
```

Result of the test run: the one-page scan (a slightly skewed brochure page from the OCRmyPDF test files) had no text layer. It was rendered at 300 DPI, read by OCR and returned 693 words with paragraphs and bullet points in about 9 seconds.

Other inputs: a list of PDF URLs, or a web page URL, in which case every PDF linked from that page is found and extracted. Typed (not scanned) pages are extracted directly without OCR.

## Cost

Pay per use, no subscription:

- $0.003 per extracted PDF (any number of pages),
- $0.005 per scanned page read by OCR.

The example above costs $0.008. A 10-page scanned contract costs $0.053; 1,000 typed PDFs cost $3. Failed, blank and password-protected files (without password) are not charged.

## Limits

- Quality depends on the scan. Clean 200-300 DPI scans give near-perfect text; phone photos of paper, handwriting, stamps and very small print come out much weaker, and OCR errors (wrong letters, merged words) are possible.
- Tables come back as lines of text, not as cells. Multi-column scans may mix columns.
- Pick the right OCR language; the wrong one loses accents and makes more mistakes.
- Use *Max OCR pages per PDF* as a cost cap for very long scanned books.

Questions or a missing feature: open an issue on the [Actor page](https://apify.com/digitalni.produkty.pro.zivot/pdf-text-extractor/issues).
