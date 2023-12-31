# Universal Game Controller

![npm](https://img.shields.io/npm/v/universal-game-controller)
![npm bundle size](https://img.shields.io/bundlephobia/min/universal-game-controller)
![NPM](https://img.shields.io/npm/l/universal-game-controller)

Simple game input for 💻 keyboard, 📱 touchscreen and 🎮 gamepad through a unified API. Made for game jams.

### Why?
- You’ll need keyboard support for maximum game development speed and compatibility.
- However, actual gameplay will be more fun and impressive with a game controller. Only if you had the time...
- Touchscreen support will make it 100x easier to show the game to your friends after the game jam.

### How?

Current game inputs are:
- one joystick
- one button

Constraining? That’s where creativity starts! Never underestimate the power of simplicity – easy to learn is easy to love.

Demo: https://ugc.bloat.app

See importing instructions below.

## Design goals
- Fun and practical to use in game jams.
- Games are equally playable on laptop (for development), TV (for immersion) and phone (for sharing).
- Easy to use for new players (on all platforms).
- Fun even at high skill levels (on all platforms).
- Light-weight and zero dependencies.

## Features

All user inputs are provided as a state of the controller – no events as of now. These are meant to be read in the game update loop.

### Joystick
`controller.move`: `{ x, y }`
- Output is a 2D vector that is guaranteed to be inside the unit circle (even if multiple controllers are used at once).
- Gamepad: Analog left joystick (with a dead zone for drift compensation).
- Keyboard: WASD (with diagonal length compensation).
- Touchscreen: The first touch (anywhere) can be slided around as a virtual joystick. Scrolling the page is disabled (also on Safari).
- Should be exactly (0, 0) when the user is not touching the controls – however, this is not guaranteed for gamepad, since the neutral position might vary from device to device, and there is no specification for maximum allowed drift.

### Trigger button
`controller.trigger`: boolean
- Gamepad: Button 1 (Xbox: A, Switch: B)
- Keyboard: Space. Scrolling is prevented.
- Touchscreen: Second touch (anywhere). The button is sustained even if the first touch ends.

### Controller type
`controller.type`: `InputType`
- The game can display instructions for the specific input device that the player is using.
- If a gamepad is connected, type is always gamepad.
- Otherwise touchscreen and keyboard will switch to the one that had the last input event.
- On page load before any user actions, type is touchscreen [if a touchscreen is present](https://hacks.mozilla.org/2013/04/detecting-touch-its-the-why-not-the-how/).

## Importing

### URL

```html
<script src="https://cdn.jsdelivr.net/npm/universal-game-controller@1.3.2/dist/main.js"></script>
```

```javascript
const { controller } = UniversalGameController
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
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="https://cdn.jsdelivr.net/npm/universal-game-controller@1.3.2/dist/main.js"></script>
    </head>
    <body>
        <div id="instructions" style="font-family: sans-serif; width: 100%; text-align: center; font-size: 2em; padding-top: 0.3em;"></div>
        <div id="container" style="border: 2vmin solid rgb(15, 31, 46); width: 80vmin; height: 80vmin; border-radius: 50%; position: absolute; left: calc(50% - 40vmin); top: calc(50% - 40vmin); display: flex; justify-content: center; align-items: center;">
            <div id="output" style="border: 2vmin solid rgb(15, 31, 46); width: 40vmin; height: 40vmin; border-radius: 50%; transform: translate(0vmin); background-color: rgb(15, 31, 46); box-sizing: border-box;"></div>
        </div>
        <script>
            const { controller, InputType } = UniversalGameController;
            const output = document.querySelector('#output');
            const instructions = document.querySelector('#instructions');

            const gameLoop = () => {
                const move = controller.move;
                output.style.transform = `translate(${move.x * 25}vmin, ${move.y * 25}vmin)`;

                if (controller.trigger) {
                    output.style.width = '60vmin';
                    output.style.height = '60vmin';
                } else {
                    output.style.width = '30vmin';
                    output.style.height = '30vmin';
                }

                if (controller.type === InputType.KEYBOARD) {
                    instructions.innerText = 'Use WASD to move and space to trigger';
                } else if (controller.type === InputType.TOUCH) {
                    instructions.innerText = 'Use one finger to move and another to trigger';
                } else if (controller.type === InputType.GAMEPAD) {
                    instructions.innerText = 'Use the left stick to move and button 1 to trigger';
                }

                requestAnimationFrame(gameLoop);
            }
            gameLoop();
        </script>
    </body>
</html>
```

## Ideas
- Configurations for gamepad joystick dead zone and touchscreen virtual joystick size.
- Provide the current touch coordinates for displaying virtual joysticks.
- Capturing touch input only on specific DOM elements.
- Twin-stick.
- Events for trigger button state change.
- Mouse support (because WASD has only 8 directions and this can be too limiting in top-down shooting games).
- Optional joystick smoothing (to make it easier to move only a tiny bit with WASD).
- More buttons with game-defined graphics for touchscreen version (how to hide them when touch screen is not present?)

## Non-goals
- Anything that displays graphics (e.g. virtual joysticks).
- Anything that requires user action to enable (e.g. motion sensor).
- Local multiplayer. Well, maybe. Multiplayer is fun, but this gets a bit complicated to handle in a generic way. The main point of this library is to provide an opinionated mapping between different input types, and there more trade-offs with 2-player control mapping possibilities.
- Exposing all features of each input method. If you need more, just use the vanilla APIs – they are not *that* complicated.
- XR / 3D input.