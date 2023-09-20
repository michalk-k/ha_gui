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
The UI is built with tiles made of top-level button-cards, embedding other button-cards. The dashboard yaml starts with the definition of all needed templates. Those sets all trickery needed to create the UI further with minimal code.

Each page consists of the similar structure:
* custom:mod-card - it will wrap vertical-stack to take control over view padding
* vertical-stack (HA native) - allows dividing UI into vertical sections (like light switches, window statuses etc). Not used on main page.
* grid (HA native) - creates grid with 3 columns and an infinite number of rows.
* tiles are based on a highly configurable button-card template(s).

# The Tile
The tile uses preconfigured templates. Mostly any tile has associated one template, affecting it's major functionality, then optionally extended by other templates.
Obviously, all of them may be extended by using custom-card features.

Worth mentioning, tiles that show multiple entities, are built from embedded custom-button cards one into another.

All tiles (except of special ones like weather of temperature) inherit from custom tile which divides its area into a grid: top-left corner, top-right corner, name (middle), state (middle), bottom-left corner, and bottom-right corner. Those sub-areas may be used differently depending on use-case. Main page tiles differ from room tiles by functionality:

**Main page tile**

![image](https://github.com/michalk-k/ha_gui/assets/7868445/a52847a6-a68f-4b2b-9b56-17141a65190e)

Each tile represents an overview of a room or location given by a tile name. It boasts of following features:

* Click on the tile - opens a view of a particular room/location.
* Long Press on the tile - turns off all main lights in the location. Additional lights like night lighting and Christmas tree lights might remain on.
* Upper-left icon - indicates if any of the main lights found in the room/location are turned on.
* Upper-right icon - indicates if any of the windows/doors in the room/location are open or tilted. If at least one window is open, the icon shows an open window. If none are wide open but at least one is tilted, the icon of the tilted window is shown. 
* Bottom-right icon - shows the current temperature of the room/location
* Bottom-left corner - it's a special container to show special features enabled in a particular room/location. It may be printer printing or night lamp, washing machine, Christmas tree etc. Those icons appear only if a particular feature is enabled. Number of icons is not limited, however mobile app provides space for max 3 icons.


**Tile templates:** 
* mycustomcard - template of the tile card to be used on main page of UI.
* mycustomcard_action - template representing state of entities which doesn't change on demand (windows, doors)
* mycustomcard_action_toggle - template representing light or other on/off entity/appliance
* customcard_graph_apex - tile with apexchart, preconfigured for showing temperature, target temperature and humidity
* customcard_21wrapper- kind of universal card, used only for weather on the main page

Anote most of cards accepts provides `entity` attribute to pass entity to be controlled or to display its state

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
Other cards have the functionality of light switches or indicate the state of windows/doors. 

Most cards in the room view have a bit different layout
* name of the entity
* state of the entity
* top-left - icon indicating state of the entity

Some has additional items like up/down controls for roof window, or indicators for type of recycled trash collection.

**Lights/switches**
To create a tile with the ability to turn on/off, `mycustomcard_action_toggle` template has to be used.
RGB, CCT, and dimmable lights provide a popup with extended control, enabled by pressing and holding the tile. That functionality is enabled by using additional templates:  `lightpopup_rgb`, `'lightpopup_cct`, or `lightpopup_dim`

**Windows**
My house is equipped with tilting windows. I use Shelly sensors which provide tilt angle information next to open/close. Using this information GUI provides a graphical cue about window state: either opened, closed or tilted.

Example:
<img width="50%" src="https://github.com/michalk-k/ha_gui/assets/7868445/4f9ecd36-a5a6-4fa0-9ea6-1d48319eae1b" alt="Fragment of code" />

   
