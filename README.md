<img src=".github/fox.png" width="160" alt="">

# page-mascot

An interactive character that watches the cursor and blinks when you poke it.

**[See the fifty-two &rarr;](https://koboyo.com/page-mascot)**

## Install

```bash
npm i page-mascot
```

## Use existing mascots

Pick a character on the [demo page](https://koboyo.com/page-mascot) and download its two
sheets into `public/mascots`, then point the component at them:

```tsx
import { Mascot } from '@dangnguyendota/page-mascot'

<Mascot
  directions="/mascots/fox-directions.webp"
  reactions="/mascots/fox-reactions.webp"
/>
```

The paths are whatever your app serves, so imported images work too.

Or let an agent do it, once the skill below is installed:

```
/page-mascot put the fox on my page
```

## Draw your own

Install the skill:

```bash
npx skills add nilbuild/page-mascot --skill page-mascot --global --yes
```

Then ask your agent:

```
/page-mascot a chibi otter with chocolate-brown fur
/page-mascot make one that looks like me        [attach a photo]
/page-mascot a chibi fox, in the riso style
```

It draws the nine directions and nine expressions, builds them into two aligned sheets,
checks the character does not jump between them, and puts the component on your page, with
the same two props as above.

Drawing needs an image tool. Codex has its own; Claude Code goes through the OpenAI images
API:

```bash
export OPENAI_API_KEY=sk-...
```

Or take the [prompts](skills/page-mascot/reference/prompts.md) and use them in any chat
UI.

## Props

| prop | default | |
| --- | --- | --- |
| `directions` | none | path to the directions sheet |
| `reactions` | none | path to the reactions sheet |
| `size` | `140` | px, square |
| `label` | `'mascot'` | what a screen reader calls it |
| `className` | | |

Tracking switches off without a fine pointer, and the click squash honours
`prefers-reduced-motion`.

## Play an animation

Pass a ref to trigger an expression from code, without waiting for a click:

```tsx
import { useRef } from 'react'
import { Mascot, type MascotHandle } from '@dangnguyendota/page-mascot'

const mascotRef = useRef<MascotHandle>(null)

<Mascot
  ref={mascotRef}
  directions="/mascots/fox-directions.webp"
  reactions="/mascots/fox-reactions.webp"
/>

<button onClick={() => mascotRef.current?.playAnimation('happy')}>
  Happy
</button>
```

`playAnimation(name)` walks a short sequence of cells on the reactions sheet, then
returns to cursor tracking. A second call cancels the one that is playing and starts
the new sequence from the first frame. Unknown names are ignored.

Built-in sequences:

| name | |
| --- | --- |
| `happy` | delighted ↔ heart |
| `celebrate` | sparkle, heart, delighted |

Every expression on the sheet is also a name, held briefly then released: `blink`,
`heart`, `sparkle`, `surprised`, `wink`, `bashful`, `sleepy`, `dizzy`, `delighted`.

Clicking the mascot still plays the original poke reaction.

## How it works

Each character is two 3×3 sprite sheets: nine head directions, and nine expressions.

![the two sheets that make up one character](.github/sheets.png)

The pointer's angle picks a cell on the directions sheet, with a dead zone so the head
settles when the cursor is close. A click shows a cell from the reactions sheet for half a
second. `playAnimation` uses that same sheet: while a sequence is playing it wins over
head-turning, then tracking resumes on its own.

The same character can be drawn in six styles: colour, ink, sketch, riso, paper and pixel.
Only the rendering changes, so the alignment holds.

![the same fox drawn in six styles](.github/styles.png)

## License

MIT © [Kamran Ahmed](https://kamran.fyi)
