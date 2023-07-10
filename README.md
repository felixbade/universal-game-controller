# Universal Game Controller
Simple game input for touch screen, keyboard and gamepad.

## Design goals
- Supports multiple input devices out of the box – platform agnostic API.
- The player does not need to configure which input method they are using. All of them just work, all the time.
- Fun to develop with in game jams.
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

## Installation

```sh
npm install universal-game-controller
```

## Example
```javascript
import { controller } from 'universal-game-controller';

setInterval(() => {
  const move = controller.move;
  console.log(`Move: X:${move.x.toFixed(2)}, Y:${move.y.toFixed(2)}`);

  const trigger = controller.trigger;
  console.log(`Trigger: ${trigger}`);
}, 100);
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