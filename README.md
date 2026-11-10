# home-assistant

Personal Home Assistant configuration and reusable blueprints.

## Blueprints

### Switch → Light control

Controls one or more lights from a Shelly switch input running in detached mode.

| Action | Result |
| --- | --- |
| Single push | Toggles the lights as a group (any on → all off, otherwise all on) |
| Double push | Sets a fixed brightness, color temperature, and transition (configurable) |
| Hold | Dims up and down between min and max; release keeps the level |

**Maintenance (auto-restart):** Some lights hang when they sit idle for a long time or when power comes back after an outage. If any selected light stays `unavailable` for a set time (default 60 s), the blueprint turns the selected **power switch** (the relay that feeds the fixture) off for a few seconds (default 2 s), then back on. It then waits for the lights to come back online (default 60 s). If they don't, it tries again, up to 3 more times by default. When the lights recover, they go back to the on/off state the group had before the restart. If every attempt fails, a persistent notification lists the lights that are still stuck. The feature is on by default but does nothing until you select a power switch. It also checks once after Home Assistant starts.

**Requirements:** The Shelly input must be in detached mode with input type *Button*. Its event entity must expose `single_push`, `double_push`, `long_push` and `btn_up`. You can check this in the entity's `event_types` attribute.

#### Install

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fmatkovsky%2Fhome-assistant%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fswitch_light_control.yaml)

Or import it manually:

1. Go to **Settings → Automations & Scenes → Blueprints → Import Blueprint**.
2. Paste this URL:
   ```
   https://github.com/matkovsky/home-assistant/blob/main/blueprints/automation/switch_light_control.yaml
   ```
3. Create an automation from the blueprint, then select the switch's event entity and the light(s).

#### Update

Go to **Blueprints → ⋮ → Re-import blueprint**. Every automation that uses the blueprint picks up the changes.

#### Changelog

- **1.1**: Adds the Maintenance section, which auto-restarts lights that stay unavailable by power-cycling their relay. The automation mode changes from `single` to `parallel`, so a restart doesn't block the button.
- **1.0**: Single push, double push and hold-to-dim.
