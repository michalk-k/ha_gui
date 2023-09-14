# ha_gui

# Dependencies
There are the following prerequisites:

## JS Modules
**mandatory**
* custom:button-card
* custom:card-mod
* custom:mod-card
* custom:stack-in-card
* custom:light-popup-card
* custom:light-entity-card
* custom:apexcharts-card

**optional**
Those cards are used by selected pages expicitely like printer status or springler control
* custom:numberbox-card
* custom:bar-card
* custom:auto-entities

**I'm not sure**
* lovelace-card-tools/card-tools.js

## Theme
Most styling aspects needed for tiles and overall appearance on mobile devices (iPhones) are implemented using my theme (see `themes/mxm_theme/`)
To make this theme permanent I run it as a `backend preferred theme`. To achieve that following automation has to be run on HA startup:
```yaml
alias: Set Home Assistant theme at startup
trigger:
  - platform: homeassistant
    event: start
action:
  - service: frontend.set_theme
    data:
      name: mxm_theme
```

# Structure
The UI is build with use of button-card templates exclusively. The dashboard yaml starts with the definition of all needed templates. Those sets all trickery needed to create the UI further with minimal code.

Each page consists of the following structure:
* custom:mod-card - it will wrap vertical-stack to take control over its padding
* vertical-stack (native) - allows dividing UI into vertical sections (like light switches, window statuses etc) 
* grid (native) - creates grid with 3 columns and an infinity number of rows.
* tiles are based on button-card template(s), which can be configured in different way, passing entity to them, extending with other templates etc

# The Tile usage
The tile uses preconfigured templates which can be added to main tail template(s).
Obviously, all of them may be extended by using custom-card features. However, there are some ready-to-use pre-configured features like entity_id:

**Tile templates:** 
* mycustomcard - template of the tile card to be used on main page of UI.
* mycustomcard_action - template representing state of entities which doesn't change on demand (windows, doors)
* mycustomcard_action_toggle - template representing light or other on/off entity/appliance
* customcard_graph_apex - tile with apexchart, preconfigured for showing temperature, target temperature and humidity
* customcard_21wrapper- kind of universal card, used only for weather on the main page

**Tile feature templates:**
* lightpopup_dim - extends `mycustomcard_action_toggle` for support of dimming light
* lightpopup_rgb - extends `mycustomcard_action_toggle` for support of rgb light
* lightpopup_cct - extends `mycustomcard_action_toggle` for support of rgb light
* subsection_window - template used for styling window state indication inside `mycustomcard`
* subsection_temp - template used for styling temperature indication inside the `mycustomcard`
* subsection_bottom - template used for styling in the bottom-left corner
* subsection_hide_inactive - template hiding the card if an entity is off
* card_hide_unavailable - template hiding the card if an entity is unavailable

## House overview:
<img width="50%" src="https://github.com/michalk-k/ha_gui/assets/7868445/4d6e0ffb-3466-4994-b0e3-9021a1d5dc6c" alt="House overview" />

Weather uses HA native weather-forecast card.
Tiles represent rooms or their groups (ie whole house).\
Click on the tile opens page for the room.\
Hold on tile turns off all lights in the room.

You may find that tiles on this page are divided into 5 areas:
* name of the room
* top-left - icon indicating state of all lights in the room. The icon and state is determined by entity assigned to the card. The entity is obviously a `light-group`
* top-right - icon indicating state of all opened windows/doors in the room. It requires embedding another card to show something. In our case, it's another custom-button card, templated with `subsection_window`
* bottom-left - list of extra features in the room (ie enabled printing, rain, tv etc). Only enabled features are shown, otherwise are hidden. It requires embedding of horizontal-stack (HA native) with custom-button cards templated with `subsection_bottom` as well as `subsection_hide_inactive` and `card_hide_unavailable` for hiding inactive indicators
* bottom-right - temperature of the room. It requires embedding another card to show something. In our case, it's another custom-button card, templated with `subsection_temp`

## Room view
<img width="50%" src="https://github.com/michalk-k/ha_gui/assets/7868445/c3418206-8434-4012-b1ea-333779a4e3ac" alt="Single room" />

Top of the view is reserved for a temperature and humidity glance. It uses apex-chart card embedded with the use of `customcard_graph_apex` template.
Other cards have the functionality of light switches or indicate a state of windows/doors. 

**Lights/switches**
To create a tile with the ability to turn on/off, `mycustomcard_action_toggle` template has to be used.
RGB, CCT, and dimmable lights provide a popup with extended control, enabled by pressing and holding the tile. That functionality is enabled by using additional templates:  `lightpopup_rgb`, `'lightpopup_cct`, or `lightpopup_dim`

**Windows**
My house is equipped with tilting windows. I use Shelly sensors which provide tilt angle information next to open/close. Using this information GUI provides a graphical cue about window state: either opened, closed or tilted.

Example:
<img width="50%" src="https://github.com/michalk-k/ha_gui/assets/7868445/4f9ecd36-a5a6-4fa0-9ea6-1d48319eae1b" alt="Fragment of code" />

   
