---
name: stop-terminal-recording
description: "Stop an active terminal recording session and finalize the output file."
user-invocable: true
---

# Stop Terminal Recording — Finalize Active Session

You are stopping an active terminal recording session.

## Steps

1. **Detect the active recording type.** Check for active recording processes:
   ```bash
   ps aux | grep -E "(asciinema rec|script )" | grep -v grep
   ```
   Also check for the recording state file:
   ```bash
   cat /tmp/.terminal-recording-active 2>/dev/null
   ```

2. **Stop the recording.**
   - **Asciinema:** Send `exit` to the recording shell or signal the process:
     ```bash
     exit
     ```
   - **Script:** Exit the script session:
     ```bash
     exit
     ```

3. **Verify the output.** Check the recording file exists and has content:
   ```bash
   ls -lh <output-files>
   ```

4. **If gif conversion was requested**, convert now:
   ```bash
   agg <output>.cast <output>.gif --theme monokai --font-size 14
   ```

5. **Report to the user:**
   - Confirm the recording has stopped
   - Where files were saved
   - File size and duration
   - Playback instructions:
     - `.cast`: `asciinema play <path>`
     - `.log` + `.timing`: `scriptreplay <timing> <log>`
     - `.gif`: `open <path>`

## Important Rules

- If no recording is active, tell the user clearly — do not error silently.
- Always verify output files exist after stopping.
- If the stop fails, check if the recording process is still running and report the status.
