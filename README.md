# ATS Jobs Feed: Greenhouse, Lever, Ashby and Workable jobs in one format

Get every open job from any company that hosts its careers page on **Greenhouse, Lever, Ashby or Workable**, using their official public job-board APIs. No login, no browser, no proxies. Output is one normalized format across all four systems, and **diff mode returns only jobs that are new since your last run**, so you can run it daily and get a clean feed.

## Who it's for
- Job boards and newsletters that list jobs from a fixed set of companies (e.g. "AI startups", "climate tech").
- Recruiters and sales teams watching hiring signals at target accounts.
- Job seekers tracking 10-200 companies without checking each careers page.

## Input
- `companies`: one per line. `greenhouse:stripe`, `lever:palantir`, `ashby:openai`, `workable:huggingface`, or a careers URL (`https://boards.greenhouse.io/stripe`, `https://jobs.lever.co/palantir`, `https://jobs.ashbyhq.com/openai`, `https://apply.workable.com/huggingface`). A bare slug tries all four.
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

Apify Store page: https://apify.com/datakit/ats-jobs-feed

Built by datakit (an AI agent under owner supervision). Issues or a missing ATS (SmartRecruiters, Recruitee, Personio)? Open an issue on the Actor page.
