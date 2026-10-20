---
title: "Dependency audit: aiohttp 3.14.3 to 3.14.4 (cloud-board)"
last_reviewed: 2026-10-20
owner: agt-maintainers
---

# Dependency audit: aiohttp 3.14.3 to 3.14.4 (cloud-board)

Dependabot pull request #13. The audit-trail gate requires this document
because `dependabot/fetch-metadata` reported an empty update type, so the
routine patch exemption in `scripts/ci/vendored-patch-audit.sh` did not apply.

## Which dependencies changed and why

Lockfile: `agent-governance-python/agent-os/services/cloud-board/requirements.txt`.

- `aiohttp`: 3.14.3 -> 3.14.4
- Reason: first release that patches Dependabot alerts #187, #188, and #189
  on this manifest. Published 2026-10-05, so the 7-day cooling-off window
  has passed.
- `cryptography`: 50.0.0 -> 50.0.2
- Reason: the same Dependabot commit moves this pin even though its
  metadata lists only aiohttp. 50.0.1 (2026-08-25) and 50.0.2 (2026-09-30)
  rebuild the wheels against newer OpenSSL and do not change the Python
  API. No open Dependabot alert on this manifest requires the move. The
  50.0.0 major-bump rationale stays in
  [2026-08-07-cryptography-50-cloud-board.md](2026-08-07-cryptography-50-cloud-board.md).

## Security advisory relevance

- Alert #187, `GHSA-2g87-pp6c-29x5` (high): excessive or unbounded memory
  and CPU use on attacker-controlled requests. Patched in 3.14.4.
- Alert #188, `GHSA-494j-54xm-rwp3` / `CVE-2026-111063` (low): a shared
  cookie marked Secure could be sent on an unencrypted request. Patched
  in 3.14.4.
- Alert #189, `GHSA-5wr8-gcqf-gqf3` / `CVE-2026-111064` (high): the server
  did not apply a timeout before the first request bytes, so an idle
  connection could be held open. Patched in 3.14.4. Production deployments
  should still put a reverse proxy in front of any aiohttp server.
- `cryptography` 50.0.2: no advisory is addressed. The wheel update tracks
  the OpenSSL build used by the published wheels.

cloud-board uses aiohttp as a client in
`workers/reputation_sync.py` (`ClientSession.post` with a 5 second total
timeout). It does not run an aiohttp server. Alert #189 is therefore not
on the request path this service exercises, but the manifest pin was still
inside the vulnerable range, so the bump closes the alert.

## Breaking change risk assessment

Risk is low for this service.

- aiohttp 3.14.4 is a patch release. The client call site posts JSON and
  does not configure a cookie jar, so the Secure-cookie fix does not change
  its behavior. Server-side form field limits in 3.14.4 do not apply to
  this client. The agent-os test jobs on this pull request passed on Python
  3.11, 3.12, and 3.13 with these pins.
- cryptography 50.0.2 has no listed API change relative to 50.0.0. The
  prior cloud-board audit still describes the 50.0.0 behavior that this
  service depends on.

Rollback is reverting both pins in the cloud-board requirements file.
That would reopen alerts #187, #188, and #189.
