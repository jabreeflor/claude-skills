---
name: stop-recording
description: "Stop an active browser screencast and save the MP4 video."
user-invocable: true
---

# Stop Recording — Finalize Active Screencast

You are stopping an active browser recording session.

## Steps

1. **Stop the screencast.** Call `mcp__chrome-devtools__screencast_stop`. This tells Chrome to stop capturing frames and ffmpeg to finalize the MP4.

2. **Verify the output.** The stop command returns the file path. Check it exists:
   ```bash
   ls -lh <output-path>
   ```

3. **Re-encode for compatibility.** The Chrome DevTools screencast produces VP9-encoded MP4 with GBR pixel format, which macOS QuickTime and many players cannot open. Always re-encode to H.264:
   ```bash
   ffmpeg -i <output-path> -c:v libx264 -pix_fmt yuv420p -crf 23 -preset fast <output-path-without-ext>_h264.mp4
   ```
   After re-encoding succeeds, replace the original file:
   ```bash
   mv <output-path-without-ext>_h264.mp4 <output-path>
   ```

4. **Report to the user:**
   - Confirm the recording has stopped
   - Where the MP4 was saved
   - File size
   - Suggest playback: `open <path>` (macOS) or `xdg-open <path>` (Linux)

## Important Rules

- If no screencast is active, tell the user clearly — do not error silently.
- If the stop command fails, report the error and suggest the user check if Chrome is still connected.
- Always re-encode the output — the raw screencast VP9/GBR format is not playable in most video players.
