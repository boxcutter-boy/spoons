# Spoon

An energy-pacing app for people who run out. Single user, local only, no
accounts, no streaks, no guilt.

Spoon reads your calendar, learns what things actually cost you, and tells you
what the week ahead is going to do to you — early enough to change it.

**→ [Try it](https://boxcutter-boy.github.io/spoons/)**

It runs entirely in your browser. Nothing is sent anywhere; your state lives in
`localStorage` on your own device and goes no further.

## What's here

| | |
|---|---|
| `index.html` | The whole app. One file, no build step, no dependencies. |
| `concept.md` | The design document — every decision and why, including the ones that got reversed. |
| `open-questions.md` | Things not yet settled. |
| `dev-server.py` | A static server, so the prototype loads over `http://` and its fonts and `localStorage` work. |
| `wireframe-v1.html` | The first pass, kept for reference. |

## Running it

```bash
python3 dev-server.py
```

Then open <http://localhost:5173>. It reloads when the file changes.

You only need the server to edit it. Opening `index.html` straight off disk
mostly works, but `file://` blocks `localStorage`, so nothing persists between
reloads, and the web fonts don't load.

## The model, briefly

You have a charge between 0 and 1. Events cost against it, restorative things
give back, and the night refills you — non-linearly, so a day that ends near
empty recovers less than a day that ends comfortable. Overspending accrues debt,
which has to be paid back before the refill helps you again.

Nothing is rated. You mark the things that took it out of you, and everything
else drifts quietly downward — because a cost isn't a property of an event, it's
a property of an event at a point in your life. The daily standup you never mark
becomes almost free; the meeting you mark three times gets priced like it. Stop
marking something and it fades back down on its own.

Spoon never writes to your calendar. It prepares a change and hands it over —
you save it there, so anyone else on an invite hears it from their own calendar
rather than silently from us.

## Type

**EB Garamond** for display, **Libre Franklin** for everything working — a revival
of Franklin Gothic, which Morris Fuller Benton drew in 1903. Both are reading
faces rather than interface faces, which suits an app that is mostly sentences.

## Design notes

Nothing in the app generates prose at runtime. Every sentence is a fixed
template with enumerated slots, so all of it has to be written out and checked.
`concept.md` covers the rest.
