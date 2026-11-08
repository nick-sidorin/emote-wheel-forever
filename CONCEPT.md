# Emote Wheel — Concept

> **Status:** Idea / concept only. No code, working addon, or release exists yet.
> **Working title:** Emote Wheel (subject to change)
> **Initial target:** World of Warcraft: Forever
> **Design decision:** Reuse Blizzard's built-in gamepad radial wheel if its implementation is safely available to addons. This is a requirement to investigate, not a confirmed API integration.

## The idea

A small, elegant radial menu for a **personal set of favorite character emotes**, preferably built on **WoW Forever's own Blizzard gamepad radial UI** rather than a custom imitation.

Press a configurable key to bring up a wheel, choose a gesture, and let your character express it without typing a slash command or opening a large emote catalog. The experience should feel quick, unobtrusive, and at home in World of Warcraft's UI.

This concept grew out of a manually configured, six-button emote toolbar. That toolbar is useful, but occupies permanent screen space and relies on an inconsistent assortment of item/spell icons that do not actually depict the gestures.

**The goal is not to recreate every emote menu. It is to make a handful of favorite emotes delightful to use.**

## Experience goals

- **Instant access:** invoke the wheel with one configurable keybind, use an emote, return to the game.
- **Small by design:** start with eight slots for a curated set of favorites, rather than a searchable directory of every emote.
- **Be native by default:** investigate reusing Blizzard's actual radial menu, visual assets and interaction patterns; merely drawing something vaguely Warcraft-themed is not the desired result.
- **Stay out of the way:** the wheel appears only when summoned, then closes after selection or cancellation.
- **Personal first:** build around the author's favorite emotes before considering a broader public-facing customization system.
- **Work with both input styles:** keyboard/mouse is the initial personal use case; the wheel should also make sense for analog-stick selection on a gamepad. Verify controller bindings and supported frame behavior in Forever before promising direct support.

## Core interaction (proposed)

1. The player presses an assigned key to open an eight-segment wheel, ideally the existing Blizzard gamepad-style radial, in a comfortable on-screen position.
2. Moving the pointer (or, eventually, aiming the controller stick) highlights a segment, its icon and the emote name.
3. Clicking or otherwise confirming a segment performs that emote using the game's native emote behavior.
4. The wheel closes immediately. Pressing the key again or Escape cancels without performing anything.

**Eight sectors are a starting constraint, not a fundamental design limit:** the four cardinal directions plus four diagonals provide a natural mental model. If the native Blizzard wheel has a different supported structure, prefer investigating that structure before trying to force eight custom slices.

**Interaction details are not finalized.** A press-and-hold variant (hold key, point, release to execute) may feel even faster, but should only be adopted after in-game testing. Avoid surprising accidental emotes: no execution merely from opening or hovering over the wheel.

## First personal set (illustrative)

A possible eight-slot starting wheel:

| Slot | Emote | Typical slash command |
| --- | --- | --- |
| 1 | Wave | `/wave` |
| 2 | Bow | `/bow` |
| 3 | Laugh | `/laugh` |
| 4 | Cheer | `/cheer` |
| 5 | Clap | `/clap` |
| 6 | Salute | `/salute` |
| 7 | Shrug | `/shrug` |
| 8 | Point | `/point` |

The selection is a placeholder, not an approved final layout. Verify each command and its available localized aliases in the actual target client before implementation.

## Native Blizzard wheel — preferred approach

**The owner specifically wants the same wheel that Blizzard draws for gamepad menus in WoW Forever**, not a lookalike wheel designed from scratch. This is the main UI direction and should guide any eventual technical investigation.

WoW Forever has a built-in gamepad radial menu. Blizzard describes the main radial menu as part of its controller experience, and public addon/interface research references a `GamepadRadial` component. **That does not yet establish that addons may instantiate, customize or safely reuse it for emotes.** Compatibility must be verified against the actual Forever UI source and in-game runtime before any implementation is committed.

Preferred implementation order:

1. Inspect Forever's bundled Blizzard UI/FrameXML (and any relevant gamepad addons) for its radial frames, templates, assets, input bindings and exposed extension points.
2. **If supported and safe, use the real Blizzard radial component**, preserving native highlight, spacing, controller feel, scale and cancel/confirm behavior. Avoid disturbing the game's existing main menu or bindings.
3. If the component is private, protected or unsuitable, investigate **reuse of Blizzard-owned visual textures/atlases and interaction conventions** in an addon-owned wheel. Match the authentic interface closely without copying third-party addon assets or protected code paths.
4. Only consider a fully custom visual implementation if both native reuse approaches prove impractical, and document why.

### Icons

The original six-button toolbar used mismatched item and spell icons that did not represent their emotes. Avoid system emojis or an arbitrary mixture of shields, flowers and spell glyphs.

- First inspect original Blizzard icon/atlas assets for usable, thematically coherent gesture imagery.
- If suitable Blizzard assets do not exist, draw a **small, consistent original set for the chosen eight favorites**; do not commit to illustrating every emote.
- Keep sector labels readable so the MVP remains useful even while icon art is being explored.
- No permanent toolbar is required. The native radial, not an always-visible panel, is the preferred interaction.

## Evidence and references (as of November 2026)

- [Blizzard — Explore WoW: Forever with a Gamepad](https://worldofwarcraft.blizzard.com/en-us/news/24316974/): official confirmation of the controller UI and built-in main radial menu.
- [Improved Forever: Wheel](https://www.curseforge.com/wow/addons/improved-forever-wheel): another addon with controller wheels designed to resemble the native game wheel, but not proof that Blizzard's own radial frame is reusable.
- [Blizzard radial interface references in an addon investigation](https://github.com/zyaga/wow-forever-japanese/blob/main/docs/architecture/addon-modules.md): mentions `GamepadRadial:ActivateRadial` and related menu integration; investigate source and availability before relying on them.

## Scope boundaries

### First prototype — if development begins

- One configurable keybind.
- Eight fixed, personally selected emotes.
- One radial menu with clear highlight, confirm, and cancel behavior. **Use Blizzard's native gamepad wheel if technically supported; otherwise first reuse its visual assets and behavior.**
- Native game emote execution (no external services or accounts).
- Compatible with the actual Forever client, subject to testing.

### Possible future features — not MVP requirements

- Edit favorites from an available emote list, without turning the main wheel into a giant catalog.
- Support 8 vs. 12 slots, or several small named wheels.
- Remap slots, adjust size/position, and optionally use a compact bar.
- Controller-stick selection and customizable bindings **if the Forever client exposes a practical, safe interaction path**. Gamepad support is a design goal, not an already proven feature.
- A complete, consistent custom icon pack developed gradually, as needed.

### Explicit non-goals

- Shipping every emote with a unique illustration in the first version.
- Building a large always-visible emote database/browser as the primary interface.
- Copying another addon's art or implementation.
- Replacing WoW's default chat or changing roleplay text.
- Adding features just to match existing all-in-one emote addons.

## Technical questions to validate later

Before any code is written, check the **Forever** addon environment rather than assuming Retail APIs:

- What are the actual native Blizzard gamepad wheel components, template names, asset atlases, and their lifecycle? Can an addon instantiate or extend them safely without hijacking the main radial?
- Can the native wheel (or an addon-owned wheel built with native assets) be opened/closed safely both with keyboard/mouse mode and gamepad mode, in and out of combat, without protected-action warnings?
- Which native emote APIs or slash-command mechanisms are appropriate and permitted for a player-initiated choice? Is executing on key release permitted and reliable?
- Can keyboard and controller inputs coexist without interfering with ordinary movement or action bindings?
- How does Blizzard position its existing gamepad radial? How should UI scale, pointer position, screen edges, and different resolutions affect our invocation without changing the built-in menu?
- What does localization require for displayed emote names and command execution?

If implementation starts, keep it compatible with the client's Lua/runtime constraints, test in the real Forever client, and avoid intrusive protected UI hooks.

## Existing inspiration

- **Simple Emote Menu:** the convenient idea of choosing emotes from a large collection, but a broader interface than this project needs.
- **Stryk's Emote Wheel** and **Flick Wheel:** radial selection is an established direction; the differentiation here is the deliberately tiny **personal favorites first** scope and cohesive visual design.

This is an independent experiment, not an attempt to clone the above addons.

## Project principles

- Start with something the author actually enjoys using.
- Prefer a polished, small interaction over a feature-complete but cluttered interface.
- Keep the codebase, documentation, issues and PRs in **English**; localize actual game UI as appropriate.
- When the project moves beyond this concept, decide scope explicitly instead of treating every future idea as a commitment.

---

*This file records a product idea only. Development has not started.*