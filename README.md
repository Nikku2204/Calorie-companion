# Calorie Companion

Track your calories in a few snaps, with an AI coach to keep your meals on plan.

Built as a single React file. Every smart feature is powered by Claude, called straight from inside the app.

---

## Highlights

**A home screen with a pulse.** A "DAY N OF YOUR JOURNEY" header that increments off your real start date, a streak chip, a calorie room ring, and floating macro pills that fill as you log.

**Two day modes that flex with your training.** A Rest day / Workout day toggle that re-themes the whole screen. Workout days add your session burn on top of the baseline, so harder days get more fuel. Both targets are editable right from the home screen with a tap.

**A meal coach you actually talk to.** Brainstorm meals in plain language. When you settle on one, a "Log meal" button appears right there in the chat stream and drops it straight onto your dashboard. You can also just type edits like "change my lunch to 400" and the UI updates itself.

**Snap to log, three ways.**
- Photograph your plate and get a calorie and macro estimate, with the spotted items listed so you can confirm or tweak.
- Screenshot your fitness app and it reads each workout and its burn.
- Snap a protein bar wrapper or powder tub and it reads the label to fill in your staple.

**A staples library with one tap logging.** Save your favourite meals, protein bars and protein powders once, each gets a quick add button for instant logging later.

**Insights that stay encouraging.** Streak, consistency, variety, a seven day calories chart, a protein trend, and your most loved meal and go to workout, all pulled from what you have logged.

**It remembers.** Everything persists locally between sessions.

---

## How the AI works

The whole app is "Claude inside Claude". It calls Anthropic's Claude API directly from the client to power chat, vision and natural language editing.

**A structured side channel inside a normal chat.** The meal coach replies conversationally, but when a meal is finalised or an edit is requested it appends a hidden, delimited action block at the end of its reply:

```
<<<ACTION>>>{"type":"meal","meal":{"name":"...","calories":0,"protein":0,...}}<<<END>>>
```

The UI strips that block out of the visible message, parses it, and uses it to render the in stream "Log meal" button or to apply an edit to an existing entry. So one chat box does two very different jobs, brainstorming and structured data entry, without the user ever seeing the machinery.

**Vision with strict JSON and graceful fallback.** Each photo flow sends a base64 image plus a tailored prompt that asks for JSON only (dish name, items, calories, macros, confidence). Responses are parsed defensively, and if a read fails the app drops you into a manual entry form rather than dead ending.

**Day aware prompting.** The coach is told whether today is a rest or workout day and what the day's target is, so its suggestions match the calorie room you actually have.

---

## Under the hood

**Single file React.** One component, no router, no global state library. View state moves through onboarding, plan review and dashboard, with the dashboard split into tabbed sections (Home, Coach, Snap, Move, Staples, Insights).

**A small, swappable persistence layer.** Data is held in React state as the source of truth, with a thin storage helper layered on top for persistence. The helper writes through a try and catch and falls back to in memory if storage is unavailable, so the app never breaks even when persistence does. The adapter is deliberately tiny, so porting to plain `localStorage` for a standalone deploy is roughly a one function change.

**Calorie cycling, modelled honestly.** Two editable baselines (rest and workout). Room left on a rest day is baseline minus eaten, and on a workout day it is baseline plus logged burn minus eaten. The breakdown under the ring always shows exactly where the number comes from, and macro targets recompute per day so carbs scale up on training days while protein holds steady.

**A self contained design system.** All theming lives in the component as CSS custom properties, keyframe animations and inline styles, no Tailwind config, no external CSS. Includes a fix for the classic gradient text trap, where the `background` shorthand silently resets `background-clip` on re-render, by using the `background-image` longhand so clipped hero text survives the day mode toggle.

---

## Tech stack

- React (single functional component, hooks only)
- Anthropic Claude API for chat, vision and edits
- lucide-react for icons
- Inline CSS in component with custom properties and keyframe animation
- Client side persistence with an in memory fallback

No build config is required to read it, it is one `.jsx` file.

---

## Running and deploying

The smart features call `https://api.anthropic.com/v1/messages` without an API key, which works inside Claude's artifact sandbox because the key is injected there. The rest of the app (onboarding, dashboard, logging, staples, insights, persistence) is fully static and runs anywhere.

To run it as a standalone app or on GitHub Pages, two changes are needed:

1. **Add a key safely.** Put a small backend or proxy in front of the API call that attaches your `Authorization` header, so the key never ships to the browser.
2. **Swap the storage adapter.** Point the persistence helper at `localStorage` (or your own backend) instead of the sandbox store.

Drop the component into any React app (Vite or Create React App both work), install `lucide-react`, and render it.

---

## Project structure

```
CalorieCompanion.jsx     the entire app
README.md                you are here
```

One file on purpose. Easy to read top to bottom, easy to lift into any project.

---

## A note

This is a portfolio demo, built for fun and to explore AI native product patterns. Any calorie and macro figures it produces are rough estimates, not medical or nutritional advice. The defaults are intentionally gentle, baselines sit at maintenance, nothing is pushed into a deficit, and harder days add fuel rather than easier days cutting it.

---

## The series

This is one entry in an ongoing weekend build series, a recurring creative practice rather than a product. Each one tries to use AI in a genuinely different way and ship something small, polished and a little bit delightful by Sunday night.
