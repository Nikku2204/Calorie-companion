# Calorie Companion

Track your calories in a few snaps, with an AI coach that plans meals, looks things up, and logs straight from the chat.

### [Try it live](https://claude.ai/public/artifacts/1baf940c-b9c3-4f27-9d0a-9c1c5076ce06)

Part of a weekend app series: one small, genuinely novel build per weekend. No roadmap, no scope creep, no stakeholders, just a silly idea and a Sunday night deadline.

Built as a single React file. Every smart feature is powered by Claude, called straight from inside the app.

---

## A look inside

<table>
  <tr>
    <td width="50%"><img src="home1.png" alt="Home screen" width="100%"><br><sub><b>Home</b> &middot; your day at a glance, with rest and workout day modes</sub></td>
    <td width="50%"><img src="coach1.png" alt="Meal coach" width="100%"><br><sub><b>Coach</b> &middot; plan, search and log straight from the chat</sub></td>
  </tr>
  <tr>
    <td width="50%"><img src="snap1.png" alt="Snap a meal or your kitchen" width="100%"><br><sub><b>Snap</b> &middot; photograph a plate, or your kitchen for ideas</sub></td>
    <td width="50%"><img src="move1.png" alt="Log a workout" width="100%"><br><sub><b>Move</b> &middot; log a workout from a fitness screenshot</sub></td>
  </tr>
  <tr>
    <td width="50%"><img src="staples1.png" alt="Staples library" width="100%"><br><sub><b>Staples</b> &middot; save go-tos for one-tap logging</sub></td>
    <td width="50%"></td>
  </tr>
</table>

---

## Features

**An AI meal coach you actually talk to.** Brainstorm a meal in plain language and get a realistic calorie and protein breakdown. When a meal looks right, an Add button appears under it in the chat, and one tap logs it straight to your day. You can also type edits like "change my lunch to 400" and the app updates itself.

**Show the coach what you have.** Snap your fridge, your counter, or a meal you are about to eat, attach it in the chat, and the coach builds a suggestion around what is actually there.

**The coach looks things up.** Tell it what you ordered, like a bowl from a named chain or a dish from a local restaurant, and it searches the web for real menu numbers to estimate from, rather than guessing.

**Snap a meal.** Photograph your plate and get a calorie and macro estimate, with the items it spotted listed so you can confirm or tweak before logging.

**Ideas from your kitchen.** Photograph your fridge or counter and get a few meals you could make from what you have, each one ready to log or to take into the coach for a refine.

**Log a workout from a screenshot.** Screenshot your fitness app and it reads each workout and its burn, then adds it to your day.

**A staples library with one tap logging.** Save your favourite meals, protein bars and protein powders once, and each gets a quick add button. Fill a new staple by snapping its label, or just type a product name and it searches the web for the calories and protein.

**Rest days and workout days that flex.** A simple toggle with two separate baselines. Workout days add the energy you burn on top, so harder days get more fuel, and the macros recompute per day. Both baselines are editable in a tap.

**Gentle mode.** Switch off the exact figures and the app shows how fuelled you feel and the habits you are building instead. Nothing to hit, nothing to fail. The defaults are non restrictive throughout.

**A weekly recap.** A short, warm, animated recap of your week, written from what you have logged.

**Insights that stay encouraging.** Days showing up, consistency, variety, a seven day chart, and your most loved meal and go to workout, all pulled from your log.

**Make it yours.** Set your own background image, per page if you like.

**It remembers.** Everything persists locally between sessions.

---

## How the AI works

The whole app is "Claude inside Claude". It calls Anthropic's Claude API (Claude Sonnet) directly from the client to power the coach, the vision estimates, the web lookups and the weekly recap.

**A structured side channel inside a normal chat.** The coach replies conversationally, but when a meal is finalised or an edit is requested it appends a hidden, delimited action block at the end of its reply:

```
<<<ACTION>>>{"type":"meal","meal":{"name":"...","calories":0,"protein":0,...}}<<<END>>>
```

The UI strips that block out of the visible message, parses it, and uses it to render the in stream Add button or to apply an edit to an existing entry. So one chat box does two very different jobs, brainstorming and structured data entry, without the user ever seeing the machinery.

**Vision with strict JSON and graceful fallback.** Each photo flow sends a base64 image plus a tailored prompt that asks for JSON only (a dish name, items and macros, or a list of ingredients and meal ideas). Responses are parsed defensively, and if a read fails the app drops you into a manual entry form rather than dead ending.

**Web search where it helps.** The coach and the staples lookup use Claude's web search tool to pull real nutrition for restaurants, takeaways and branded products, and fall back to the model's own knowledge for home cooked food.

**Day aware prompting.** The coach is told whether today is a rest or workout day and how much room and which macros are left, so its suggestions match the calorie room you actually have.

---

## Under the hood

**Single file React.** One component, hooks only, no router and no global state library. View state moves through onboarding, plan review and dashboard, with the dashboard split into tabbed sections: Home, Coach, Snap, Move, Staples, Insights.

**Honest calorie modelling.** Two editable baselines, rest and workout. Room left on a rest day is baseline minus eaten, and on a workout day it is baseline plus logged burn minus eaten. Macro targets recompute per day so carbs scale up on training days while protein holds steady. Inputs are range checked and the result is capped, so a stray entry cannot produce a nonsense plan.

**A small, swappable persistence layer.** Data lives in React state as the source of truth, with a thin storage helper on top. The helper writes through a try and catch and falls back to in memory if storage is unavailable, so the app never breaks even when persistence does. Porting to plain localStorage for a standalone deploy is roughly a one function change.

---

## Tech stack

- React (single functional component, hooks only)
- Anthropic Claude API for the coach, vision, web lookups and recaps
- Claude web search tool for restaurant and product nutrition
- lucide-react for icons
- Client side persistence with an in memory fallback

It is one `.jsx` file, no build config required to read it.

---

## Running and deploying

The smart features call `https://api.anthropic.com/v1/messages` without an API key, which works inside Claude's artifact sandbox because the key is injected there, so the published link above is the live demo. The rest of the app (onboarding, dashboard, logging, staples, insights, persistence) is fully static and runs anywhere.

To run it standalone or on GitHub Pages, two changes are needed:

1. **Add a key safely.** Put a small backend or proxy in front of the API call that attaches your `Authorization` header, so the key never ships to the browser. The same proxy carries the web search tool.
2. **Swap the storage adapter.** Point the persistence helper at `localStorage` or your own backend instead of the sandbox store.

Drop the component into any React app (Vite or Create React App both work), install `lucide-react`, and render it.

---

## A note

This is a portfolio demo, built for fun and to explore AI native product patterns. Any calorie and macro figures it produces are rough estimates, not medical or nutritional advice. The defaults are intentionally gentle: baselines sit at maintenance, nothing is pushed into a deficit, and harder days add fuel rather than easier days cutting it.

---

## The series

This is one entry in an ongoing weekend build series, a recurring creative practice rather than a product. Each one tries to use AI in a genuinely different way and ship something small, polished and a little bit delightful by Sunday night.
