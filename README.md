# Campus Tycoon

**A campus-themed browser board game for 2–4 players, built with vanilla JavaScript.**

[**Play the live game →**](https://campus-tycoon-cici.yz12524.chatgpt.site)

Roll the dice, buy campus properties, collect rent and respond to Chance + Fate cards. The last player who has not gone bankrupt wins.

> Play locally with friends on the same device. Each browser runs its own game; online multiplayer and saved games are not implemented. Refreshing the page starts a new game.

## About the project

Campus Tycoon was developed as a Computing 2 web application coursework project. This personal repository, `imperial-tycoon`, is a fork of the [original coursework submission](https://github.com/Computing-2-Submissions-2025-26/computing-2-submission-Cici-Szx). The in-game title remains **Campus Tycoon**.

The implementation separates game rules from interface rendering so that movement, purchases, cards and turn transitions can be tested without opening a browser.

## What you can play

- **2–4 selectable players**, each starting with £1,000.
- A **25-space campus board** with properties and special locations.
- Two-dice movement and a £100 reward for passing or landing back on Start during normal forward movement.
- Property purchase decisions, rent payments and a £20 rent increase when an owner lands on their own property.
- **40 Chance + Fate cards** with effects such as money changes, movement and extra rolls.
- A **one-card hand limit**, with options to use, keep or replace a drawn card.
- Stage-specific controls, player colours, ownership indicators and a game log.
- Bankruptcy handling and winner detection.

## How to play

1. Open the [live game](https://campus-tycoon-cici.yz12524.chatgpt.site) and choose two, three or four players.
2. Select **Roll Dice**. Your token moves and the destination's rules are applied.
3. If an affordable property is unowned, choose **Buy Property** or **Skip Buying**. Other destinations may charge rent, award money or require a special decision.
4. Follow the end-of-turn card flow: **Draw Card**, then use or keep the card, or replace your existing hand. Replacing a held card immediately uses the old card.
5. During the held-card window, other players can use their saved card on the active player. Select **End Turn** when ready to continue.
6. Continue until only one player remains solvent. **New Game** opens the player-count selector to start again.

Card effects can alter this sequence. The visible controls follow the current game phase.

## Run locally

### Requirements

- Node.js and npm for dependencies, tests and documentation.
- Python 3 for the local static server.
- A modern browser with JavaScript enabled.

### Setup

```bash
git clone https://github.com/Cici-Szx/imperial-tycoon.git
cd imperial-tycoon
npm ci
npm start
```

Open [http://localhost:8001](http://localhost:8001).

`npm start` runs `python3 -m http.server 8001 --directory web-app`. Python serves the files; all game rules execute in the browser. No database, API keys or environment variables are required.

### Useful commands

| Command | Purpose |
| --- | --- |
| `npm start` | Serve the game locally on port 8001 |
| `npm test` | Run the Mocha game-rule tests |
| `npm run docs` | Generate JSDoc documentation in `docs/` |

## Architecture

```text
Player clicks a button
        ↓
main.js receives the event
        ↓
game.js checks the phase and applies the rules
        ↓
main.js stores the returned state
        ↓
render() refreshes the board, players, controls and feedback
```

**State model.** Plain JavaScript objects describe players, the board, the deck, the active player, the current phase and the winner. Update helpers create replacement objects and arrays rather than directly modifying the old state.

**Turn phases.** Phases such as `roll`, `buyDecision`, `drawChoice`, `cardDecision` and `heldCardWindow` determine which actions are available. Rule functions also check their relevant preconditions.

**Interface.** HTML provides the containers, CSS defines the layout and visual style, and JavaScript renders the game using the DOM. No frontend framework is required.

**Testing.** The rule module can be imported directly into Node.js without a DOM. Dice generation accepts an injected random function, and tests can supply specific dice results to exercise scenarios.

## Project structure

| File or directory | Responsibility |
| --- | --- |
| `web-app/index.html` | Page structure, controls and player-count dialog |
| `web-app/default.css` | Board layout, typography, colours, feedback and responsive styles |
| `web-app/main.js` | Event handlers, current state and DOM rendering |
| `web-app/game.js` | Board and card definitions, movement, purchases, rent, turns and victory rules |
| `web-app/tests/game.test.js` | Automated tests of game behaviour |
| `web-app/ramda.js` | Small template placeholder; not imported by the game |
| `web-app/assets/data/` | Template JSON data files; not loaded by the current game |
| `web-app/assets/characters/`, `tiles/`, `ui/` | Reserved asset folders with placeholder files |
| `package.json`, `package-lock.json` | Commands and reproducible development dependencies |
| `jsdoc.json` | Documentation generation configuration |
| `.mocharc.json` | Mocha test discovery configuration |

## Key rule functions

The functions are exported from `web-app/game.js`. The browser also exposes the module as `window.CampusTycoonGame` for inspection.

| Function | Purpose |
| --- | --- |
| `createInitialState(playerNames)` | Initialise a game for 2–4 players |
| `getCurrentPlayer(state)` | Return the active player |
| `rollDice(randomFn)` | Generate two dice and their total |
| `takeTurn(state, diceRoll)` | Process the roll phase, movement and destination |
| `movePlayer(state, playerId, steps)` | Move around the board and handle the Start reward |
| `resolveTile(state, playerId)` | Apply the destination's rules |
| `buyProperty(...)` / `skipBuyProperty(...)` | Resolve a purchase decision and continue the relevant card flow |
| `drawEndCard(state)` | Draw a card when the current phase permits it |
| `useDrawnCard(...)` / `keepDrawnCard(...)` / `replaceHeldCard(...)` | Resolve the drawn-card decision |
| `useHeldCard(...)` | Use a saved card during the permitted window |
| `finishTurn(state)` / `endTurn(state)` | Finish the interaction window and advance turn order |
| `checkWinner(state)` | Return the sole remaining non-bankrupt player, or `null` |
| `getPlayerProperties(...)` / `getPlayerNetWorth(...)` | Read a player's assets |

## Verification

**42 existing Mocha tests passed on 17 September 2026.**

The suite covers:

- Player setup, board size and card definitions.
- Board wrapping and Start rewards.
- Purchase eligibility, payment, ownership and rent.
- Card effects, hand limits, deck recycling and repeat-draw prevention.
- Held-card timing and targeting.
- Bankruptcy, turn order and victory.
- Running the rule functions independently of the DOM.

Run `npm test` to check the current checkout. Passing these tests establishes the tested rule behaviours; it does not establish usability, game balance or complete browser coverage.

## Current limits and next steps

- **Same-device play:** no cross-device room or real-time synchronisation.
- **Session-only state:** no accounts or persistent save/load.
- **Small screens:** the board uses horizontal scrolling where necessary.
- **Trading:** `transferProperty()` exists in the rules and tests, but there is no trading interface.
- **Further validation:** first-time-player observation and balance play-testing remain future work.

The public demo is hosted separately from GitHub. Repository changes require a separate deployment to update the live game.
