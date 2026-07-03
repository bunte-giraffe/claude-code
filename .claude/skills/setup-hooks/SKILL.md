---
description: Configure Claude Code hooks for the GlobalAI workshop. Sets up SessionStart, PreToolUse, and ConfigChange hooks with OS-appropriate commands.
---

Configure Claude Code hooks for this workshop participant's machine.

## Steps

1. Detect the operating system by running `uname -s` (Mac/Linux) or checking `$env:OS` (Windows).

2. Read the current `~/.claude/settings.json`. If it doesn't exist, start with `{}`.

3. Merge the following hooks into the `hooks` section — do not overwrite any existing hooks the user may already have:

### SessionStart hook
- **Mac/Linux**: play a welcome sound using `say "Welcome to the GlobalAI workshop!"` 
- **Windows**: use `powershell -c "Add-Type -AssemblyName System.Speech; (New-Object System.Speech.Synthesis.SpeechSynthesizer).Speak('Welcome to the GlobalAI workshop!')"`

### PreToolUse hook (all platforms — requires `jq`)
```
jq -c '{systemMessage: (["=== PreToolUse ===", "event:    " + .hook_event_name, "tool:     " + .tool_name, "id:       " + .tool_use_id] + (.tool_input | to_entries | map("  " + .key + ": " + (.value | tostring))) | join("\n"))}'
```

### ConfigChange hook
- **Mac/Linux**: `jq '.' > /tmp/configchange_payload.json`
- **Windows**: `jq '.' > $env:TEMP\configchange_payload.json`

4. Write the updated settings back to `~/.claude/settings.json`.

5. Verify the JSON is valid by running `jq . ~/.claude/settings.json`.

6. Tell the user which OS was detected, which hooks were added, and remind them that `jq` must be installed for the PreToolUse hook to work (Mac: `brew install jq`, Windows: `winget install jqlang.jq`).
