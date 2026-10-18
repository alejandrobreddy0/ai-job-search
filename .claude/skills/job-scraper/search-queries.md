# Search Queries for Job Scraper


## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. Shipped country-agnostic CLIs include `linkedin-search` and `freehire-search`; Danish demos and any skill you add with `/add-portal` are included the same way. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** write every query category in every language listed in your CLAUDE.md Languages table (typically 1-2, sometimes more). A posting requiring a language you have *not* declared, as a job condition, is excluded before scoring; a posting requiring a *higher level* than you declared in a language you *do* work in is flagged for your own judgment, not excluded — see `04-job-evaluation.md`'s Language Gate, the single source of truth for this rule. Translate each category's keywords rather than machine-translating word-for-word (e.g. "Frontend Developer" -> "Desarrollador Frontend", not a literal word-for-word translation) if you work in more than one language.

## Search Sites

Primary:
- **linkedin.com/jobs** - LinkedIn job listings (filter: London, United Kingdom; include Remote UK); also covered by `linkedin-search` CLI
- **uk.indeed.com** - largest general UK board
- **otta.com / welcometothejungle.com** - tech/startup-focused, lists base salaries
- **cwjobs.co.uk / totaljobs.com** - UK boards with salary filters

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies (Greenhouse, Lever, Ashby and Workable boards: `site:boards.greenhouse.io`, `site:jobs.lever.co`, `site:jobs.ashbyhq.com`)

## Query Categories

Combine each query with London / "Remote UK". Salary floor: where a portal supports a salary filter, set **£30,000 minimum** (base). Exclude titles containing "Account Executive", "Closer", "Field Sales" where the portal supports negative keywords.

### Priority 1: Outbound / Inbound Prospecting in AI & FinTech

Highest Priority Score potential (10/10 band).

```
site:linkedin.com/jobs "Sales Development Representative" (AI OR "artificial intelligence") London
site:linkedin.com/jobs "Business Development Representative" FinTech London
site:boards.greenhouse.io ("SDR" OR "BDR") (AI OR FinTech) London
site:jobs.ashbyhq.com ("Sales Development" OR "Business Development Representative") London
site:jobs.lever.co ("SDR" OR "BDR") London FinTech
```

### Priority 2: SDR/BDR in HR Tech, MarTech & Engineering Software

8/10 band - look for structured SDR training academies.

```
site:linkedin.com/jobs "SDR" ("HR Tech" OR "People Tech" OR HRIS) London
site:linkedin.com/jobs "Business Development Representative" (MarTech OR "marketing technology") London
site:linkedin.com/jobs "Sales Development Representative" ("engineering software" OR CAD OR "technical software") London
site:uk.indeed.com "SDR academy" OR "graduate SDR" London SaaS
```

### Priority 3: Inside Sales & Title Variants

Same function, different titles.

```
site:linkedin.com/jobs "Inside Sales Representative" SaaS London
site:linkedin.com/jobs ("Inbound SDR" OR "Outbound BDR") London
site:uk.indeed.com ("Sales Development" OR "Lead Generation Representative" OR "Business Development Associate") SaaS London
```

### Priority 4: Remote UK SDR/BDR

Wider net for fully remote roles.

```
site:linkedin.com/jobs ("SDR" OR "BDR") SaaS "Remote" "United Kingdom"
site:otta.com "Sales Development Representative" Remote UK
```

## Location Filter

Acceptable:
- London (onsite or hybrid) - any zone reachable by public transport
- 100% remote, UK-based
- Outside London requiring regular office attendance: FAIL
- Field sales requiring a personal vehicle: FAIL

## Salary & Role Filter

Apply `04-job-evaluation.md`'s Target Filter Gate before quick-fit: discard base under £30,000, commission-only, AE/closing roles, door-to-door/retail and field sales. Base salary means base - never OTE. Postings with no stated base stay in, flagged "base not stated".

## Language Filter

Your working languages and levels are in CLAUDE.md's Languages table. When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language you haven't declared at all is excluded; a posting requiring a higher level than you declared in a language you do work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language you don't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
