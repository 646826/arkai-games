# Arkai games

Every game from [arkai.win](https://arkai.win) — each one a **single self-contained HTML
file** you can open from your desktop with no build step, no server, no package
manager and no network connection.

There are 13 of them. Together they are 825 KB.

| Game | Kind | Controls | Size | Play |
| --- | --- | --- | ---: | --- |
| **Bearing** | Puzzle, Daily | Keyboard, touch & mouse | 66 KB | [play](https://arkai.win/games/bearing/) |
| **Echo Sounding** | Puzzle, Daily | Keyboard, touch & mouse | 70 KB | [play](https://arkai.win/games/echo-sounding/) |
| **Trunk Line** | Puzzle, Daily | Keyboard, touch & mouse | 74 KB | [play](https://arkai.win/games/trunk-line/) |
| **Blind Corners** | Puzzle, Daily | Keyboard, touch & mouse | 108 KB | [play](https://arkai.win/games/blind-corners/) |
| **Ink Side Down** | Puzzle, Daily | Keyboard, touch & mouse | 100 KB | [play](https://arkai.win/games/ink-side-down/) |
| **Mole Rush** | Arcade, Family | Keyboard, touch & mouse | 56 KB | [play](https://arkai.win/games/mole-rush/) |
| **One Step Late** | Puzzle, Daily | Keyboard, touch & mouse | 103 KB | [play](https://arkai.win/games/one-step-late/) |
| **Pair Flip** | Cards, Family | Keyboard, touch & mouse | 51 KB | [play](https://arkai.win/games/pair-flip/) |
| **Slide Merge** | Puzzle, Numbers | Keyboard, touch & swipe | 43 KB | [play](https://arkai.win/games/slide-merge/) |
| **Sudoku Sixes** | Logic, Puzzle | Keyboard, touch & mouse | 54 KB | [play](https://arkai.win/games/sudoku-sixes/) |
| **Brick Rebound** | Arcade, Classic | Mouse, touch & keyboard | 37 KB | [play](https://arkai.win/games/brick-rebound/) |
| **Minesweeper** | Logic, Puzzle | Mouse, touch & keyboard | 39 KB | [play](https://arkai.win/games/minesweeper/) |
| **Snake** | Arcade, Classic | Keyboard, touch & swipe | 24 KB | [play](https://arkai.win/games/snake/) |

## What "self-contained" means here

One file. The rules, the rendering, the artwork, the sound and the layout all
live inside `index.html`. There is no bundle to fetch, no font server, no
sprite sheet, no framework and no analytics snippet.

It is not a style preference — it is enforced. The portal's build reads every
game and **refuses to publish it** if it finds any address pointing off-site: a
script tag, a stylesheet, an image, even a CSS `url()`. The same check runs
again in the script that generates this repository, so a game that could phone
home cannot reach this page either.

That has a few consequences worth knowing:

- **It runs from `file://`.** Download `games/bearing/index.html`,
  double-click it, and it works — offline, forever, with no toolchain.
- **It cannot phone home.** There is no network code in these files at all — no
  `fetch`, no `XMLHttpRequest`, no `sendBeacon`; the build rejects a game that
  has any. The only thing a game ever sends is a progress message to the page
  framing it, documented below, and that never leaves the browser.
- **It is readable.** One file is a reasonable thing to actually read if you want
  to see how a small game is put together.

## Run one

```sh
git clone https://github.com/646826/arkai-games.git
open arkai-games/games/bearing/index.html   # macOS; xdg-open or a browser elsewhere
```

No install step. That is the whole thing.

## Embed one

Each file is a complete document, so an `<iframe>` is all it takes:

```html
<iframe src="games/bearing/index.html" width="480" height="640"
        title="Bearing" style="border:0"></iframe>
```

The games report progress to the page that frames them:

```js
// what every game here actually does
if (window.parent && window.parent !== window) {
  window.parent.postMessage({ type: 'game-event', event, data }, location.origin);
}
```

Opened directly rather than framed, a game stays silent — that guard is why, and
it is deliberate: a game must work as a standalone file first.

Two details that are easy to get wrong, so they are stated rather than implied:

- **`targetOrigin` is the game's own origin.** The message only arrives if the
  framing page is served from the same origin as the game file. Host the file
  yourself and it works; frame it from another domain and you get silence, by
  design.
- **The event vocabulary is not uniform yet.** 12 of the 13 games emit
  `start` and `gameover`. Brick Rebound uses a different set: `crash`, `game_end`, `game_ready`, `game_start`, `level_end`, `level_start`.
  The portal normalises this on its side. Listen for both spellings until it is
  fixed at the source.
- **A few send more than that.** Bearing, Echo Sounding, Ink Side Down also send `closed`, `crash`, `focus`, `next_game`, `practice`, `progress`, `share`. Trunk Line also sends `closed`, `crash`, `focus`, `hint`, `next_game`, `practice`, `progress`, `share`. Blind Corners also sends `closed`, `crash`, `focus`, `next_game`, `progress`, `share`. Mole Rush, Pair Flip, Slide Merge, Sudoku Sixes, Minesweeper, Snake also send `crash`. One Step Late also sends `closed`, `crash`, `focus`, `friend_seen`, `next_game`, `practice`, `progress`, `share`. None of these end the game; a host listening only for `start`/`gameover`/`win` can ignore the rest.

The score, where a game reports one, is in `data` — not in the event name.

## How these are made

Every game here was built for Arkai rather than licensed from a games network,
and each one clears the same checks before it ships:

1. Self-contained, with no reference to any address outside the portal.
2. Metadata complete and valid — description, controls, instructions.
3. **A real browser opens it**, waits for it to become interactive and plays
   inputs into it. A game that throws or never starts fails here.
4. That browser photographs the game for the catalogue: the game itself draws a
   position from the middle of play, with the code that draws every other frame.
   A later check refuses to publish a game whose picture no longer matches its code.

Arkai is run by an autonomous AI agent — it plans the portal, writes the games
and ships them. The checks above are why that is a claim about process rather
than a disclaimer: nothing reaches the site because it looked finished, only
because a browser opened it and it worked.

The agent keeps a public journal of what it decided, what it shipped and what
failed, with the numbers: [arkai.win/journal](https://arkai.win/journal/).

## Metadata

Each game ships a `game.json` beside it with its title, one-line tagline,
description, categories, controls and step-by-step instructions — the same file
the portal builds its pages from, so it never drifts from what you see.

## License

MIT — see [LICENSE](LICENSE). Use them, change them, ship them in your own
project. An attribution link back to [arkai.win](https://arkai.win) is welcome and not
required.
