# Bug Reporting & Diagnostics

Game Export Pipeline has a built-in, **user-initiated** bug reporter. It's **offline and has no telemetry** — nothing is sent automatically. When something goes wrong, the add-on captures a diagnostic report that you review and send yourself.

Find it at the bottom of the main **GameExport** panel, under **Report a problem**.

---

## What it does

- **Catches add-on errors** instead of crashing Blender. If an operation (Export, Scan Folder, Fix Safe, Validation, FBX export, …) hits a Python error, it's captured: the panel shows **"An error was captured"**, the operation reports a clear message, and the traceback + context are written to a local log (`gep_error_log.txt` in your temp folder, size-capped).
- **Builds a diagnostic report** with: add-on & Blender versions, OS, Python, the active profile, scene stats (object / mesh counts — **no file names**), and the traceback. **Local paths are scrubbed** (your home folder becomes `~`, `/Users/<name>` / `/home/<name>` / `C:\Users\<name>` become `<user>`).

---

## Sending a report

| Button | What it does |
|---|---|
| **GitHub Issue** | Copies the report to your clipboard and opens a pre-filled new issue on the project's GitHub tracker — paste the report into the body and submit. |
| **Copy** | Copies the report to the clipboard so you can paste it anywhere (issue, email, Discord). |
| **Save .txt** | Writes the report as a `.txt` file (in your Report folder, or temp) and opens it. |
| **Email** | Copies the report and opens a pre-addressed email — paste it into the body and send. |
| **Crash Log** | Opens Blender's own `*.crash.txt` (see below). |

!!! info "You're always in control"
    The report is copied / shown **before** you send it. Nothing leaves your machine on its own — no analytics, no background uploads.

---

## Hard crashes (segfaults)

If **Blender itself** crashes (a hard segfault, not a Python error), the add-on can't catch it — Python is already gone. In that case the only artefact is **Blender's own crash log**, a `*.crash.txt` in your temp folder. The **Crash Log** button opens the most recent one so you can attach it to your report.

---

## For maintainers

The GitHub issue target is the `ISSUES_URL` constant in `operators/diagnostics.py`. Point it at whichever repo hosts your issue tracker, make sure **Issues** are enabled on that repo, and (optionally) keep a `bug` label so the pre-fill can apply it.
