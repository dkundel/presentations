footer: Dominik Kundel | [@dkundel](https://twitter.com/dkundel) | #jsunconf #coffeejs #htcpcp
slidenumbers: false
build-lists: true
autoscale: true

# ☕️.js
## How I hacked my Coffee Machine

---

# [fit] HI!
# [fit] I'm Dominik!

![right 75%](../images/me-avatar.png)

---

## About me
 
![right 75%](../images/me-neutral.png)

Developer Evangelist at

![inline 40%](../images/Twilio_logo_red.png)

Get in touch with me!

[@dkundel](https://twitter.com/dkundel)
[dkundel@twilio.com](mailto:dkundel@twilio.com)
[github/dkundel](https://github.com/dkundel)

^ I'm a developer evangelist for a company called Twilio

^ Twilio is a Cloud Communication platform that allows you to add
voice, SMS, video or similar to your applications

^ Today I'm talking about nothing of that. Instead I'll be talking about...

---

![](images/barista-coffee-machine.jpg)

## How I hacked my ☕️ Machine

---

![](images/barista-coffee-machine.jpg)

![inline](images/original-latissima.jpg)

---

![](images/barista-coffee-machine.jpg)

![inline](images/original-latissima.jpg)![inline](images/closed-latissima-vertical.jpg)

---

# Why?

---

# 1. It's interesting

---

# 2. _"Alexa, make me a coffee"_

---

# Why __JS__?

---

## Why __JS__?

1. The last time I wrote C++ was in university
2. I know JavaScript well which makes coding faster
3. Writing a web server is easy
4. It's one more challenge
5. Hardware === eventDriven && __JS__ === eventDriven

---

# Options?

---

## Option 1: __Espruino__

![inline 100%](images/espruino.png)

### [`www.espruino.com`](https://www.espruino.com/)

---

## Option 1: __Espruino__

![inline](images/espruino-web-ide.png)![inline](images/Pico_angled.jpg)

---

## Option 2: __Tessel__

![inline 200%](images/tessel.png)

### [`tessel.io`](https://tessel.io/)

---

## Option 2: __Tessel__

![inline](images/tessel-website.png)![inline](images/tessel-photo.jpg)

---

![inline 40%](images/nodejs-icon.png)

---

## Option 3: __Johnny-Five__

![inline](images/johnny-five.png)

### [`johnny-five.io`](http://johnny-five.io/)

---

## Option 3: __Johnny-Five__

![inline](images/johnny-five-npmjs.png)![inline](images/johnny-five-platforms.png)

---

![inline 40%](images/nodejs-icon.png)

---

![inline](images/johnny-five.png)![inline 20%](images/plus.png)![inline](images/tessel-photo.jpg)

---

# How?

---

## Disclaimer

![inline 120%](images/engineering.gif)

---

![inline](images/open-latissima.jpg)

---

![inline](images/open-latissima-annotated-1.png)

---

![inline](images/open-latissima-annotated-2.png)

---

![inline](images/felix-taking-it-apart.jpg)

---

![inline](images/control-plate.jpg)

---

![inline](images/wireup.jpg)

---

![inline](images/notes-3.jpg)

---

![inline](images/notes-3.jpg)![inline](images/notes-2.jpg)

---

![inline](images/notes-3.jpg)![inline](images/notes-2.jpg)![inline](images/notes-1.jpg)

---

![right fit](images/control-plate.jpg)

## What We Knew
- 8 cables/pins
- 6 switches
- 7 LEDs

---

![right fit](images/control-plate.jpg)

## Assumptions

1. One cable is ⚡️
2. We need at least 3 pins + power controlling the 🖲
3. We need at least 3 pins + power controlling the 💡
4. What is the 8th cable for?

---

![right fit](images/control-plate.jpg)

## The Naive Approach

```js
var five = require('johnny-five');
var Tessel = require('tessel-io');
var board = new five.Board({
  io: new Tessel()
});
board.on('ready', function () {
  var p1 = new five.Pin({ 
    pin: 'b0',
    mode: 2 
  });
  //...
  var p8 = new five.Pin({ 
    pin: 'b7', 
    mode: 2 
  });
  var pins = [p1, /* ... */, p8];
  board.repl.inject({ pins: pins });
});
```

---

![right fit](images/control-plate.jpg)

## The Naive Approach II

```js
const five = require('johnny-five');
const Tessel = require('tessel-io');
const board = new five.Board({
  io: new Tessel()
});
board.on('ready', () => {
  const p2 = new five.Pin({ 
    pin: 'b1' /*,
    mode: 2 */
  });
  //...
  const p8 = new five.Pin({ 
    pin: 'b7'/*, 
    mode: 2 */
  });
  const pins = [p2, /* ... */, p8];
  board.repl.inject({ pins: pins });
});
```

---

## Buttons? How About Buttons!

```js
board.on('ready', () => {
  // PIN declaration ...
  const b2 = new five.Button('b1');
  const b3 = new five.Button('b2');
  const b7 = new five.Button('b6');
  const b8 = new five.Button('b7');
  const buttons = [b2, b3, b7, b8];
  buttons.forEach(btn => {
    btn.on('press', () => console.log('Pressed button no.%d', btn.pin));
    btn.on('release', () => console.log('Released button no.%d', btn.pin));
  })
})
```

---

![right fit](images/control-plate.jpg)

## We Are On To Something 🕵

- Pin 1 is ⚡️ (turns on LED4, LED5, LED7)
- Pin 2 can manipulate 🖲 S3 && 🖲 S4
- Pin 2 can manipulate 🖲 S1 && 🖲 S2
- Pin 4-6 can turn off 💡 LED4, 💡 LED5, 💡 LED7
- Pin 7-8 react on 🖲

---

![](images/paper-ball-wallpaper.jpg)

## Back To The Drawing Board

---

![inline](images/control-plate.jpg)

---

![inline 80%](images/Fritzing_logo.png)

# To The Rescue!

---

![inline](images/control-plate.jpg)

---

![fit](images/original-schema.png)

---

![fit right](images/original-schema.png)

## 🎉 Progress! 🎉

### Pin 1-3 === ⚡️
### Pin 4-6 === 💡
### Pin 7-8 === 🖲???

---

![right fit](images/control-plate-diodes.png)

# Diodes!

---

![fit](images/schema-switches.png)

---

![right fit](images/control-plate.jpg)

# It All Makes Sense!

🖲 S1 = P3 ➡ P7
🖲 S2 = P3 ➡ P8
🖲 S3 = P2 ➡ P7
🖲 S4 = P2 ➡ P8
🖲 S5 = P1 ➡ P7
🖲 S6 = P1 ➡ P8
💡 LED7 (Power) = P1 ➡ P6

---

![fit 170%](images/setup_bb.png)

---

![fit](images/first-wire-up.jpg)

---

```js
const espresso = new five.Relay({
  pin: 'a4',
  type: 'NO'
});
const grande = new five.Relay({
  pin: 'a5',
  type: 'NO'
});
const power = new five.Relay({
  pin: 'a6',
  type: 'NO'
});
espresso.close();
grande.close();
power.close();
board.repl.inject({ espresso, grande, power });
```

---

![fit autoplay loop mute](images/first-working-code.mov)

---

![](images/closed-latissima-horizontal.jpg)

---

# __I__oT Coffee Machine?

---

# `418 - I'm a teapot`

---

## IETF RFC 2324
# `HTCPCP`

---

#[fit] `Hyper Text Coffee Pot Control Protocol`

---

#[fit] `Hyper Text Coffee Pot Control Protocol`

- Built on top of `HTTP`
- Adds `BREW` method
- `Safe` and `Accept-Additions`
- `HTTP` code `418`
- `coffee://` URI scheme
- many more things

---

# 🎉 It's Alive!! 🎉

---

# ☕️ DEMO ☕️

---

# What I learned:

---

# Reverse-engineering is a lot of fun

---

# Reverse-engineering can be frustrating

---

# __JS__ ❤️ Hardware

---

# Tessel 2 is a great!

---

![](images/johnny-five.png)

# Johnny-Five is super useful!

---

# What's Next?

- Programmatically determine state
- Add more relays
- Alexa integration
- Suggestions???

---

![right 150%](images/thank-you.gif)

## Any Questions?
## Get in touch with me!

🍺 Dominik Kundel
🐦 [@dkundel](https://twitter.com/dkundel) 
📧 [dkundel@twilio.com](mailto:dkundel@twilio.com)
💻 [github/dkundel](https://github.com/dkundel)

![inline 40%](../images/Twilio_logo_red.png)
---