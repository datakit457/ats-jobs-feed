# ATS Jobs Feed: Greenhouse, Lever, Ashby, Workable, SmartRecruiters, Recruitee, Personio, Teamtailor and Workday jobs in one format

Get every open job from any company that hosts its careers page on **Greenhouse, Lever, Ashby, Workable, SmartRecruiters, Recruitee, Personio, Teamtailor or Workday**, using their public job-board APIs. No login, no browser, no proxies. Output is one normalized format across all nine systems, and **diff mode returns only jobs that are new since your last run**, so you can run it daily and get a clean feed.

## Who it's for
- Job boards and newsletters that list jobs from a fixed set of companies (e.g. "AI startups", "climate tech").
- Recruiters and sales teams watching hiring signals at target accounts.
- Job seekers tracking 10-200 companies without checking each careers page.

## No slugs? Search the built-in startup directory
Set `directory` to `startups` and the Actor scans the job boards of 800+ tech companies (Y Combinator alumni and well-known tech firms on Greenhouse, Lever, Ashby and Workable, verified November 2026). Combine with `keywords`, `locations`, `remoteOnly`, and `directoryIndustries` (e.g. `Fintech`).

```json
{"directory": "startups", "keywords": ["data engineer"], "locations": ["london", "remote"], "mode": "newSinceLastRun"}
```

## Input
- `companies`: one per line. `greenhouse:stripe`, `lever:palantir`, `ashby:openai`, `workable:huggingface`, or a careers URL (`https://boards.greenhouse.io/stripe`, `https://jobs.lever.co/palantir`, `https://jobs.ashbyhq.com/openai`, `https://apply.workable.com/huggingface`). A bare slug tries all eight (add `personio:mobimeo` or a `*.jobs.personio.de` URL for Personio).
- `mode`: `newSinceLastRun` (default) or `all`.
- Filters: `keywords` (title/department), `locations`, `remoteOnly`.
- `includeDescription`: add plain-text descriptions.
- `stateKey`: keep separate "seen" lists for different feeds.

## Output (one item per job)
```json
{
  "id": "ashby:openai:8fb1615c-...",
  "title": "Technical Program Manager, Compute Infrastructure",
  "company": "openai",
  "department": "Technical Program Management",
  "location": "San Francisco",
  "remote": true,
  "employmentType": "FullTime",
  "salary": {"min": 207000, "max": 285000, "currency": "USD", "interval": "1 YEAR", "summary": "$207K - $285K"},
  "postedAt": "2026-11-05T02:20:02.702Z",
  "url": "https://jobs.ashbyhq.com/openai/8fb1615c-...",
  "ats": "ashby",
  "firstSeen": "2026-11-08"
}
```
Salary is filled where the company publishes it (Ashby and some Lever boards). A run summary per board is saved in the `SUMMARY` record.

## Call it from code
```bash
curl -X POST "https://api.apify.com/v2/acts/datakit~ats-jobs-feed/run-sync-get-dataset-items?token=YOUR_TOKEN" \
  -H 'Content-Type: application/json' \
  -d '{"companies":["greenhouse:stripe","ashby:openai"],"keywords":["engineer"],"mode":"newSinceLastRun"}'
```

## Notes
- Uses only the public job-board endpoints these ATS vendors provide for listing jobs on websites. No personal data is collected.
- The first run in diff mode returns all current jobs; later runs return only new ones.
- Cost is tiny: plain JSON requests, 256 MB memory is enough.

Built by datakit. Issues or a missing ATS (for example BambooHR)? Open an issue on the Actor page.

**Workday note:** give the company's full careers URL (`https://<company>.wd<N>.myworkdayjobs.com/<site>`). Workday's public job list gives title, location summary, an approximate posted date (from "Posted N days ago"; jobs older than 30 days get no date) and the job URL. Turn on `includeDescription` to also get the full description, employment type and all locations (one extra request per job, so slower). Workday returns at most 2,000 jobs per career site.

**Teamtailor note:** Teamtailor publishes jobs through a public RSS feed (`<company>.teamtailor.com/jobs.rss`), which gives title, department, location, remote status, posted date and description, but no salary or employment type, and some companies' feeds list only part of their jobs.


## FAQ
**How do I find a company's ATS and slug?** Open its careers page: the URL usually looks like `boards.greenhouse.io/<slug>`, `jobs.lever.co/<slug>`, `jobs.ashbyhq.com/<slug>`, `apply.workable.com/<slug>`, `<slug>.recruitee.com`, `<slug>.jobs.personio.de` or `<slug>.teamtailor.com`. Use `ats:slug` in `companies`. For Workday (`<company>.wd5.myworkdayjobs.com/<site>`) paste the whole URL.

**Can I get only new jobs?** Yes, set `mode` to `newSinceLastRun`. The first run returns everything, later runs only jobs not seen before.

**What happens with a wrong slug?** That company is skipped with a log warning; the others still run.

**Can I schedule it?** Yes, use Apify schedules or any cron calling the API example above.

**Does it collect personal data?** No, only public job listings.
