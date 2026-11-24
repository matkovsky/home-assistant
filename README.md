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

### Scheduled blinds (sun/time) with motion guard

Moves one or more blinds/shades to a set position at sunrise or sunset (with an optional ± offset, e.g. *sunset + 1 h*) or at a fixed time. Create one automation per schedule, e.g. one for "close at sunset" and one for "open at sunrise".

Position uses the Home Assistant convention: **0 = fully closed, 100 = fully open**. Covers that don't support positions are closed at 0 and opened at 100.

**Safety guard (optional):** Select motion sensors and a *No motion for* interval, plus any doors/windows (e.g. the terrace door). At the scheduled time, the blueprint waits until every motion sensor has been clear for the full interval and every door/window is closed, then moves the blinds. If the guard doesn't clear within the *Postpone limit* (default 2 h), the blinds are left alone and a persistent notification says which sensor blocked them. Unavailable sensors count as not clear. Set the limit to 0 to skip right away instead of waiting.

**Also:** days of week, and an optional `input_boolean` that must be on for the schedule to run (e.g. a vacation-mode helper).

#### Install

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fmatkovsky%2Fhome-assistant%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fcover_schedule.yaml)

Or import this URL via **Settings → Automations & Scenes → Blueprints → Import Blueprint**:

```
https://github.com/matkovsky/home-assistant/blob/main/blueprints/automation/cover_schedule.yaml
```

#### Changelog

- **1.0**: Sun/fixed-time schedule with offset, position, motion + door/window guard with postpone, weekdays, enable toggle.

### Bathroom vent (humidity + max runtime + button)

Runs a bathroom vent (e.g. a Shelly relay) based on humidity, with a hard runtime limit and an optional toggle button. Create one automation per bathroom.

**Every run follows the same rule:** the vent stops when humidity has stayed below *Turn off below* for the *Stay below for* time **or** when *Maximum runtime* is reached, whichever comes first. This applies to runs started by:

| Started by | Behaviour |
| --- | --- |
| Humidity rising above *Turn on above* | Vent turns on, then the run rule applies |
| Button gesture while the vent is off | Vent turns on, then the run rule applies. If humidity is already below the off level, it runs for the full max runtime |
| Button gesture while the vent is on | Vent turns off **immediately** and the run is cancelled |
| Dashboard / wall switch / voice / other automation | Same run rule if *Also limit runs started elsewhere* is on (default). Turning it off from anywhere cancels the run |
| Humidity rising again during a run (new shower) | Max-runtime timer starts again |

The on and off levels are separate, so you can leave a gap between them, e.g. on above 70 %, off below 60 %. A single stray low reading would otherwise stop the vent and the next normal reading would start it again, so *Stay below for* (default 3 min) holds the vent on until the reading has settled below the off level; a reading back at or above it restarts the hold. After a Home Assistant restart or an automation reload, a vent that was left running gets a fresh max-runtime timer, so it never runs indefinitely. If the timer stops the vent while humidity is still high, it stays off until humidity drops and rises above the on level again, or until you press the button.

**Button:** The button is configured as a trigger. Click **Add trigger → Device**, pick your IKEA/Aqara/Hue button and choose the gesture, e.g. *double press*. You can add several gestures. Any trigger type works: ZHA and Zigbee2MQTT device triggers, event entities, or a Shelly input.

#### Install

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fmatkovsky%2Fhome-assistant%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fbathroom_vent_control.yaml)

Or import this URL via **Settings → Automations & Scenes → Blueprints → Import Blueprint**:

```
https://github.com/matkovsky/home-assistant/blob/main/blueprints/automation/bathroom_vent_control.yaml
```

#### Changelog

- **1.1**: *Stay below for* hold on the off level, so stray low humidity readings no longer switch the vent off and on repeatedly.
- **1.0**: Humidity on/off levels, max runtime, button toggle via trigger selector, optional limits for externally started runs, resume after restart/reload.
