# HA-Custom-Cards
My custom cards for Home Assistant.

All cards are extentions of a already exisitng theme, [MiniHass MKII](https://github.com/fredrikpersson92/minihass/tree/main)

# Custom Vehicle Card

16.01.2024, Update: 
0.1 - Green pulse effect added when vehicle is charging
0.2 - Updated defrosting and a/c icons so they react correctly with the state. 

![Custom_vehicle](https://github.com/BerrisNO/HA-Custom-Cards/blob/main/custom_card_vehicle/vehicle_card.gif)

Note: You will need the following lovelace resources to make this work. 

* [button-card](https://github.com/custom-cards/button-card)
* [lovelace-card-mod](https://github.com/thomasloven/lovelace-card-mod)

# Tibber like button card

This card was made to look similar to the buttons in the tibber app. 
![tibber](https://github.com/BerrisNO/HA-Custom-Cards/blob/main/tibber_screenshot.png)

<details><summary>YAML code</summary>

```yaml
type: custom:button-card
styles:
  card:
    - padding: 1rem
    - background: |
        [[[
	  // Change the below 3 sensors with your state, min and max sensor. 

          const currentPrice = parseFloat(states['sensor.elctricity_price'].state);
          const minPrice = parseFloat(states['sensor.elctricity_price'].attributes.min);
          const maxPrice = parseFloat(states['sensor.elctricity_price'].attributes.max);
          
          // Calculate the percentage of the current price between min and max price
          const percentage = (currentPrice - minPrice) / (maxPrice - minPrice) * 100;
          
          // Calculate the degrees for the conic-gradient
          const degrees = Math.min((percentage / 100) * 360);
          
          // Determine the color based on the percentage
          let color;
          if (percentage <= 33) { 
            color = 'lime';
          } else if (percentage <= 80) {
            color = 'orange';
          } else {
            color = 'red';
          }
          
          // Return the conic-gradient CSS value
          return `conic-gradient(${color} ${degrees}deg, black 0deg)`;
        ]]]
    - aspect-ratio: 2
    - border-radius: var(--border-radius)
  custom_fields:
    test:
      - width: calc(100% - 0.4rem)
      - height: calc(100% - 0.4rem)
      - left: 0.22rem
      - background: var(--contrast-0)
      - position: absolute
      - border-radius: 1rem
custom_fields:
  test:
    card:
      type: custom:button-card
      entity: sensor.nordpool_kwh_krsand_nok_3_1000_025
      show_state: false
      show_label: true
      show_icon: false
      label: >
        [[[ return states['sensor.electricity_price'].state +
        '<span style="font-size:0.5em "> øre</span>' ]]]
      styles:
        grid:
          - grid-template-areas: '"l l" "n n"'
          - grid-template-rows: 1fr 1fr
          - grid-template-columns: 1fr 1fr
        card:
          - background: none
        label:
          - padding-top: 10%
          - text-align: center
          - font-size: var(--fs-600)
          - font-family: Montserrat
          - font-weight: 600
          - color: var(--contrast-100)
          - overflow: visible
        name:
          - text-align: center
          - font-size: var(--fs-400)
          - font-family: Montserrat
          - font-weight: 500
          - justify-self: top
          - align-self: start
          - color: var(--contrast-100)
          - overflow: visible

```
</details>
