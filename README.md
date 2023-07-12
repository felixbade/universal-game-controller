# Universal Game Controller

![npm](https://img.shields.io/npm/v/universal-game-controller)
![npm bundle size](https://img.shields.io/bundlephobia/min/universal-game-controller)
![NPM](https://img.shields.io/npm/l/universal-game-controller)

Simple game input for 📱 touchscreen, 💻 keyboard and 🎮 gamepad through a unified API.

Demo: https://ugc.bloat.app

## Design goals
- Same code works with multiple input devices.
- The player does not need to configure which input method they are using. All of them just work, all the time.
- Easy to explain controls to new players.
- Robust, high performance controls – works for competitive games.
- No input method may produce advantageous output compared to the others – for example WASD diagonal vector is not longer than analog joystick diagonal.
- Batteries included – for example joystick deadzone is handled automatically.
- Light-weight and zero dependencies.

## Features

All user inputs are provided as a state of the controller – no events. These are meant to be read in the game update loop.

### Joystick
- Output is 2D vector that is guaranteed to be inside the unit circle
- Should be exactly (0, 0) when the user is not touching the controls – however, this is not guaranteed for gamepad, and might vary from device to device.
- Gamepad: Analog joystick with a dead zone for drift compensation
- Keyboard: WASD with diagonal length compensation
- Touchscreen: The first touch (anywhere) can be slided around as a virtual joystick. Scrolling the page is disabled.

### Trigger button
- Output is a boolean
- Gamepad: Button 1
- Keyboard: Space. Scrolling is prevented.
- Touchscreen: Second touch (anywhere). The button is sustained even if the first touch ends.

### Controller type (coming soon)
- The game can display instructions for the specific input device that the player is using.
- If gamepad is connected, type is always gamepad.
- Otherwise touchscreen and keyboard will switch to the one that had the last input event.
- On page load, before any user actions, type is touchscreen if [a touchscreen is present](https://hacks.mozilla.org/2013/04/detecting-touch-its-the-why-not-the-how/).

## Importing

### URL

```html
<script src="https://cdn.jsdelivr.net/npm/universal-game-controller@1.1.5/dist/main.js"></script>
```

```javascript
const controller = UniversalGameController.controller
```

### NPM
```sh
npm install universal-game-controller
```

```javascript
import { controller } from 'universal-game-controller'
```

## Example using URL import
```html
<!DOCTYPE html>
<html>
    <head>
        <script src="https://cdn.jsdelivr.net/npm/universal-game-controller@1.1.5/dist/main.js"></script>
    </head>
    <body>
        <div id="output" style="border: solid 2vw hsl(210, 50%, 12%); width: 20vw; height: 20vw; border-radius: 50%; position: absolute; left: calc(50% - 10vw); top: calc(50% - 10vw);"></div>
        <script>
            const controller = UniversalGameController.controller;

            const output = document.querySelector('#output');

            setInterval(() => {
                const move = controller.move;
                output.style.transform = `translate(${move.x * 20}vw, ${move.y * 20}vw)`;

                const trigger = controller.trigger;
                if (controller.trigger) {
                    output.style.backgroundColor = 'white';
                } else {
                    output.style.backgroundColor = 'hsl(210, 50%, 12%)';
                }
            }, 10);
        </script>
    </body>
</html>
```

## Ideas
- Configurations for gamepad joystick dead zone and touchscreen virtual joystick size
- Events for trigger button state change
- Twin-stick
- Mouse support
- Capturing touch input only on specific DOM elements.
- More buttons with game-defined graphics for touchscreen version (how to hide them when touch screen is not present?)

## Non-goals
- Exposing all features of each input method
- Local multiplayer
- Anything that displays graphics (including virtual joysticks)
- XR
- Anything that requires user action to enable (e.g. motion sensor)