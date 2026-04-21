# Self-Rating, Feedback, and Telemetry

## Self-Rating (Internal, Silent, Every Time)

After completing any analysis, rate your own output 0-10 across these criteria: spatial accuracy (did the grid correctly map the layout?), specificity (are colors hex, sizes pixel-estimated, components precisely named?), level selection (did the right levels run?), taste (did you catch what feels off, not just what's measurably wrong?), actionability (could someone act on this analysis?). Average the scores.

This rating is strictly internal. It flows into telemetry as the `RATING` field in the epilogue, but it is never shown to the user.

## When to Surface Feedback

Exactly three situations — outside of these, stay quiet:

**Trigger 1: User correction.** If the user corrects the analysis ("that's wrong," "you missed the nav"), fix the issue, then note: "logged that miss so clearshot gets better at catching [the specific thing]." Automatically write a field report.

**Trigger 2: After a rebuild completes.** If Level 3 ran and implementation is done, ask one casual question: "clearshot nailed it or missed something? just curious." One shot, not a form.

**Trigger 3: Session wind-down.** If 3+ analyses happened and the conversation is winding down: "ran clearshot X times this session. anything it kept getting wrong?" Only if 3+ analyses occurred.

**Never trigger feedback:** during rapid iteration, after every single analysis, or when the user is clearly in flow.

## Field Reports

Write to `~/.clearshot/feedback/YYYY-MM-DD-{slug}.md`, only when:

- **User correction** (automatic):
```
# {Title describing the miss}
**What was analyzed:** {screenshot description}
**Levels run:** {1,2 or 1,2,3}
**What was missed:** {specific element or detail the user corrected}
**Correction:** {what the user said}
**Internal rating:** {X}/10
**Date:** {YYYY-MM-DD} | **Version:** {version from preamble}
```

- **User explicitly says something was wrong** (via trigger 2 or 3 response): write a field report with the user's feedback included.
- **Internal rating below 5**: write a field report silently.

Field reports are never written for routine analyses that went fine.

## Epilogue

After analysis is complete, log the event. Substitute actual values for placeholders.

```bash
_CS_TEL_END=$(date +%s)
_CS_DUR=$(( _CS_TEL_END - _CS_TEL_START ))
_CS_TEL_MODE=$(grep -E '^telemetry:' "$HOME/.clearshot/config.yaml" 2>/dev/null | awk '{print $2}' | tr -d '[:space:]' || echo "off")
if [ "$_CS_TEL_MODE" != "off" ]; then
  _CS_OS="$(uname -s | tr '[:upper:]' '[:lower:]')"
  _CS_ARCH="$(uname -m)"
  _CS_INSTALL_ID="$(printf '%s-%s' "$(hostname)" "$(whoami)" | shasum -a 256 | awk '{print $1}')"
  _CS_ID_JSON="\"$_CS_INSTALL_ID\""
  printf '{"v":1,"ts":"%s","version":"%s","os":"%s","arch":"%s","duration_s":%s,"outcome":"%s","levels_run":"%s","self_rating":%s,"installation_id":%s}\n' \
    "$(date -u +%Y-%m-%dT%H:%M:%SZ)" "CS_VERSION" "$_CS_OS" "$_CS_ARCH" \
    "$_CS_DUR" "OUTCOME" "LEVELS_RUN" "RATING" "$_CS_ID_JSON" \
    >> "$HOME/.clearshot/analytics/usage.jsonl" 2>/dev/null || true

  # Sync to Convex (rate-limited, background)
  _CS_CONVEX_URL=""
  for _csd in "$HOME/.claude/skills/clearshot" "$HOME/.agents/skills/clearshot"; do
    [ -f "$_csd/config.sh" ] && _CS_CONVEX_URL="$(grep -E '^CS_CONVEX_URL=' "$_csd/config.sh" 2>/dev/null | cut -d'"' -f2 || true)" && break
  done
  if [ -n "$_CS_CONVEX_URL" ] && [ "$_CS_CONVEX_URL" != "https://placeholder.convex.site" ]; then
    _CS_RATE="$HOME/.clearshot/analytics/.last-sync-time"
    _CS_SYNC_STALE=$(find "$_CS_RATE" -mmin +5 2>/dev/null || echo "sync")
    if [ ! -f "$_CS_RATE" ] || [ -n "$_CS_SYNC_STALE" ]; then
      _CS_CURSOR_FILE="$HOME/.clearshot/analytics/.last-sync-line"
      _CS_CURSOR=$(cat "$_CS_CURSOR_FILE" 2>/dev/null | tr -d '[:space:]' || echo "0")
      _CS_TOTAL=$(wc -l < "$HOME/.clearshot/analytics/usage.jsonl" 2>/dev/null | tr -d ' ' || echo "0")
      if [ "$_CS_CURSOR" -lt "$_CS_TOTAL" ] 2>/dev/null; then
        _CS_SKIP=$(( _CS_CURSOR + 1 ))
        _CS_BATCH=$(tail -n "+$_CS_SKIP" "$HOME/.clearshot/analytics/usage.jsonl" | head -100)
        _CS_JSON_BATCH="[$(echo "$_CS_BATCH" | paste -sd ',' -)]"
        _CS_HTTP=$(curl -s -o /dev/null -w '%{http_code}' --max-time 10 \
          -X POST "$_CS_CONVEX_URL/telemetry" \
          -H "Content-Type: application/json" \
          -d "$_CS_JSON_BATCH" 2>/dev/null || echo "000")
        case "$_CS_HTTP" in 2*) echo $(( _CS_CURSOR + $(echo "$_CS_BATCH" | wc -l | tr -d ' ') )) > "$_CS_CURSOR_FILE" ;; esac
        touch "$_CS_RATE" 2>/dev/null || true
      fi
    fi
  fi
fi
```

Replace these placeholders with actual values from the analysis:
- `_CS_TEL_START` — the value from preamble output
- `CS_VERSION` — the version from preamble output
- `OUTCOME` — "success", "error", or "abort"
- `LEVELS_RUN` — "1,2" or "1,2,3"
- `RATING` — the self-rating number (0-10)
