---
name: record-terminal
description: "Record terminal sessions as asciicast or text logs, capturing commands, output, and timing for playback and sharing."
user-invocable: true
args: "[--output <path>] [--format <asciicast|script|gif>] [--commands <description>] [--title <title>]"
---

# Record Terminal — Capture Terminal Sessions

You are orchestrating a terminal recording session. You will capture commands, their output, and timing data so the session can be replayed, shared, or converted to GIF.

## Parse Arguments

Parse arguments from `$ARGUMENTS`:
- `--output <path>`: output file path (default: `terminal-recording` in the current working directory, extension added based on format)
- `--format <format>`: recording format (default: `asciicast`)
  - `asciicast` — asciinema v2 format, replayable in terminal or web (`.cast` extension)
  - `script` — BSD `script` capture with timing, works everywhere (`.log` + `.timing` files)
  - `gif` — records as asciicast then converts to animated GIF (`.gif` extension, requires `agg`)
- `--commands <description>`: natural language description of commands to execute during recording
- `--title <title>`: title embedded in the recording metadata

If no arguments are provided, start an interactive recording and wait for the user to tell you what commands to run or say "stop".

## Phase 1: Setup

1. **Detect available tools.** Check which recording tools are installed:
   ```bash
   command -v asciinema && asciinema --version
   command -v script && echo "script available"
   command -v agg && echo "agg available"
   ```

2. **Select recorder based on format and availability:**
   - `asciicast` or `gif` format: requires `asciinema`. If missing, offer to install:
     - macOS: `brew install asciinema`
     - Linux: `pip install asciinema` or `apt install asciinema`
   - `script` format: uses BSD/GNU `script` (pre-installed on macOS and Linux)
   - `gif` format also requires `agg` for conversion. If missing, offer to install:
     - macOS: `brew install agg`
     - Rust/cargo: `cargo install agg`

3. **Prepare output paths.** Based on format:
   - `asciicast`: `<output>.cast`
   - `script`: `<output>.log` (transcript) + `<output>.timing` (timing data)
   - `gif`: `<output>.cast` (intermediate) + `<output>.gif` (final)

4. **Set recording environment.** Configure a clean shell environment for the recording:
   ```bash
   export ASCIINEMA_REC=1
   ```

## Phase 2: Record

### Option A: Asciinema Recording (asciicast / gif)

1. **Start recording with commands.** If `--commands` was provided, execute them inside a recorded session:
   ```bash
   asciinema rec <output>.cast --title "<title>" --command "bash -c '<commands>'" --overwrite
   ```

   For multiple commands, write a temporary script:
   ```bash
   cat > /tmp/terminal-recording-script.sh << 'SCRIPT'
   #!/bin/bash
   set -e
   # Commands will be written here
   <command1>
   sleep 0.5
   <command2>
   sleep 0.5
   <command3>
   SCRIPT
   chmod +x /tmp/terminal-recording-script.sh
   asciinema rec <output>.cast --title "<title>" --command "/tmp/terminal-recording-script.sh" --overwrite
   ```

   Add `sleep 0.5` between commands so each step is visible during playback.

2. **Start interactive recording.** If no commands were specified, start recording and execute commands via the Bash tool one at a time. Each Bash command you run will be captured:
   ```bash
   asciinema rec <output>.cast --title "<title>" --stdin --overwrite
   ```
   Then tell the user the recording is active and ask what commands to run.

3. **Execute requested commands.** Run each command the user requests using the Bash tool. The recording captures everything. Add brief pauses between commands:
   ```bash
   sleep 0.5
   ```

### Option B: Script Recording (script format)

1. **Start recording on macOS:**
   ```bash
   script -t 2><output>.timing <output>.log
   ```

   **Start recording on Linux:**
   ```bash
   script --timing=<output>.timing <output>.log
   ```

2. **Execute commands** within the script session using the Bash tool.

3. **Tell the user** the recording is active and wait for instructions or execute `--commands`.

## Phase 3: Finalize

1. **Stop the recording.**
   - Asciinema: the recording stops automatically when the command/script exits. If interactive, run:
     ```bash
     exit
     ```
   - Script: run `exit` to end the script session.

2. **Verify the output:**
   ```bash
   ls -lh <output-files>
   ```

3. **If gif format, convert the asciicast to GIF:**
   ```bash
   agg <output>.cast <output>.gif --theme monokai --font-size 14
   ```
   Then verify:
   ```bash
   ls -lh <output>.gif
   ```

4. **Clean up temporary files:**
   ```bash
   rm -f /tmp/terminal-recording-script.sh
   ```
   For gif format, ask the user if they want to keep the intermediate `.cast` file.

5. **Report to the user:**
   - Where the recording was saved
   - File size
   - Recording duration (for asciicast, parse from the file)
   - How to play it back:
     - `.cast`: `asciinema play <path>` or upload with `asciinema upload <path>`
     - `.log`: `cat <path>` to view, or `scriptreplay <timing-file> <log-file>` for timed playback
     - `.gif`: `open <path>` (macOS) or share directly — GIFs play everywhere
   - For asciicast, mention they can share on asciinema.org: `asciinema upload <path>`

## Phase 4: Playback (Optional)

If the user asks to play back a recording:

- **Asciicast:** `asciinema play <path> --speed 2` (2x speed is a good default)
- **Script:** `scriptreplay <timing-file> <log-file>`
- **GIF:** `open <path>` (macOS) or `xdg-open <path>` (Linux)

## Important Rules

- Always verify recording tools are installed before starting. Never assume availability.
- Add 0.5s pauses between commands so playback is readable.
- If a command fails during recording, continue capturing — the error is part of the session.
- Never leave a recording running without telling the user. Always confirm recording state.
- If the user says "stop" at any point, immediately finalize the recording.
- For `script` format, detect macOS vs Linux — the `script` command has different flags on each platform.
- Clean up temporary scripts after recording.
- Default to `asciicast` format — it produces the best playback experience and is widely supported.
- If `--commands` includes destructive operations, warn the user before executing.
