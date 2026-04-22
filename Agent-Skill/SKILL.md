---
name: rocketsim
description: "Use the `rocketsim` CLI on PATH to inspect and interact with iOS Simulator apps via the rs/1 JSON protocol. Read visible accessibility elements as a compact agent snapshot, then tap, long-press, type, swipe, scroll, focus, or press hardware buttons by stable element id with race protection via screen hashes. Use when: (1) the user mentions RocketSim, the rocketsim command, or RocketSim CLI, (2) interacting with an app in the iOS Simulator, (3) navigating or testing a simulator app with AI, (4) reading visible UI state or accessibility elements, (5) tapping, long-pressing, swiping, scrolling, typing, or pressing simulator buttons, (6) using RocketSim instead of ad-hoc simulator automation."
---

# RocketSim Agent Skill

`rocketsim` is a CLI on `PATH` that talks to the running RocketSim app via the
`rs/1` JSON protocol. Use it to inspect and interact with iOS Simulator apps
reliably and with low token cost.

## Prerequisite (one-time)

If `which rocketsim` fails, ask the user to open **RocketSim → Settings →
Command Line Tool** and click *Install Command Line Tool*. RocketSim symlinks
the CLI into `/opt/homebrew/bin` (Apple Silicon) or `/usr/local/bin` (Intel),
so the CLI on `PATH` always equals the installed app version with zero drift.

If RocketSim is not installed, link to
<https://apps.apple.com/app/apple-store/id1504940162>.

## The agent loop

Every interaction follows the same four steps:

1. **Snapshot** — `rocketsim screen` (~25 tokens) for a tiny summary, or
   `rocketsim elements --agent` for the full snapshot.
2. **Act** — `rocketsim interact <action> --id <N> --screen <hash>`. Always
   pass `--screen` so the CLI fails fast on stale snapshots instead of acting
   on the wrong element.
3. **Wait** — `rocketsim wait screen-changed --from <hash>` if the action
   navigates, or `rocketsim wait keyboard --state hidden|visible` if a
   keyboard appears or dismisses.
4. **Re-snapshot** — repeat from step 1.

Pick the right snapshot mode for the step:

| Mode  | Use when                                                  | Includes                                                         |
| ----- | --------------------------------------------------------- | ---------------------------------------------------------------- |
| `nav` | Deciding what screen you are on, or which area to act in. | Headings, tabs, nav bars, top-level controls. Smallest body.     |
| `act` | About to tap, type, swipe, or scroll a specific element.  | Interactive elements with id, label, role, frame. Default mode.  |
| `debug` | Investigating why an action failed or an element is missing. | Full tree with parent ids, traits, raw frames.               |

Add `--since <hash>` to `elements --agent` to get an empty body when the
screen hash is unchanged; skip the second read entirely.

## Targeting elements

Always target by **id** from the latest snapshot. Coordinates and label scans
are advanced fallbacks.

```bash
rocketsim interact tap        --id 7 --screen $H
rocketsim interact long-press --id 12 --duration 1.5 --screen $H
rocketsim interact type       --id 4 --text "hello" --screen $H
rocketsim interact type       --id 4 --stdin --screen $H < message.txt
rocketsim interact type       --id 4 --file ./body.txt --screen $H
rocketsim interact swipe      --id 9 --direction up --screen $H
rocketsim interact scroll     --id 9 --direction up --pages 0.5 --screen $H
rocketsim interact focus      --id 4 --screen $H
```

Hardware buttons do not need an id:

```bash
rocketsim interact button --name home
rocketsim interact button --name lock --duration 1.5   # lock and side support hold
```

Pass `--screen <hash>` on every interaction. If the snapshot has changed
under you, the CLI returns `snapshot_changed` instead of acting on the
wrong element. Re-snapshot and retry with the new id.

## Blocking primitives

```bash
rocketsim wait screen-changed --from $H   [--timeout 5]
rocketsim wait element        --label "Done" [--state enabled] [--timeout 5]
rocketsim wait keyboard       --state hidden|visible [--timeout 5]
```

Batch sequential steps with `do` so the CLI re-snapshots and re-validates
between each step:

```bash
rocketsim do \
  --step "interact tap --id 3" \
  --step "interact type --id 4 --text 'hello'" \
  --step "interact tap --label Submit" \
  [--continue-on-error]
```

Need an annotated screenshot of the current state for visual reasoning?

```bash
rocketsim snapshot --agent --agent-mode act --image /tmp/state.png
```

## Error taxonomy

Every error response is JSON with `code`, `message`, `recovery_suggestion`,
and an optional `context` map (expected vs current hash, candidate ids, etc.).

| Code                        | Meaning                                               | Recovery                                                            |
| --------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------- |
| `snapshot_changed`          | The hash you passed via `--screen` no longer matches. | Re-snapshot; retry with the new id and hash.                        |
| `element_not_found`         | No element matches the given id or selector.          | Re-snapshot and target a current id.                                |
| `multiple_matches`          | Selector matched more than one element.               | Use `--id` instead, or refine the selector.                         |
| `element_disabled`          | Element exists but is not enabled.                    | Wait for the precondition or pick another path.                     |
| `element_offscreen`         | Element exists but is not hittable.                   | Scroll the parent until it becomes hittable.                        |
| `keyboard_obscures_target`  | Software keyboard covers the target.                  | Dismiss the keyboard or scroll the target into view, then retry.    |
| `wait_timeout`              | The wait condition did not resolve within `--timeout`. | Increase the timeout or reconsider the precondition.               |
| `simulator_not_focused`     | The simulator window is not active.                   | Ask the user to bring the simulator window forward.                 |
| `simulator_not_booted`      | No booted simulator.                                  | Ask the user to boot a simulator.                                   |
| `app_not_running`           | Target app is not running.                            | Ask the user to launch the app under test.                          |

## End-to-end example

```bash
SNAP=$(rocketsim elements --agent --agent-mode act)
HASH=$(jq -r '.screen.hash' <<<"$SNAP")
ID=$(jq '.elements[] | select(.label=="Sign In") | .id' <<<"$SNAP")

rocketsim interact tap --id "$ID" --screen "$HASH"
rocketsim wait screen-changed --from "$HASH" --timeout 5

SNAP=$(rocketsim elements --agent --agent-mode act)
HASH=$(jq -r '.screen.hash' <<<"$SNAP")
EMAIL=$(jq '.elements[] | select(.label=="Email") | .id' <<<"$SNAP")

rocketsim interact type --id "$EMAIL" --text "user@example.com" --screen "$HASH"
```

## Reference

The canonical long-form `rs/1` protocol reference, including full JSON
schemas, advanced selectors, and coordinate-based fallbacks, lives at
<https://www.rocketsim.app/docs/features/agentic-development/>.
