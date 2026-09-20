# Canonical value-engine context checkpoint

2026-09-20. Worktree `dhq-shared-readiness-engine-context`, branch `codex/engine-context-20260920`, baseline `f72c8e4` (the reviewed actual-shared union). This is an isolated canonical-source batch. No shared pin, release workflow, read-only source snapshot, server data or deployment was changed. Root's subsequent session union is in a separate file and can integrate normally.

## Reproduced issue and bounded resolution

**HIGH — cold LeagueIntel work crossed league/account boundaries.** Running the actual baseline module established three failures: an IndexedDB read requested in league 111 adopted league 222 after selection changed; an explicit background state for 222 was ignored and active 111 was built; a held 111 build completed after account replacement, published its values globally, and saved them labeled as league 222. The prior loader read mutable `S` at different times, providers read active players/credentials, and cache publication had no captured ownership boundary. [Baseline executable evidence](evidence/engine-context/baseline.log).

`loadLeagueIntel()` remains callable without arguments for existing public/native/Scout consumers. It now owns a cloned, validated league/season snapshot, captures account/provider-session bytes and the observed storage generation, and checks that scope before and after asynchronous boundaries. A superseded or timed-out operation rejects rather than pretending completion. Default request waits are bounded to 20 seconds; late responses cannot restart publication or follow-up requests. Foreground readers invalidate stale published values, and existing reset/annotation patterns retain their intended boundaries.

The additive background contract is `App.loadLeagueIntel({ state, isCurrent?, timeoutMs?, onProgress? })`, returning `{ data, brain, leagueId, season }`. Explicit state defaults to **no global publication**. Setting `publish: true` is permitted only for the actual active state object. `isCurrent` lets a caller cancel a replaced view in addition to automatic identity checks. A fresh caller can reuse confirmed account-owned cache data independently of the earlier caller's mounted lifetime. The optional progress callback receives owned values before the ledger finishes; consumers must still preserve their own component publication boundary.

Only `dhq-engine.js`, `dhq-providers.js`, and `points-ledger.js` contain production changes. Provider binding captures players, MFL key/token/crosswalk and checks internal request chains. Conflicting MFL league/connection/year is rejected before a private request. Unbound provider calls retain their existing interface. The ledger accepts an optional context key/current predicate; canceled in-flight producers cannot poison or erase a newer retry's cache, while settled matching results survive an old caller's cleanup. Cache hits also establish the current one-brain result instead of leaving another document's missing/stale bridge.

Value/history cache keys now include a known modern account owner, provider, league and season. Values additionally require an exact relevant input signature (scoring, rosters, rights, player attributes, depth charts and tuning). Tokens/keys/cookies are excluded from persisted metadata. Unknown-provenance legacy caches are not adopted or explicitly deleted by this loader; legacy/guest builds remain usable in memory without creating an unowned private cache. Existing general storage janitor behavior is unchanged. Storage denial cannot turn successful in-memory values into a failed save claim or an unhandled rejection; these caches are optional, rebuildable data.

## Preserved behavior and compatibility

- The current owner's original market-blended valuation engine remains the default; no V2 switch, price formula, injury rule, scoring rule, one-brain formula, rank rule, provider business policy or pick-price formula changed.
- Actual full serialized engine results match the baseline under ordinary, superflex and custom-IDP scoring. The injured fixture player retains its established nonzero value. Pick-value function outputs also match for 27 year/round combinations.
- Actual `points-ledger.js` and `one-brain.js` output matches the baseline for healthy input, including the existing record-first rank. Explicit background output does not alter `S`, `App.LI`, `window.DhqBrain` or foreground events.
- Public and native `js/league-detail.js` call the no-argument loader inside `try/catch`; Scout `js/app.js` catches its promise; native's existing Empire bootstrap catches it. The public Empire replacement intentionally waits for this new contract rather than temporarily modifying the active bridge. Actual existing `App.LI = App.LI || {}` annotations and explicit reset patterns have regressions.
- This boundary check is not JWT authentication. It captures an existing app session and rejects malformed modern session presence; backend/proxy authorization remains authoritative.

## Verification

All tests use isolated, synthetic data. No connected account, live provider mutation, real purchase or public release was performed.

| Evidence | Result |
| --- | --- |
| `node tests/engine-context.cjs` | Three original failures reproduced against actual `f72c8e4` source |
| `node tests/engine-context-recovery.cjs` | 20 actual-module groups pass: exact outputs, cache identity, missing storage, malformed modern session, reentrant consumer annotation/reset compatibility, selection/account cancellation, old-finally/new-loader ownership, bounded helpers, actual one-brain/ledger, canceled retry, bound Sleeper player data and private MFL follow-ups |
| `NODE_PATH=<public-consumer>/node_modules node tests/engine-context-browser.cjs reports/public-readiness/evidence/engine-context-browser` | Chrome 390×844: actual canonical engine, providers, ledger, one-brain, core, pick model and storage; background context, foreground build, real IndexedDB reload, real second-tab account change while cold stats are held, and new-account retry pass |
| Existing capital tests | 19 groups pass, including untouched non-pick baseline equality |
| Existing ESPN boundaries and transactions | 18 + 18 groups pass |
| Existing Yahoo context | 14 groups pass |
| Syntax and `git diff --check` | Pass |

[Focused log](evidence/engine-context/recovery.log), [browser log](evidence/engine-context/browser.log), [browser result](evidence/engine-context-browser/result.json), [reload image](evidence/engine-context-browser/ready-reloaded.png), [account-switch image](evidence/engine-context-browser/account-switch-recovery.png). Browser transport permits only local GETs and fixture-fulfilled provider GETs; external writes are blocked. The browser's fixture shell is clearly labeled and is **not** evidence that an actual Empire or LeagueDetail journey has been integrated or released. The initial fixture server omitted UTF-8 and caused a script parse failure; fixing its response encoding exposed the actual source and all recorded final checks passed.

## Remaining acceptance and next executable work

1. Independent source review is required before root integration. Root should merge this additive API into its actual-shared union, then validate the exact published consumer pin through the established process. No deployment is claimed here.
2. Connect the public Empire coordinator to explicit engine output. Its current `assessAllTeams` implementation still reads global player values and the global one-brain bridge; it needs a bounded explicit assessment input adapter, not temporary `S`/`LI` replacement. Full cold-start Empire health/value readiness remains open until that caller integration and browser flow pass.
3. **Existing high correctness gap:** `_dhqBrainPicksByOwner` still generates five rounds across three years, independently of the reviewed format/remaining-right contract. Correct its input evidence while preserving the owner's one-brain formula. Seasonal/keeper assessments require their established context rather than dynasty advice.
4. **Existing data-truthfulness gap:** several engine history/stat/ledger paths still turn provider failures into empty records. Sleeper history also includes non-failed pending transactions; ESPN/Yahoo engine-history adapters remain empty stubs. This batch preserves healthy calculations and isolates context; it does not certify complete evidence during an outage or finish every provider's history. Introduce explicit evidence status before treating those builds as complete advice.
5. MFL's engine history adapter still has independent payload/history mapping alongside `mfl-api.js`; selected-context/credential fencing is fixed here, while full provider payload and partial-history completeness need coordinated validation. The separate security lane owns `mfl-api.js` and its selected payload checks.
6. Legacy unowned offline records, private account restore, whole-product mobile/device/store tests and released assets retain their separate acceptance gates. Do not count this module fixture as public-launch completion.

Independent review of root's public Empire correction `df66d36` was also completed without source edits: all 17 actual evidence groups pass; pending/unknown trades cannot infer completed behavior, malformed weekly rows remain unavailable, both detail pick metrics preserve unknown coverage, and cloud notes use canonical owner identity. No material finding remains in that bounded correction.
