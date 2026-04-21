# Preamble — Run Before Any Analysis

Execute this bash block first, before any analysis. It locates the skill directory, reads config, checks for updates, and initializes session tracking.

```bash
# ─── Find skill directory (works from any install path) ───────
_CS_DIR=""
for _d in "$HOME/.claude/skills/clearshot" "$HOME/.agents/skills/clearshot"; do
  [ -f "$_d/SKILL.md" ] && _CS_DIR="$_d" && break
done
# fallback: search
[ -z "$_CS_DIR" ] && _CS_DIR="$(cd "$(dirname "$(find "$HOME/.claude" "$HOME/.agents" -name SKILL.md -path '*/clearshot/*' -print -quit 2>/dev/null)")" 2>/dev/null && pwd || echo "")"
_CS_VER=""
[ -n "$_CS_DIR" ] && [ -f "$_CS_DIR/VERSION" ] && _CS_VER="$(cat "$_CS_DIR/VERSION" | tr -d '[:space:]')"
_CS_STATE="$HOME/.clearshot"
mkdir -p "$_CS_STATE/analytics" "$_CS_STATE/feedback"

# ─── First-run detection ─────────────────────────────────────
_CS_FIRST_RUN="no"
[ ! -f "$_CS_STATE/config.yaml" ] && _CS_FIRST_RUN="yes"

# ─── Read config (only if it exists) ─────────────────────────
_CS_UPDATE_MODE="ask"
_CS_TEL="off"
_CS_TEL_PROMPTED="no"
if [ -f "$_CS_STATE/config.yaml" ]; then
  _CS_UPDATE_MODE=$(grep -E '^update_mode:' "$_CS_STATE/config.yaml" 2>/dev/null | awk '{print $2}' | tr -d '[:space:]' || echo "ask")
  _CS_TEL=$(grep -E '^telemetry:' "$_CS_STATE/config.yaml" 2>/dev/null | awk '{print $2}' | tr -d '[:space:]' || echo "off")
fi
[ -f "$_CS_STATE/.telemetry-prompted" ] && _CS_TEL_PROMPTED="yes"

# ─── Version check (only if user opted into updates) ─────────
# No network calls until config exists and user has chosen
if [ -n "$_CS_VER" ] && [ "$_CS_FIRST_RUN" = "no" ]; then
  _CS_CACHE="$_CS_STATE/last-update-check"
  _STALE=""
  [ -f "$_CS_CACHE" ] && _STALE=$(find "$_CS_CACHE" -mmin +60 2>/dev/null || true)
  if [ ! -f "$_CS_CACHE" ] || [ -n "$_STALE" ]; then
    _CS_REMOTE=$(curl -sf --max-time 5 "https://raw.githubusercontent.com/udayanwalvekar/clearshot/main/VERSION" 2>/dev/null | tr -d '[:space:]' || true)
    if echo "$_CS_REMOTE" | grep -qE '^[0-9]+\.[0-9.]+$' 2>/dev/null; then
      if [ "$_CS_VER" != "$_CS_REMOTE" ]; then
        if [ "$_CS_UPDATE_MODE" = "always" ]; then
          cd "$_CS_DIR" && git pull origin main --quiet 2>/dev/null
          _CS_VER="$(cat "$_CS_DIR/VERSION" 2>/dev/null | tr -d '[:space:]')"
          echo "CS_AUTO_UPDATED: $_CS_VER"
          echo "UP_TO_DATE $_CS_VER" > "$_CS_CACHE"
        else
          echo "UPGRADE_AVAILABLE $_CS_VER $_CS_REMOTE" > "$_CS_CACHE"
          echo "CS_UPGRADE: UPGRADE_AVAILABLE $_CS_VER $_CS_REMOTE"
        fi
      else
        echo "UP_TO_DATE $_CS_VER" > "$_CS_CACHE"
      fi
    fi
  else
    _CACHED="$(cat "$_CS_CACHE" 2>/dev/null || true)"
    case "$_CACHED" in UPGRADE_AVAILABLE*)
      if [ "$_CS_UPDATE_MODE" = "always" ]; then
        cd "$_CS_DIR" && git pull origin main --quiet 2>/dev/null
        _CS_VER="$(cat "$_CS_DIR/VERSION" 2>/dev/null | tr -d '[:space:]')"
        echo "CS_AUTO_UPDATED: $_CS_VER"
        echo "UP_TO_DATE $_CS_VER" > "$_CS_CACHE"
      else
        echo "CS_UPGRADE: $_CACHED"
      fi
      ;;
    esac
  fi
fi

# ─── Session tracking ────────────────────────────────────────
_CS_SESSION_ID="$$-$(date +%s)"
_CS_TEL_START=$(date +%s)

echo "CS_FIRST_RUN: $_CS_FIRST_RUN"
echo "CS_SKILL_DIR: $_CS_DIR"
echo "CS_UPDATE_MODE: $_CS_UPDATE_MODE"
echo "CS_TEL_PROMPTED: $_CS_TEL_PROMPTED"
echo "CS_TELEMETRY: ${_CS_TEL:-off}"
echo "CS_SESSION_ID: $_CS_SESSION_ID"
echo "CS_TEL_START: $_CS_TEL_START"
echo "CS_VERSION: ${_CS_VER:-unknown}"
```

## Interpreting Preamble Output

**If `CS_FIRST_RUN` is `yes`:**
First-time run — no config exists. Tell the user:
"clearshot needs a quick first-run setup (two questions, arrow keys + enter):"
Then instruct: `! {CS_SKILL_DIR}/bin/onboarding.sh`
Proceed with analysis after setup (or if user declines — no network calls happen without config).

**If `CS_AUTO_UPDATED` appears:**
Mention briefly: "clearshot updated to v{version}" and continue.

**If `CS_UPGRADE` shows `UPGRADE_AVAILABLE <old> <new>`:**
User has `update_mode: ask`. Tell them:
"clearshot v{new} is available (you're on v{old}):"
Then instruct: `! {CS_SKILL_DIR}/bin/update-prompt.sh {old} {new}`
Continue on current version if skipped.

**If `CS_TEL_PROMPTED` is `no` (but `CS_FIRST_RUN` is also `no`):**
User skipped telemetry question. Tell them to run:
`! {CS_SKILL_DIR}/bin/telemetry-setup.sh`
Proceed with analysis regardless.
