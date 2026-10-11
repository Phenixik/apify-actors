# Career Site Jobs API: Greenhouse, Lever, Ashby, SmartRecruiters, Recruitee and Personio jobs in one schema

Search the open jobs that thousands of companies publish on their own career sites, or pull every job of the companies you choose. One unified JSON schema across six applicant tracking systems (ATS), from their official public job board APIs.

- **Search mode:** a daily index of **400,000+ open jobs at 11,000+ companies**. Filter by job title, location, remote, department, company name, ATS and posting date. Results in seconds, newest first.
- **Company mode:** pass careers page links or company names (`ramp`, `notion`, `boards.greenhouse.io/airbnb`, `lever:palantir`) and get every open role live. The ATS is detected automatically.
- **Monitoring:** `onlyNewJobs` returns only postings not seen in earlier runs, for daily feeds.
- **Price:** $1.50 per 1,000 jobs returned, nothing per company or per run.

Runs on Apify: [Career Site Jobs API](https://apify.com/digitalni.produkty.pro.zivot/ats-jobs). You need a free Apify account for the API token.

## Python

```python
from apify_client import ApifyClient

client = ApifyClient("<APIFY_TOKEN>")
run = client.actor("digitalni.produkty.pro.zivot/ats-jobs").call(run_input={
    "titleKeywords": ["data engineer", "analytics engineer"],
    "locations": ["remote", "germany"],
    "postedWithinDays": 7,
    "maxJobs": 300,
})
for job in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(job["postedAt"][:10], job["company"], "|", job["title"], "|", job["location"], "|", job["url"])
```

## Node.js

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });
const run = await client.actor('digitalni.produkty.pro.zivot/ats-jobs').call({
    titleKeywords: ['product manager'],
    locations: ['london', 'remote'],
    remoteOnly: false,
    maxJobs: 200,
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items.length, items[0]);
```

## curl (synchronous, returns the jobs as JSON)

```bash
curl -X POST "https://api.apify.com/v2/acts/digitalni.produkty.pro.zivot~ats-jobs/run-sync-get-dataset-items?token=$APIFY_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"titleKeywords": ["backend engineer"], "remoteOnly": true, "postedWithinDays": 3, "maxJobs": 100}'
```

## Track specific companies

```python
run = client.actor("digitalni.produkty.pro.zivot/ats-jobs").call(run_input={
    "companies": ["ramp", "notion", "https://boards.greenhouse.io/airbnb", "lever:palantir"],
    "includeDescription": True,
})
```

## Output schema

| Field | Example |
|---|---|
| `ats` | `greenhouse`, `lever`, `ashby`, `smartrecruiters`, `recruitee`, `personio` |
| `company`, `companySlug` | `Ramp`, `ramp` |
| `title`, `department`, `team` | `Security Engineer, Cloud`, `Engineering`, `Backend` |
| `location`, `locations`, `country` | `New York, NY (HQ)`, `[...]`, `USA` |
| `workplaceType`, `remote` | `hybrid`, `true` |
| `employmentType`, `seniority` | `FullTime`, `Mid-Senior` |
| `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryInterval`, `salaryText` | `211400`, `290600`, `USD`, `year`, `$211.4K - $290.6K` |
| `postedAt`, `updatedAt` | ISO 8601 |
| `url`, `applyUrl` | links to the employer's own posting |
| `descriptionText`, `descriptionHtml` | full job description |

## Use cases

- Niche job boards (remote, AI, climate, fintech, a country or a city) with a daily feed of fresh employer postings.
- Hiring signals for sales teams: which companies are hiring for which roles right now.
- Salary and remote-work research by role, company or region.
- "Jobs at our portfolio companies" pages for VCs and accelerators.

## Notes

- Coverage: companies on the six supported ATS, mostly tech, startups and scale-ups. The index refreshes daily; company mode is live.
- Job descriptions belong to the employers. Link back to the original posting, where candidates apply.
- Missing a company or an ATS? Open an issue in this repository or on the Actor page.
