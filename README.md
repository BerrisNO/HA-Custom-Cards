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

```
type: custom:button-card
entity: sensor.ams_7494_p #Entity of your power consuimtion
show_name: true 
name: Active Import 
icon: mdi:transmission-tower
show_icon: true
show_state: true
aspect_ratio: 1/1 
# state_display is used to style the atributes with its own font size of 0.6em.
# If you want, you can remove the shole state_display and use the normal states.
state_display: >-
  [[[ return `${entity.state} <span style='font-size:0.6em
  '>${entity.attributes.unit_of_measurement}</span>` ]]] 
styles:
  card:
    - border-radius: 25%
  state:
    - font-size: 15px
  name:
    - font-size: 10px
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
      var bar_thickness = 2; // you can change the thickness of the bar here
      var state = entity.state; // fetches the entity.state from line 2
      var percentage = (state / 5000) * 100; // this code is only ment for finding the percentage of state, to change the color.
      var threshold = 5000 // threshold is used for setting a max width of the bar, you can set a own sensor here if needed. 
      if (percentage >= 90) var color = 'red';
      else if (percentage >= 70) var color = 'orange';
      else if (percentage >= 50) var color = 'yellow';
      else if (percentage >= 30) var color = 'green';
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

```


# Auto Entity with procentage bar (replaced the tibber like card)

![screenshot](https://github.com/BerrisNO/HA-Custom-Cards/blob/main/Skjermbilde%202024-07-20%20113954.png)

This was mainly made for myself, to display what room has the most power consumtion. 
It looks for the label "room", and returns all entities i have given the label "room". 



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
                const bar_thickness = 2; // thickness of the border
                var state = entity.state; 
                const percentage = (state / states['sensor.ams_7494_p'].state) * 100; // I used my total power consumtion sensor
                const threshold = states['sensor.ams_7494_p'].state; // Set a threshold for the progress bar, you can set it manually or use a sensor like i did. 
                if (percentage >= 90) var color = 'red';
                else if (percentage >= 70) var color = 'orange';
                else if (percentage >= 50) var color = 'orange';
                else if (percentage >= 30) var color = 'lightgreen';
                else var color = 'lightgreen';
                var totalLength = 341;
                var progress = (1 - state / threshold) * totalLength;

                const svg = `
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
