---
name: agent-reach
description: "Fetch and extract clean, readable content from any public URL for AI agent consumption. Uses Agent-Reach to strip ads, navigation, and clutter, returning LLM-optimized text."
user-invocable: true
args: "<url> [--raw] [--save <path>]"
---

# Agent-Reach — Web Content Extraction for AI Agents

You are using Agent-Reach to fetch and extract clean, readable content from public URLs. This tool strips away ads, navigation, and HTML clutter to return text optimized for LLM context windows.

## Parse Arguments

Parse arguments from `$ARGUMENTS`:
- First positional argument (required): the URL to fetch content from
- `--raw`: return the extracted text directly without summarizing or processing
- `--save <path>`: save the extracted content to a file (default: print to conversation)

If no URL is provided, ask the user for one.

## Phase 1: Ensure Agent-Reach is Installed

1. **Check if Agent-Reach is available.** Run:
   ```bash
   python3 -c "from agent_reach import fetch_url_content" 2>/dev/null && echo "OK" || echo "MISSING"
   ```

2. **If missing, install it.** Clone and install:
   ```bash
   if [ ! -d "$HOME/.local/share/agent-reach" ]; then
     git clone https://github.com/Panniantong/Agent-Reach.git "$HOME/.local/share/agent-reach"
     cd "$HOME/.local/share/agent-reach" && pip install -r requirements.txt
   fi
   ```
   Then add it to the Python path for the current session:
   ```bash
   export PYTHONPATH="$HOME/.local/share/agent-reach:$PYTHONPATH"
   ```

## Phase 2: Fetch Content

1. **Extract content from the URL.** Run a Python script that uses Agent-Reach:
   ```bash
   cd "$HOME/.local/share/agent-reach" && python3 -c "
   from agent_reach import fetch_url_content
   content = fetch_url_content('THE_URL_HERE')
   print(content)
   "
   ```

2. **If the fetch fails**, try these fallbacks in order:
   - Retry once (transient network errors)
   - If blocked (403/captcha), inform the user the site may block automated access
   - If the URL is invalid, ask the user to double-check it

## Phase 3: Process and Deliver

1. **If `--raw` was specified**, return the extracted text as-is without further processing.

2. **If `--save <path>` was specified**, write the content to the file:
   ```bash
   cat <<'CONTENT' > <path>
   <extracted content>
   CONTENT
   ```
   Report the file path and size.

3. **Otherwise (default behavior)**, present the extracted content to the user in a clean format:
   - Show the source URL
   - Show the extracted text
   - Offer to summarize, save, or use the content for a specific task

## Use Cases

- **Research**: fetch and compare information across multiple articles or sources
- **Documentation**: pull current framework/library docs for coding tasks
- **Summarization**: extract article text and produce a concise summary
- **Fact-checking**: retrieve live web content to verify claims or data
- **Context loading**: stuff extracted content into an LLM prompt for grounded responses

## Important Rules

- Only fetch **public** URLs. Do not attempt to bypass authentication or paywalls.
- If the extracted content is very long, warn the user before dumping it all — offer to summarize or save to file instead.
- Respect rate limits. Do not make rapid repeated requests to the same domain.
- If Agent-Reach returns empty or garbage content, tell the user honestly rather than fabricating results.
- This tool is for **prototyping and personal use**. For production workloads, recommend commercial alternatives like Firecrawl or Browserbase.
