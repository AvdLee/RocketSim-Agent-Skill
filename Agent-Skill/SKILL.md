---
name: rocketsim
description: "Use the `rocketsim` CLI on PATH to inspect and interact with iOS Simulator apps via the rs/1 JSON envelope. Read visible accessibility elements as a compact agent snapshot, then tap, long-press, type, swipe, scroll, focus, or press hardware buttons by stable element id with race protection via screen hashes. Use when: (1) the user mentions RocketSim, the rocketsim command, or RocketSim CLI, (2) interacting with an app in the iOS Simulator, (3) navigating or testing a simulator app with AI, (4) reading visible UI state or accessibility elements, (5) tapping, long-pressing, swiping, scrolling, typing, or pressing simulator buttons, (6) using RocketSim instead of ad-hoc simulator automation."
---

# RocketSim Agent Skill

<<<<<<< Updated upstream
`rocketsim` is a CLI on `PATH` that drives the running RocketSim app via the
**rs/1 JSON envelope**. Use it to inspect and interact with iOS Simulator apps
=======
`rocketsim` is a CLI on `PATH` that talks to the running RocketSim app via the
`rs/1` JSON protocol for reads and interactions. The `snapshot` command is the
one exception: it streams annotated PNG bytes to stdout so you save it with
shell redirection. Use the CLI to inspect and interact with iOS Simulator apps
>>>>>>> Stashed changes
reliably and with low token cost.

## Setup (one-time)

If `which rocketsim` fails, ask the user to open **RocketSim → Settings →
Command Line Tool** and click *Install Command Line Tool*. RocketSim symlinks
the CLI into `/opt/homebrew/bin` (Apple Silicon) or `/usr/local/bin` (Intel),
so the CLI on `PATH` always equals the installed app version.

If RocketSim is not installed, link to
<https://apps.apple.com/app/apple-store/id1504940162>.

Verify the stack end-to-end with `rocketsim doctor` — emits a
`{checks, summary}` envelope and exits non-zero on any `fail`.

## The rs/1 envelope

**Every** `rocketsim` command writes a single JSON envelope to stdout. Parse
it; never read plain text.

```json
{"rs":"1","ok":true,"data":{ /* command-specific payload */ }}
```

```json
{"rs":"1","ok":false,"error":{
  "code":"snapshot_changed",
  "message":"Screen has moved past the snapshot you targeted.",
  "recovery_suggestion":"Re-run rocketsim elements --agent and retry with the new id and hash.",
  "context":{"expected":"a1b2c3d4","current":"e5f6a7b8"}
}}
```

Branch on `ok`. On failure, switch on `error.code` (see taxonomy below) and
follow `recovery_suggestion`.

## The agent loop

1. **Snapshot** — `rocketsim screen` (~25 tokens) for a tiny summary, or
   `rocketsim elements --agent` for the full snapshot. Pull `screen.hash`
   and the `id` you want from the envelope's `data`.
2. **Resolve id + hash** from `data.elements[]` and `data.screen.hash`.
3. **Interact** — `rocketsim interact <action> --id <N> --screen <hash>`.
   `--screen` is the race-window guard: the server only accepts the **current
   or immediately-previous** snapshot and otherwise returns `snapshot_changed`.
4. **Wait** — `rocketsim wait screen-changed --from <hash>` if the action
   navigates, or `rocketsim wait keyboard --state hidden|visible` if a
   keyboard appears or dismisses. Then loop back to step 1.

Snapshot modes (`elements --agent --agent-mode <mode>`):

| Mode    | Use when                                              | Body                                                  |
| ------- | ----------------------------------------------------- | ----------------------------------------------------- |
| `nav`   | Deciding what screen you are on or where to act.      | Headings, tabs, nav bars, top-level controls.         |
| `act`   | About to tap, type, swipe, or scroll a known element. | Interactive elements with id, label, role, frame.     |
| `debug` | An action failed or an element looks wrong.           | Full tree with parent ids, traits, raw frames.        |

`data.scrollables[]` is populated with every scrollable container in view.
Add `--since <hash>` to get an empty body when the screen hash is unchanged.

## Interactions

Always target by **id** from the latest snapshot.

```bash
rocketsim interact tap        --id 7  --screen $H
rocketsim interact long-press --id 12 --duration 1.5 --screen $H
rocketsim interact type "user@example.com" --id 4 --screen $H
rocketsim interact type --stdin            --id 4 --screen $H < message.txt
rocketsim interact type --file ./body.txt  --screen $H
rocketsim interact swipe  --id 9 --direction up --screen $H
rocketsim interact swipe  --from "200,400" --to "200,100"      # coordinate-anchored
rocketsim interact scroll --id 9  --direction up --pages 1 --screen $H
rocketsim interact focus  --id 4  --screen $H
```

`interact type` takes the text as a **positional** argument (not `--text`).
`interact scroll --id <N>` auto-resolves to the nearest scrollable ancestor
when `<N>` is a child element.

Hardware buttons need no id:

```bash
rocketsim interact button --name home
rocketsim interact button --name lock --duration 1.5   # lock and side support hold
```

**Toggles / switches**: in the snapshot they appear as `role=checkBox` with
`value="0"` (off) or `value="1"` (on). Tap them like any other element; the
server verifies the value flipped and returns an error if it did not.

## Blocking primitives

```bash
rocketsim wait screen-changed --from $H   [--timeout 5]
rocketsim wait element        --label "Done" [--state enabled] [--timeout 5]
rocketsim wait keyboard       --state hidden|visible [--timeout 5]
```

Batch sequential steps with `do` (re-snapshots and re-validates between each):

```bash
rocketsim do \
  --step "interact tap --id 3" \
  --step "interact type 'hello' --id 4" \
  --step "interact tap --label Submit" \
  [--continue-on-error]
```

Annotated screenshot for visual reasoning:

```bash
rocketsim snapshot --agent-mode act --screen "$HASH" > /tmp/state.png
```

## Error taxonomy

`error.code` is an `AgentErrorCode` raw value — stable, machine-parseable.

| Code                       | When                                                  | Do                                                         |
| -------------------------- | ----------------------------------------------------- | ---------------------------------------------------------- |
| `snapshot_changed`         | `--screen` is older than the previous snapshot.       | Re-snapshot; retry with the new id and hash.               |
| `element_not_found`        | No element matches the id/selector.                   | Re-snapshot and target a current id.                       |
| `multiple_matches`         | A label/selector matched more than one element.       | Use `--id` instead, or refine the selector.                |
| `keyboard_obscures_target` | The software keyboard covers the target frame.        | Dismiss the keyboard or scroll the target into view.       |
| `wait_timeout`             | A `wait` did not resolve within `--timeout`.          | Increase the timeout or reconsider the precondition.       |
| `invalid_argument`         | A flag combination or value is rejected client-side.  | Read `error.message`; fix the flags and retry.             |
| `duration_not_supported`   | The action does not accept the supplied `--duration`. | Drop `--duration`, or use the action that supports holds.  |
| `internal_error`           | Server-side bug or unexpected state.                  | Retry once; otherwise surface to the user with `message`.  |

## End-to-end example

```bash
SNAP=$(rocketsim elements --agent --agent-mode act)
HASH=$(jq -r '.data.screen.hash'                       <<<"$SNAP")
ID=$(  jq    '.data.elements[] | select(.label=="Sign In") | .id' <<<"$SNAP")

rocketsim interact tap --id "$ID" --screen "$HASH"
rocketsim wait screen-changed --from "$HASH" --timeout 5

SNAP=$(rocketsim elements --agent --agent-mode act)
HASH=$(jq -r '.data.screen.hash'                       <<<"$SNAP")
EMAIL=$(jq   '.data.elements[] | select(.label=="Email") | .id' <<<"$SNAP")

rocketsim interact type "user@example.com" --id "$EMAIL" --screen "$HASH"
```

## Reference

Canonical long-form `rs/1` protocol reference (full envelope schemas, advanced
selectors, coordinate fallbacks):
<https://www.rocketsim.app/docs/features/agentic-development/>.
