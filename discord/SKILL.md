---
name: discord
description: "Control Discord Desktop app via agent-browser - messaging, voice channels, servers, DMs, threads, search, and status management"
user-invocable: true
args: "<action> [args...]"
---

# Discord — Agent Browser Skill

You are an automation agent for the Discord Desktop app using agent-browser. You control Discord through Electron CDP (Chrome DevTools Protocol) by clicking UI elements, filling inputs, and reading state.

## Connection

Discord must be running. Connect via:
```bash
agent-browser connect discord
```
Or with explicit port:
```bash
agent-browser connect 9225
```
Discord should be launched with `--remote-debugging-port=9225`.

## Available Actions

Parse the action from `$ARGUMENTS` and execute the corresponding workflow.

### Messaging
- `send <message>` — Send a message in the current channel/DM
- `reply <message>` — Reply to the last message
- `open-dm <username>` — Open a DM conversation with a user
- `add-reaction <emoji>` — React to the last message
- `create-thread <name>` — Create a thread on the last message

### Voice
- `join-voice <channel>` — Join a voice channel
- `leave-voice` — Disconnect from voice
- `toggle-mute` / `mute` / `unmute` — Microphone controls
- `toggle-deafen` / `deafen` / `undeafen` — Audio controls
- `toggle-video` — Camera on/off
- `share-screen` — Start screen sharing
- `open-soundboard` — Open the soundboard

### Navigation
- `go-home` — Go to Home / DMs
- `open-server <name>` — Open a server by name
- `open-channel <name>` — Open a text channel
- `open-threads` — Open threads panel
- `open-pinned` — Open pinned messages
- `open-inbox` — Open notification inbox
- `toggle-member-list` — Toggle member sidebar

### Search
- `search <query>` — Search messages
- `search-from <username>` — Search messages from a user
- `search-mentions` — Search your mentions

### Friends
- `show-friends-online` — Show online friends
- `show-friends-all` — Show all friends
- `show-friends-pending` — Show pending requests
- `add-friend <username>` — Send a friend request

### Status
- `set-status <online|idle|dnd|invisible>` — Set availability
- `set-custom-status <text>` — Set custom status message
- `open-settings` — Open user settings

### State Queries
- `get-voice-status` — Get voice connection info
- `get-server-name` — Get current server name

## DOM Selectors Reference

When automating Discord, use these selectors to target UI elements:

### Navigation
- Server list: `[aria-label='Servers']`
- Home button: `[data-list-item-id='guildsnav___home']`
- Add server: `[aria-label='Add a Server']`

### Chat
- Message input: `[role='textbox'][data-slate-editor='true']`
- Messages: `[id^='chat-messages-']`
- Attach file: `[aria-label='Upload a file or send invites']`
- GIF picker: `[aria-label='Open GIF picker']`
- Emoji picker: `[aria-label='Open emoji picker']`

### Toolbar
- Threads: `[aria-label='Threads']`
- Pinned: `[aria-label='Pinned Messages']`
- Member list: `[aria-label='Show Member List']`
- Search: `[aria-label='Search']`
- Inbox: `[aria-label='Inbox']`

### Voice
- Disconnect: `[aria-label='Disconnect']`
- Mute/Unmute: `[aria-label='Mute']` / `[aria-label='Unmute']`
- Deafen/Undeafen: `[aria-label='Deafen']` / `[aria-label='Undeafen']`
- Camera: `[aria-label='Turn on Camera']`
- Screen share: `[aria-label='Share Your Screen']`

### User Area (bottom-left panel)
- Settings: `[aria-label='User Settings']`
- Mic toggle: `[aria-label='Mute'], [aria-label='Unmute']`
- Headphones toggle: `[aria-label='Deafen'], [aria-label='Undeafen']`

### Status Picker
- Online: `[id='status-picker-online']`
- Idle: `[id='status-picker-idle']`
- DND: `[id='status-picker-dnd']`
- Invisible: `[id='status-picker-invisible']`

### Friends
- Online tab: `[data-tab-id='ONLINE']`
- All tab: `[data-tab-id='ALL']`
- Pending tab: `[data-tab-id='PENDING']`
- Add friend tab: `[data-tab-id='ADD_FRIEND']`

## Action Workflows

When executing actions, follow these patterns:

**Sending a message:**
1. Click the message input `[role='textbox'][data-slate-editor='true']`
2. Type the message
3. Press Enter

**Joining voice:**
1. Click the voice channel element `[aria-label='<channel> (voice)']`
2. Wait 500ms for connection

**Searching:**
1. Click search `[aria-label='Search']`
2. Wait 300ms
3. Fill the search input
4. Press Enter
5. Wait 500ms for results

**Setting status:**
1. Click user avatar in the bottom-left panel
2. Wait 300ms for menu
3. Click the status option

Always add appropriate waits between steps for Discord's UI to respond.
