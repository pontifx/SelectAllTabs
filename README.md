# SALLT-1.2.1

`SALLT` is a Burp Suite extension that sends HTTP messages from Burp tables and Site map into Repeater, with grouping, deduplication, vulnerability-aware tab naming, startup history processing, JSON import/export support, and developer retest exports.

## Features
- Send selected requests from Proxy, Target, Logger, and similar Burp tables to Repeater.
- Name tabs by hostname or a custom label.
- Optionally append vulnerability reason and source to tab names.
- Group tabs by hostname or custom group name, with optional `[Group:...]` prefixes.
- Process historical Proxy history and Site map entries on startup.
- Import the current Site map into Repeater on demand.
- Optionally require Burp scan findings for Site map/history imports so scanner-backed entries are isolated before dispatch.
- Repeated imports now allocate fresh Repeater tab captions so the same Site map batch can be imported again after earlier tabs were closed.
- Auto-poll newly discovered Site map items into Repeater for a configurable time window.
- Deduplicate with `Exact match`, `Normalized URL`, or `Drift-based` strategies.
- Highlight Proxy history items from Burp scan findings, including built-in Scanner issues, BChecks, and extension-generated issues.
- Export selected messages and tracked Repeater history to JSON.
- Export selected messages as a developer retest package from the context menu:
  - local web page harness bundle
  - PowerShell retest script
  - curl retest bundle
- Import JSON datasets back into Repeater.
- Run built-in import/export validation against synthetic, large, and corrupted datasets.

## Build
Build the jar with:

```powershell
.\gradlew.bat jar
```

The output artifact is:

`build/libs/SALLT-1.2.1.jar`

## Usage
1. Load `SALLT-1.2.1.jar` in Burp as a Java extension.
2. Select messages in a Burp HTTP table, right-click, and use `SALLT -> Send selected to Repeater (SALLT)`.
3. Configure naming, grouping, highlight filtering, vulnerability tagging, deduplication, and drift settings in the `SALLT` extension tab.
4. Use `Import current Site map` in the extension tab or `SALLT -> Import current Site map to Repeater` from a context menu to pull current Site map entries into Repeater with the active deduplication, drift, grouping, and vulnerability settings.
5. Enable `Require scan findings for Site map/history imports` if you only want scanner-backed Site map/history entries sent to Repeater.
6. Enable `Process historical Proxy/Site map data on startup` if you want Burp to preload existing project data on future extension starts.
7. Use `SALLT -> Export Developer Retest` from the context menu to generate:
   - a local browser harness bundle with `open-harness.bat`
   - a standalone `retest.ps1`
   - a curl retest bundle with body assets
8. Use `Import JSON dataset`, `Export tracked Repeater history`, and `Run import/export validation` from the extension tab as needed.

## Deduplication
- `Exact match`: deduplicates identical requests.
- `Normalized URL`: deduplicates by scheme, host, port, method, and normalized path, ignoring query-string permutations.
- `Drift-based`: uses the configured drift mode and threshold, plus URL-structure tokens, to suppress near-duplicates.

## JSON Datasets
- Schema version is now `2`.
- Dataset items preserve request bytes, response bytes, Burp highlight values, and vulnerability metadata when available.
- Imports remain backward-compatible with older dataset versions.
- Burp's classic extender API still cannot preload responses directly into new Repeater tabs, so imported responses remain metadata only.

## Developer Retest Exports
- The web harness bundle includes `index.html`, `open-harness.bat`, `serve.ps1`, `retest.ps1`, `curl-retest.sh`, request body assets, and `retest.json`.
- The browser harness is useful for quick visual retests and browser-only edge cases, but browser replay may still be limited by CORS, CSP, cookie scope, and forbidden browser-managed headers.
- The PowerShell and curl outputs replay the exported requests more directly and save response artifacts to disk.

## Validation
- The extension includes an in-memory validator for import/export round trips.
- Automated tests cover:
  - synthetic datasets with vulnerability metadata
  - large dataset round trips
  - corrupted dataset rejection

Run tests with:

```powershell
.\gradlew.bat test
```
