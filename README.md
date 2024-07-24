# HA-Custom-Cards
My custom cards for Home Assistant.

All cards are extentions of a already exisitng theme, [MiniHass MKII](https://github.com/fredrikpersson92/minihass/tree/main)

<strong>Important:</strong> If you are not using the above mentioned theme, you will need to change all the colors manually for everything to work. 


# Custom Vehicle Card

16.01.2024, Update: 
0.1 - Green pulse effect added when vehicle is charging
0.2 - Updated defrosting and a/c icons so they react correctly with the state. 

![Custom_vehicle](https://github.com/BerrisNO/HA-Custom-Cards/blob/main/custom_card_vehicle/vehicle_card.gif)

Note: You will need the following lovelace resources to make this work. 

* [button-card](https://github.com/custom-cards/button-card)
* [lovelace-card-mod](https://github.com/thomasloven/lovelace-card-mod)

# Square bar card (Power consumption). 
This card will give you a graphical representation of how much total power you are using. 
It can be used for many other things, but this code is spesifically made for power consumption. 

![Square_bar_card](https://github.com/BerrisNO/images/blob/main/Skjermbilde%202024-07-20%20142558.png)

<strong>Note:</strong>  This card has to be in a square for it to work, otherwise the progress bar will be broken.

<strong>Updates:</strong>
<strong>24.07.2024</strong> Added variables in the top of the card, to minimise the need to make changes to the whole card. 

<details><summary>YAML code</summary>

```yaml
type: custom:button-card
entity: sensor.power_stue #dd your main sensor
variables:
  card_radius: null # Default 25% (does not change the radius of the bar, only the card.
  icon_height: null # Default 120 % 
  icon_width: null # Default 120 %
  sensor_threshold: sensor.ams_7494_p # Add your threshold sensor or replace with a set number.
  bar_thickness: null # Default: 3
  bar_bg_thickness: null # Default: 3
  bar_bg_opacity: null # Default: 0.5
  bar_bg_color: null # Default: grat
show_name: true
show_icon: true
show_state: true
name: Active Import
icon: mdi:transmission-tower
aspect_ratio: 1/1
state_display: >-
  [[[ return `${entity.state} <span style='font-size:0.6em
  '>${entity.attributes.unit_of_measurement}</span>` ]]]
styles:
  card:
    - border-radius: '[[[ return `${variables.card_radius ?? 25}%` ]]]'
  state:
    - font-size: 150%
    - font-family: Montserrat
    - font-weight: 500
    - overflow: visible
    - align-self: start
    - text-align: start
    - padding-bottom: 15%
  img_cell:
    - height: '[[[ return `${variables.icon_height ?? 120}%` ]]]'
    - height: '[[[ return `${variables.icon_width ?? 120}%` ]]]'
    - align-self: center
  icon:
    - justify-self: start
    - align-self: start
    - overflow: visible
    - color: var(--color-gold)
  name:
    - font-family: Montserrat
    - font-weight: 500
    - color: var(--color-dark-gray)
    - align-self: end
    - text-align: end
  custom_fields:
    progress:
      - position: absolute
      - width: 100%
      - height: 100%
      - display: flex
      - align-items: center
      - justify-content: center
      - overflow: visible
custom_fields:
  progress: |
    [[[
      var state = entity.state;
      var threshold = states[variables.sensor_threshold].state
      var percentage = (state / states[variables.sensor_threshold].state) * 100
      if (percentage >= 90) var color = 'red';
      else if (percentage >= 70) var color = 'orange';
      else if (percentage >= 50) var color = 'yellow';
      else if (percentage >= 30) var color = 'lightgreen';
      else var color = 'lightgreen';
      var totalLength = 341;
      var progress = (1 - state / threshold) * totalLength;

      var svg = `
        <svg id="progress-bar" fill="none"  viewBox="0 0 100 100";>
          <path id="background-path" d="M25,2.5
                h50 a22.5,22.5 0 0 1 22.5,22.5   v50 a22.5,22.5 0 0 1 -22.5,22.5
                h-50 a22.5,22.5 0 0 1 -22.5,-22.5   v-50 a22.5,22.5 0 0 1 22.5,-22.5" />
          <path id="progress-path" d="M25,2.5
                h50 a22.5,22.5 0 0 1 22.5,22.5   v50 a22.5,22.5 0 0 1 -22.5,22.5
                h-50 a22.5,22.5 0 0 1 -22.5,-22.5   v-50 a22.5,22.5 0 0 1 22.5,-22.5" />
        </svg>
        <style>
          #background-path {
            opacity: ${variables.bar_bg_opacity ?? 0.2};
            stroke: ${variables.bar_bg_color ?? "gray"};
            stroke-width: ${variables.bar_bg_thickness ?? 1};
          }
          #progress-path {
            stroke: ${color};
            stroke-width: ${variables.bar_thickness ?? 2};
            stroke-dasharray: ${totalLength};
            stroke-dashoffset: ${progress};
            transition: stroke-dashoffset 1s linear;
          }
        </style>
      `;
      return svg;
    ]]]
```
</details>

# Square bar card (With auto entities)

![screenshot](https://github.com/BerrisNO/HA-Custom-Cards/blob/main/Skjermbilde%202024-07-20%20113954.png)

This card displays all entities labeled as "room" in the square bar card. 
It calculates the percentage of total usage for each entity using a total active import sensor. 
The rooms are then organized and positioned within a grid, with the room having the highest consumption placed in the upper left corner.



<details><summary>YAML code</summary>

```yaml
- type: custom:auto-entities
  card:
    type: grid
    columns: 4
  card_param: cards
  filter:
    include:
      - label:  room
        options:
          type: custom:button-card
          entity_id: this.entity_id
          state_display: >-
              [[[ return `${entity.state} <span style='font-size:0.6em
              '>${entity.attributes.unit_of_measurement}</span>` ]]]
          layout: vertical
          show_last_changed: false
          show_state: true
          aspect_ratio: 1/1
          styles:
            card:
              
              - border-radius: 25%
            state:
              - align-self: start
              - padding: 5px
              - font-size: 13px
              - font-family: Montserrat
              - font-weight: 500
              - overflow: visible
            img_cell:
              - overflow: visible
            icon:
              - justify-self: start
              - align-self: start
              - overflow: visible
              - color: var(--color-gold)
            name:
              - font-size: var(--fs-300)
              - font-family: Montserrat
              - font-weight: 500
              - color: var(--color-dark-gray)
              - overflow: hidden
            custom_fields:
              progress:
                - position: absolute
                - width: 100%
                - height: 100%
                - display: flex
                - align-items: center
                - justify-content: center
                - overflow: visible
          custom_fields:
            progress: |
              [[[
                var bar_thickness = 2; // thickness of the border
                var state = entity.state; 
                var percentage = (state / states['sensor.ams_7494_p'].state) * 100; // I used my total power consumtion sensor
                var threshold = states['sensor.ams_7494_p'].state; // Set a threshold for the progress bar, you can set it manually or use a sensor like i did. 
                if (percentage >= 90) var color = 'red';
                else if (percentage >= 70) var color = 'orange';
                else if (percentage >= 50) var color = 'orange';
                else if (percentage >= 30) var color = 'lightgreen';
                else var color = 'lightgreen';
                var totalLength = 341;
                var progress = (1 - state / threshold) * totalLength;

                var svg = `
                  <svg id="progress-bar" fill="none" stroke-linecap="round" viewBox="0 0 100 100">
                    <path id="progress-path" d="M25,2.5
                      h50 a22.5,22.5 0 0 1 22.5,22.5   v50 a22.5,22.5 0 0 1 -22.5,22.5
                      h-50 a22.5,22.5 0 0 1 -22.5,-22.5   v-50 a22.5,22.5 0 0 1 22.5,-22.5" />

                  </svg>
                  <style>
                    #progress-path {
                      stroke: ${color};
                      stroke-width: ${bar_thickness};
                      stroke-dasharray: ${totalLength};
                      stroke-dashoffset: ${progress};
                      transition: stroke-dashoffset 1s linear;
                    }
                  </style>
                `;
                return svg;
              ]]]
  sort:
    method: state
    numeric: true
    reverse: true
    count: 8
```
</details>

# Circle bar card (Power consumption). 
The above card just with a 50% radius, making it a circle

![Circle_bar_card](https://github.com/BerrisNO/images/blob/main/circle.PNG)

<strong>Note:</strong>  This card has to be in a square / aspect_ratio: 1/1 for it to work, otherwise the progress bar will be broken.

<details><summary>YAML code</summary>

```yaml
type: custom:button-card
entity: sensor.power_stue
variables:
  card_radius: 50%
  icon_height: 150
  icon_width: 150
  sensor_threshold: sensor.ams_7494_p # Add your threshold sensor or replace with a set number.
  bar_thickness: null # Default: 3
  bar_bg_thickness: null # Default: 3
  bar_bg_opacity: null # Default: 0.5
  bar_bg_color: null # Default: grat
show_name: true
show_icon: true
show_state: true
name: Active Import
icon: mdi:transmission-tower
aspect_ratio: 1/1
state_display: >-
  [[[ return `${entity.state} <span style='font-size:0.6em
  '>${entity.attributes.unit_of_measurement}</span>` ]]]
styles:
  card:
    - border-radius: '[[[ return variables.card_radius ]]]'
  state:
    - font-size: 150%
    - font-family: Montserrat
    - font-weight: 500
    - overflow: visible
    - align-self: start
    - text-align: start
    - padding-bottom: 15%
  img_cell:
    - height: '[[[ return `${variables.icon_height ?? 120}` ]]]'
    - height: '[[[ return `${variables.icon_width ?? 120}` ]]]'
    - align-self: center
  icon:
    - justify-self: start
    - align-self: start
    - overflow: visible
    - color: var(--color-gold)
  name:
    - font-family: Montserrat
    - font-weight: 500
    - color: var(--color-dark-gray)
    - align-self: end
    - text-align: end
  custom_fields:
    progress:
      - position: absolute
      - width: 100%
      - height: 100%
      - display: flex
      - align-items: center
      - justify-content: center
      - overflow: visible
custom_fields:
  progress: |
    [[[
      var state = entity.state;
      var threshold = states[variables.sensor_threshold].state
      var percentage = (state / states[variables.sensor_threshold].state) * 100
      if (percentage >= 90) var color = 'red';
      else if (percentage >= 70) var color = 'orange';
      else if (percentage >= 50) var color = 'yellow';
      else if (percentage >= 30) var color = 'lightgreen';
      else var color = 'lightgreen';
      var totalLength = 315;
      var progress = (1 - state / threshold) * totalLength;

      var svg = `
        <svg id="progress-bar" fill="none"  viewBox="0 0 100 100" width: 100%; height: 100%;>
          <path id="background-path" d="M50,0.2
                h0 a50,50 0 0 1 50,50 v0 a50,50 0 0 1 -50,50
                h0 a50,50 0 0 1 -50,-50 v0 a50,50 0 0 1 50,-50" />
          <path id="progress-path" d="M50,0.2
                h0 a50,50 0 0 1 50,50 v0 a50,50 0 0 1 -50,50
                h0 a50,50 0 0 1 -50,-50 v0 a50,50 0 0 1 50,-50" />
        </svg>
        <style>
          #background-path {
            opacity: ${variables.bar_bg_opacity ?? 0.2};
            stroke: ${variables.bar_bg_color ?? "gray"};
            stroke-width: ${variables.bar_bg_thickness ?? 3};
          }
          #progress-path {
            stroke: ${color};
            stroke-width: ${variables.bar_thickness ?? 3};
            stroke-dasharray: ${totalLength};
            stroke-dashoffset: ${progress};
            transition: stroke-dashoffset 1s linear;
          }
        </style>
      `;
      return svg;
    ]]]

```
</details>


I would like to thank [@dsellers1](https://github.com/dsellers1) for the big support on this Square bar card!
