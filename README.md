# Select All Tabs to Repeater (Burp Extension)

This extension adds a context menu item that sends *all selected* requests from any Burp tool tab to Repeater. You can configure tab naming and grouping behavior in the extension tab.

## Features
- Send selected requests from Proxy, Target, Logger, etc. to Repeater.
- Auto-poll newly discovered Burp Site map entries into Repeater for a configurable time window.
- Export selected messages, including request/response bytes, to a JSON dataset.
- Import a JSON dataset back into Repeater as new tabs.
- Export tracked Repeater history seen by this extension to JSON.
- Highlight selected messages in Burp by case-insensitive keyword terms from the request, response, or both.
- Highlight Proxy history entries that match Burp Scanner findings, including built-in checks and BChecks.
- Tab naming options:
  - Hostname
  - Custom name
- Grouping options:
  - None
  - Hostname
  - Custom name (named group)
- Optional group tag prefix in tab names:
  - Group name
- Optional scope filter:
  - `Only include in-scope items` skips out-of-scope selections before sending or polling
- Optional highlight filter:
  - Send only entries with a matching Burp highlight color such as `Red` or `Yellow`
  - `Any Highlighted` matches any non-empty Burp highlight
- Optional Proxy scan-finding highlighter:
  - Toggle automatic Proxy highlighting for Burp scan findings on or off
  - Map finding severities like `Critical`, `High`, `Medium`, `Low`, `Information`, and `False positive` to different Burp colors
  - Matches Proxy items by exact messages when possible, then falls back to request and URL/method/path fingerprints for active-scan and BCheck findings
  - Turning it off clears only highlights that this feature previously applied
  - Includes a manual test that highlights every Proxy entry with a response so you can verify Burp highlighting works at all
- Optional keyword highlighter:
  - Match comma-separated terms against `Requests`, `Responses`, or `Requests + Responses`
  - Import keyword wordlists from `.csv` or `.txt` files into the terms field
  - Require `Any term` or `All terms`
  - Apply a Burp highlight color so those entries can be filtered into Repeater later
- Optional "method + path" suffix for easier identification.
- Optional drift filter:
  - Compare `Requests`, `Responses`, or `Requests + Responses`
  - Only send messages whose drift is at or above your configured percentage threshold

## Build
1. Build the JAR:
   - Windows:
     ```
     .\gradlew.bat jar
     ```
   - macOS/Linux:
     ```
     ./gradlew jar
     ```
2. Load the jar in Burp: **Extensions** -> **Add** -> **Java**.

The output jar will be in `build/libs/select-all-tabs-1.0.0.jar`.

## Usage
1. In Burp, select requests in any tab with an HTTP message table (e.g., Proxy history). Use Ctrl+A to select all.
2. Right-click and choose **Select All Tabs -> Send selected to Repeater (Select All Tabs)**.
3. To export messages, right-click selected messages and choose **Select All Tabs -> Export selected messages to JSON**.
4. To import a dataset, use **Select All Tabs -> Import JSON dataset to Repeater** from the context menu or click **Import JSON dataset** in the extension tab.
5. To export tracked Repeater traffic, use **Select All Tabs -> Export tracked Repeater history to JSON** or click **Export tracked Repeater history** in the extension tab.
6. In the **Select All Tabs** extension tab, set:
   - **Grouping** = **Custom Name**
   - Your **Custom group name**
   - Optional **Only include in-scope items**
   - Optional **Highlight filter**
   - Optional **Proxy scan-finding highlighter** toggle and severity-to-color mapping
   - Optional **Keyword highlighter** terms, scope, match mode, and highlight color
   - Optional **Drift filter mode** and **Minimum drift %**
7. After sending, use the shared `[Group:...]` tag in Repeater tab captions to quickly create/select a single Repeater tab group.
8. For fine-grained imports, select messages in Proxy/Target/Logger, right-click **Select All Tabs -> Highlight selected by keyword rules**, then run the normal send action with **Highlight filter** set to that same color.
9. To automatically flag issues in Proxy history, enable **Highlight Proxy history entries with scan findings** and choose the colors you want for each severity. The extension will backfill existing findings and keep up with new Burp Scanner and BCheck issues.
10. If scan-finding highlights are not appearing, use **Test: highlight all Proxy responses** first. If those highlights show up, the Burp highlight path is working and the remaining problem is issue matching rather than Burp itself.
11. For larger keyword sets, click **Import keyword wordlist (.csv/.txt)** to merge file-based terms into the keyword highlighter field.

## Auto-Poll
1. Configure the same naming/grouping/scope settings you want to use for incoming items.
2. Set **Auto-poll Site map interval (seconds)** and **Auto-poll total duration (seconds)**.
3. Optionally enable a drift mode and threshold to suppress near-duplicate requests/responses within that auto-poll session.
4. Click **Start auto-poll**.

Notes:
- Auto-poll snapshots the current Site map when it starts, then only considers newly discovered entries during that session.
- When drift mode uses responses, messages without a response are skipped for that filter.

## JSON Datasets
- Export is selection-based: choose the Repeater tabs or other HTTP messages you care about, then export only those.
- Imported datasets recreate new Repeater tabs from the saved requests.
- Selection-based dataset exports preserve the source Burp highlight value as metadata.
- Response bytes are preserved in the JSON export, but Burp's extender API does not provide a way to preload those responses back into newly created Repeater tabs.
- Tracked Repeater history export is best-effort: the classic Burp Extender API does not expose a list of all arbitrary Repeater tabs or their captions.
- This extension records Repeater traffic it observes after the extension loads, and preserves tab names when those tabs were created through this extension and the request fingerprint still matches.

## Notes
- Grouping is implemented via tab name prefixes so you can visually group/sort tabs in Repeater.
- Tab grouping and tab-group colors are not exposed through Burp's extender API, so this extension tags tab names consistently to make one-pass grouping fast in Repeater.
