# Contributing to Airlock

Thanks for your interest. Airlock is in early development — right now, the most valuable contribution isn't code, it's **feedback**.

## Right now, what helps most

**Tell us your use case.** Open a [GitHub Discussion](https://github.com/bdhobson/airlock/discussions) and describe:

- What you're building (what kind of agent, what tools it uses)
- What security concern brought you here
- What v1 must have for you to actually use it

This shapes the roadmap more than anything else.

**Share attack patterns you've encountered.** If you've seen real prompt injection attempts in the wild (in tool responses, scraped content, user input) describe them. These become detection rules.

**Report gaps in the README.** If something is unclear or the problem statement doesn't resonate, open an issue.

## When code does land

Once the proxy MVP is up:

- **Issues first** — open an issue before a PR for anything significant
- **Small, focused PRs** — one thing at a time
- **Tests for detection rules** — every new rule needs a test case with a realistic injection sample
- **No new dependencies without discussion** — keeping the stack lean is a priority

---

Questions? Open a Discussion or reach out to [@bdhobson](https://github.com/bdhobson).
