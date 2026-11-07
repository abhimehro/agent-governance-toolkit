---
title: "Dependency audit: November 7 security remediations"
last_reviewed: 2026-11-07
owner: agt-maintainers
---

# Dependency audit: November 7 security remediations

**Date:** 2026-11-07
**Lockfiles changed:** hypervisor `uv.lock`, policy-engine `Cargo.lock`, and the npm lockfiles listed below. Python requirement pins for `pypdf` and `aiohttp` also moved.

Live Dependabot state on this fork was 94 open alerts. Alerts 310 (`certifi` in the marketplace lock), 314, 316, and 317 (`zod`), and 318 and 319 (`cedar-policy`) are already fixed and are not repeated here.

## Which dependencies changed and why

Each row is the version this change resolves. Transitive packages moved only when the patched release required them or when an existing manifest range already required a newer version than the lock contained.

| Package | Manifest | From | To | Why |
|---|---|---|---|---|
| `certifi` | `agent-governance-python/agent-hypervisor/uv.lock` | 2026.5.20 | 2026.10.18 | Critical alert 311 |
| `dnspython` | same uv lock | 2.8.0 | 2.9.0 | High alert 309 |
| `cryptography` | same uv lock | 48.0.1 | 49.0.0 | Alerts 144 and 146. 50.0.0 does not install; see unfixable |
| `pypdf` | caas `requirements.txt` and `pyproject.toml` | 6.19.0 / `>=6.10.2` | 6.21.4 / `>=6.21.4,<7.0` | Alerts 249-256 and 329-338 |
| `aiohttp` | cloud-board `requirements.txt`; agent-os dev extra | 3.14.3 / `>=3.14.3` | 3.14.4 / `>=3.14.4,<4.0` | Alerts 187-189 and 214-218 |
| `follow-redirects` | copilot `package-lock.json` | 1.16.0 | 1.16.2 | Alerts 196, 197, and 320 |
| `body-parser` | mcp-server, policy-engine node, mastra locks | 2.3.0 or 2.2.2 | 2.4.0 | Alert 277, and the same advisory range in the other two locks |
| `qs` | api override; mcp-server lock | 6.15.2 / 6.16.0 | 6.16.1 / 6.17.1 | Alerts 79, 95, 322, and 326 |
| `@hono/node-server` | policy-engine node, mastra, mcp-server, mcp-proxy | 1.19.17 or 2.1.1 | 2.1.6 | Alerts 321, 327, 339, and 344. Advisory floor is 2.1.3 |
| `hono` | policy-engine node, mcp-proxy | 4.13.7 | 4.13.17 | Alerts 190 and 195. Floor is 4.13.11 |
| `fast-uri` | policy-engine node lock | 3.1.7 | 3.1.9 | Alerts 270, 280, 282, and 284 |
| `vite` | mcp-server, mcp-proxy, copilot-governance | 8.0.16 | 8.3.6 | Vite alerts whose floor is 8.1.6 |
| `vitest` and `@vitest/mocker` | those three packages | 4.1.11 | 4.1.12 | Mocker alerts 241, 242, 246, 247, 261, and 262 |
| `@vitest/coverage-v8` | mcp-server | 4.1.10 | 4.1.12 | Keep the coverage package on the same vitest release |
| `@modelcontextprotocol/sdk` | mcp-server and mcp-proxy | 1.31.0 | 1.32.0 | Alerts 200, 201, 203, 206, 207, 208, 210, and 213 |
| `ip-address` | mcp-server, mcp-proxy, policy-engine node, mastra | 10.7.2 or 10.7.3 | 10.7.6 | Alerts 243 and 248. Floor is 10.7.4 |
| `js-yaml` | mcp-proxy | 4.3.0 | 4.3.2 | Alerts 13 and 135 |
| `@babel/core` | typescript lock | 7.29.0 | 7.29.13 | Alert 42. Floor is 7.29.6 |
| `rustls` | `policy-engine/Cargo.lock` | 0.23.40 | 0.23.46 | Alerts 181, 302, 305, and 308 |

`cargo update -p rustls --precise 0.23.46` also moved `aws-lc-rs` 1.17.0 to 1.19.0, `aws-lc-sys` 0.41.0 to 0.47.0, and `rustls-webpki` 0.103.13 to 0.103.16, and added `pkg-config` 0.3.34. Those crates are the lock update required by rustls 0.23.46. `agent-governance-rust` was already on rustls 0.23.46 and was not changed.

Refreshing the hypervisor lock also brought packages that were already below the floors in `agent-hypervisor/pyproject.toml` up to those floors: `fastapi` 0.151.0 to 0.162.2, `pydantic` 2.13.5 to 2.14.3, `pydantic-core` 2.46.5 to 2.50.4, `hypothesis` 6.168.5 to 6.170.15, `pytest-asyncio` 1.4.0 to 1.4.1.post1, `ruff` 0.17.1 to 0.17.4, `typing-extensions` 4.15.0 to 4.16.0, and `typing-inspection` 0.4.2 to 0.4.5. No new requirement ranges were added.

The vite 8.3.6 resolution replaces rolldown 1.0.3 with 1.2.16 and lightningcss 1.32.0 with 1.33.0, including their platform packages. Those are vite's own dependencies. body-parser 2.4.0 brings `content-type` 2.1.0 and `iconv-lite` 0.7.3. The mastra lock also materializes existing non-optional peers of `hono-openapi` 1.3.0 (`@standard-community/standard-json`, `@standard-community/standard-openapi`, `openapi-types`, `@types/json-schema`, and `quansync`) that the previous lock had not recorded.

## Security advisory relevance

This change is intended to clear 86 of the 94 open alerts. The advisory ids below are the GHSA identifiers reported by Dependabot. No advisory is ignored or suppressed.

| Alerts | GHSA | Resolution |
|---|---|---|
| 311 critical | `GHSA-6q48-6c7w-9gw2` | `certifi` 2026.10.18 |
| 309 high | `GHSA-45c3-73f7-pv4m` | `dnspython` 2.9.0 |
| 146 high | `GHSA-jwv3-5hgf-82ww` | `cryptography` 49.0.0 |
| 144 medium | `GHSA-m2h6-j472-rp4c` | `cryptography` 49.0.0 |
| 249-256 and 329-338 | pypdf GHSAs `GHSA-7mh2-4gg2-979f`, `GHSA-39mj-jf6j-2qj3`, `GHSA-9mx8-4j7g-633p`, `GHSA-qfv9-64jg-mh7p`, `GHSA-5hx5-vpf9-5wcv`, `GHSA-cgf9-327m-v77c`, `GHSA-f54x-cmq8-qg57`, `GHSA-6vvc-x485-93v7`, `GHSA-gmh7-3g9r-mjh5`, `GHSA-jr96-gvgh-j3r4`, `GHSA-6c7f-9265-g8fw`, `GHSA-x3rh-gjgm-fmqc`, `GHSA-62m5-6wh8-28x7`, `GHSA-4vqv-7265-gm2w`, `GHSA-42gh-23hr-vphw`, `GHSA-mfr5-77xh-9j7h`, `GHSA-cggf-277g-3q54`, `GHSA-5849-834g-hvw5` | `pypdf` 6.21.4 |
| 187, 188, 189, 214, 215, 216, 217, 218 | `GHSA-2g87-pp6c-29x5`, `GHSA-494j-54xm-rwp3`, `GHSA-5wr8-gcqf-gqf3`, `GHSA-9xw5-w5jj-pvcg`, `GHSA-pqxq-w44r-73xw`, `GHSA-6cjc-53gc-5rqm`, `GHSA-gx76-c99x-2c96`, `GHSA-f9xw-jh8m-3mwq` | `aiohttp` 3.14.4 |
| 196, 197, 320 | `GHSA-8r9p-f939-6h3c`, `GHSA-q3jh-wrc6-jgq8`, `GHSA-mrwp-f3q8-3g9p` | `follow-redirects` 1.16.2 |
| 277 | `GHSA-27r3-jxmh-p828` | `body-parser` 2.4.0 |
| 79, 95, 322, 326 | `GHSA-x5fp-wj9c-mxmx`, `GHSA-4mjr-xmp4-gh2g`, `GHSA-6pq8-jhm2-2vcw` | `qs` 6.16.1 or 6.17.1 |
| 321, 327, 339, 344 | `GHSA-rmxm-3fg6-px4f` | `@hono/node-server` 2.1.6 |
| 190, 195 | `GHSA-5r4p-p66f-jhc7` | `hono` 4.13.17 |
| 270, 280, 282, 284 | `GHSA-wjr9-j795-jxcr`, `GHSA-333x-w4r6-6crp`, `GHSA-w74x-v6f9-j3xg`, `GHSA-xhq7-xmqj-769h` | `fast-uri` 3.1.9 |
| 202, 204, 205, 209, 211, 212, 219, 220, 221, 239, 240, 244, 245, 259, 260 | `GHSA-698v-4r3x-3vcv`, `GHSA-587h-7wmj-h5pm`, `GHSA-r825-w8mm-5pxw`, `GHSA-9jrq-w75r-8gcw`, `GHSA-rq7h-c2jc-7f22` | `vite` 8.3.6 |
| 241, 242, 246, 247, 261, 262 | `GHSA-xp26-3454-mjgp`, `GHSA-qxg6-89mg-rqv8` | `@vitest/mocker` 4.1.12 |
| 200, 201, 203, 206, 207, 208, 210, 213 | `GHSA-6prh-2h8m-c8cw`, `GHSA-22jm-h49p-29qw`, `GHSA-rvq5-wwqv-78pq`, `GHSA-46jr-4wx8-v453` | `@modelcontextprotocol/sdk` 1.32.0 |
| 243, 248 | `GHSA-rxp7-6hxx-x8q5` | `ip-address` 10.7.6 |
| 13, 135 | `GHSA-5p4m-2wfm-xmqj`, `GHSA-2883-xcg3-v3hh` | `js-yaml` 4.3.2 |
| 42 | `GHSA-4x5r-pxfx-6jf8` | `@babel/core` 7.29.13 |
| 181, 302, 305, 308 | `GHSA-2mjx-qc3c-rqvc`, `GHSA-c8m5-c952-6cm4`, `GHSA-w44r-93j7-qv28`, `GHSA-h2v8-394m-p7vv` | `rustls` 0.23.46 |

Alert 181 is patched in rustls 0.23.45. Alerts 302, 305, and 308 are patched in 0.23.46. The lock uses 0.23.46.

### Still open, not suppressed

| Alerts | GHSA | Why it stays open |
|---|---|---|
| 9 high | `GHSA-g6cj-pr64-35w5` | First patched `cryptography` is 50.0.0. Published `agent-governance-toolkit-core` 5.0.0 requires `cryptography>=46.0.7,<50.0`. The lock stops at 49.0.0. No override was added past that published cap. |
| 343, 328, 325 medium | `GHSA-g3hv-75wg-m249` | `minimatch` is patched in 11.0.1. eslint 10.14.0, typescript-eslint, and glob declare `^10.2.x`, `^9.0.x`, or `^3.1.x`. 11 is outside those ranges. The 3.x copies are below the vulnerable range. No override was added. |
| 341, 323 low | `GHSA-gcqx-w48m-w2h4` | `deepmerge` 4.3.1 is the current release and Dependabot reports no patched version. |
| 35, 32 low | `GHSA-g7r4-m6w7-qqqr` | `esbuild` is patched in 0.28.1. `tsup` 8.5.1 depends on `esbuild ^0.27.0`, and no tsup 8 release accepts 0.28.1. tsup 9 is a major bump. No override was added. |

`npm audit` also reports `zod` below 4.6.6 in the policy-engine node lock (4.4.3) and the mastra lock (3.25.76, plus nested 4.4.3), and reports `@ai-sdk/provider-utils` and `@mastra/core` findings in mastra. Those are not in the open Dependabot set. The prior zod alerts are already closed, and moving mastra from zod 3 to 4.6.6 is not a compatible patch. They were left unchanged. `glob`, `rimraf`, and `jest` findings are the same minimatch advisory through parent packages.

`agent-governance-python/agent-os/modules/caas/requirements.lock` still records `pypdf==6.7.5`, and the cloud-board `requirements.lock` still records `aiohttp==3.13.3`. Both files were already behind their `requirements.txt` pins before this change and are not the Dependabot manifests. A full relock would move unrelated packages, so those hash locks were not rewritten.

## Breaking change risk assessment

**Risk: low to medium, and limited to versions already allowed by the parent ranges.**

- `certifi` 2026.10.18 removes compromised e-Szigno and emSign root certificates. TLS connections that chain only to those roots will fail. That is the security fix. The hypervisor requires Python `>=3.11`.
- `dnspython` 2.9.0 requires Python `>=3.11`, which matches the hypervisor. It is pulled by `email-validator` 2.3.0, whose range accepts 2.9.0. The change bounds DNS-name parsing cost.
- `cryptography` 49.0.0 stays inside the published core cap. It drops the macOS x86_64 wheel, removes deprecated aliases and the standalone `ChaCha20` API, and tightens some X.509 NULL-parameter parsing. Alert 9 is not fixed by this version.
- `pypdf` 6.21.4 stays on 6.x. The caas module uses `PdfReader` for text extraction. The pyproject floor moved from 6.10.2 to 6.21.4 so a fresh install cannot select a vulnerable 6.x release. The upper bound stays `<7.0`.
- `aiohttp` 3.14.4 is a patch inside the existing `<4.0` cap. The cloud-board service uses `ClientSession`.
- `follow-redirects` 1.16.2 is inside axios `^1.16.0`. It stops credential headers from following a cross-origin redirect.
- `body-parser` 2.4.0 is inside the express ranges already declared (`^2.3.0` or `^2.2.1`). `qs` 6.17.1 is inside `^6.16.0` / `^6.16.1`. The api service override moved from the vulnerable exact pin 6.15.2 to 6.16.1, which its express 4.22 range accepts.
- `@hono/node-server` 2.1.6 is inside the SDK range `^1.19.9 || ^2.0.5`. 2.1.3 is the advisory floor; 2.1.6 is the current 2.1 release and is not 3.x. Its package metadata requires Node `>=20`. Several package `engines` fields still say `>=18`. CI for these packages uses Node 20 and 22. The September 2026 hono audit left the engines field unchanged because the stdio server imports do not load `@hono/node-server`. That is still true for the current mcp-server imports (`server/index.js`, `server/stdio.js`, and `types.js`). Node 18 installs can warn `EBADENGINE`. Node 20 and 22 match the dependency.
- `@modelcontextprotocol/sdk` 1.32.0 is the advisory floor and a minor bump from 1.31.0. mcp-server imports `Server`, `StdioServerTransport`, and the stable request schemas. mcp-proxy declares the SDK but its `src` tree does not import it. The SDK still accepts `@hono/node-server` `^2.0.5`.
- `vite` 8.3.6 is inside vitest's existing `^6 || ^7 || ^8` range. 8.1.6 would also clear the alerts; 8.3.6 is the current 8.x release selected by `npm update` and is the same minor line already used elsewhere in this repo (mastra resolves 8.3.5). The rolldown and lightningcss updates belong to that vite release. Dev-only.
- `vitest` 4.1.12 is a patch. `@vitest/mocker` is published with the same version, so the direct vitest pin is what moves the mocker. This does not take vitest 5.
- `ip-address` 10.7.6 is inside `express-rate-limit` `^10.2.0`. `hono` 4.13.17 is inside `^4.11.4`. `fast-uri` 3.1.9 is inside ajv `^3.0.1`. `js-yaml` 4.3.2 is inside eslint `^4.1.0`. `@babel/core` 7.29.13 is inside the existing `^7.27.4` / `^7.23.9` ranges. The other `@babel/*` packages and the browserslist data files moved with that core update.
- `rustls` 0.23.46 is the same 0.23 line. It is transitive; no `Cargo.toml` dependency was added. The aws-lc crates moved because that rustls release requires them.

## Rollback

Revert this commit. The previous lock entries and pins are the rollback point. Do not replace the unfixable rows with an override or an `npm audit fix --force` result.
