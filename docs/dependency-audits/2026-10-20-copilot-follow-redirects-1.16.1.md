---
title: "Dependency audit: follow-redirects 1.16.0 to 1.16.1 (copilot extension)"
last_reviewed: 2026-10-20
owner: agt-maintainers
---

Lockfile bump for Dependabot alerts #196 and #197, prepared outside
Dependabot. The audit-trail gate applies because this is not a Dependabot
pull request.

## Which dependencies changed and why

Lockfile: `agent-governance-python/agent-os/extensions/copilot/package-lock.json`.

- `follow-redirects`: 1.16.0 -> 1.16.1
- Reason: 1.16.1 is the first release that patches both alerts. It was
  published 2026-10-01, so the 7-day cooling-off window has passed.
- Parent: `axios` 1.20.0 already depends on `follow-redirects@^1.16.0`.
  No `package.json` change and no npm override. `npm update follow-redirects`
  resolved 1.16.1; the committed diff keeps only that package's version,
  tarball URL, and integrity. The integrity matches
  `npm view follow-redirects@1.16.1 dist.integrity`.
- Other `package-lock.json` files in this repository do not resolve
  `follow-redirects`.

Upstream commits between 1.16.0 and 1.16.1:

- `080a76d` Escape all RegExp modifiers.
- `9865ca4` Support key/value header arrays.
- `4630125` Release version 1.16.1 of the npm package.

## Security advisory relevance

- Alert #196, `GHSA-8r9p-f939-6h3c` / `CVE-2026-111755` (high):
  `sensitiveHeaders` names were escaped incompletely. A declared name
  containing `^` or `|` was not stripped on a cross-origin redirect, so a
  custom credential header could be forwarded. Patched in 1.16.1.
  Vulnerable range: `>= 1.16.0, < 1.16.1`.
- Alert #197, `GHSA-q3jh-wrc6-jgq8` / `CVE-2026-111756` (high): headers
  passed as a Node array were not walked by the credential strip, so
  `Authorization`, `Cookie`, `Proxy-Authorization`, and declared sensitive
  headers survived a cross-origin or protocol-downgrade redirect. Patched
  in 1.16.1. Same vulnerable range.

The copilot extension reaches this package only through axios. Its one
client, `CMVKClient`, sends object-form `Content-Type` and `User-Agent`
headers and does not set `Authorization`, `Cookie`, or `sensitiveHeaders`.
Neither defect is on that call path. The lockfile still resolved a version
inside both vulnerable ranges, so the bump is what closes the alerts.

## Breaking change risk assessment

Risk is low.

- 1.16.1 is a patch. The two code commits tighten header stripping. Callers
  that already pass object headers with ordinary names, which is what axios
  and this extension do, keep the same redirect behavior.
- Array-form headers are now stripped instead of forwarded. This extension
  does not pass headers that way.
- Rollback is reverting the `follow-redirects` entry in the copilot
  lockfile. That would reopen alerts #196 and #197.
