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
