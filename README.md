# ha_gui

# Dependencies
The following modules are used by my GUI:

## JS Modules
**Mandatory**
* button-card/button-card.js
* lovelace-card-mod/card-mod.js

**Optional**
* apexcharts-card/apexcharts-card.js
* bar-card/bar-card.js
* group-card/group-card.js
* ha-card-weather-conditions/ha-card-weather-conditions.js
* light-entity-card/light-entity-card.js
* light-popup-card/light-popup-card.js
* numberbox-card/numberbox-card.js

**I'm not sure**
* lovelace-card-tools/card-tools.js

##Theme
Most styling aspects needed for tiles and overall appearance on mobile devices (iPhones) are implemented using my theme (see `themes/mxm_theme/`)
To make this theme permament I run it as a `backend prefered theme`. To achieve that following automation has to be run on HA startup:
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

Each page consist following structure:
* custom:mod-card - it will wrap vertical-stack to take control over its padding
* vertical-stack (native) - allows dividing UI into vertical sections (like light switches, window statuses etc) 
* grid (native) - creates grid with 3 columns and an infinity number of rows.
* tiles are based on button-card template(s), which can be configured in different way, passing entity to them, extending with other templates etc



Example:
![image](https://github.com/michalk-k/ha_gui/assets/7868445/4f9ecd36-a5a6-4fa0-9ea6-1d48319eae1b)


   
