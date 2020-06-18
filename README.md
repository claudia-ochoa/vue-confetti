# vue-confetti
A conversion from dom-confetti (https://github.com/daniel-lundin/dom-confetti) from React to a Vue component

# Usage
Put the `ConfettiCanon.vue` in your components folder and import it as usual. 
Then wherever you import the file position the pixel wherever you want it. 
You can pass a config options if you want.

## Example
import and cofigure the confetti canon.
```
import ConfettiCanon from '@/components/ConfettiCanon.vue'

export default {
  name: 'YourComponent',
  components: {
    ConfettiCanon
  },
  data () {
    return {
      confettiConfig: {
        angle: 90,
        spread: 100,
        startVelocity: 50,
        elementCount: 80,
        dragFriction: 0.11,
        duration: 10000,
        stagger: 26,
        width: '15px',
        height: '15px',
        colors: ['#a864fd', '#29cdff', '#78ff44', '#ff718d', '#fdff6a']
      }
    }
  }
 }
```
For more options and the explainations of all settings check the official documentation [here](https://github.com/daniel-lundin/dom-confetti#interface).

Then include that it with the config (if you have a config) in the `<template>`.
```html
<confetti-canon :config="confettiConfig" />
```

## Credits
This file was converted from [dom confetti](https://github.com/daniel-lundin/dom-confetti) and all credit goes to [Daniel Lundin](https://github.com/daniel-lundin).

All I did was convert them from one to the other.
