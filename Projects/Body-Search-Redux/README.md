# 🛡️ Body Search Redux

**An Immersive Guard Inspection System for Skyrim Special Edition / Anniversary Edition**

**Author:** Rhiven  
**Project Status:** Concept & Feasibility Study  
**Development Status:** Not Started  
**Target Platform:** Skyrim SE / AE  
**Primary Framework:** SexLab  
**Future Framework Support:** OStim (under consideration)

---

## 📖 Overview

**Body Search Redux** is a planned Skyrim mod that introduces immersive, randomized guard inspections for female player characters.

While exploring the cities of Skyrim, the player may occasionally be approached by a nearby city guard requesting a search for stolen goods and illegal substances.

Rather than triggering a simple inventory check, the interaction unfolds through a branching dialogue system where the player can cooperate, refuse or attempt to negotiate.

The objective is to create a believable gameplay mechanic that integrates naturally with Skyrim's existing guard, crime and arrest systems.

This project is inspired by the original **SexLab Body Search** mod.

It is planned as a new implementation, using the original mod's concepts and source files as technical references where permitted.

---

## 🎮 Planned Gameplay Features

### 1. Random Guard Inspections

City guards may initiate inspections based on configurable conditions.

Planned features:

- Random inspection probability adjustable through MCM.
- Short detection distance, approximately 3–5 meters.
- Only one guard inspection active at a time.
- Guards approach the player before initiating dialogue.
- Female player characters only.
- Configurable cooldown between inspections.
- City-specific restrictions and exclusions.
- No inspections during incompatible gameplay situations.

### 2. Dialogue-Based Player Choices

When approached, the player will have three main options:

**Cooperate with the Guard**

The guard requests a thorough body search.

The player may accept or refuse at the final warning.

If accepted:

- The player character automatically removes equipped clothing and armor.
- The inventory is inspected for stolen items and contraband.
- If nothing suspicious is found, the guard releases the player.
- If illegal items are detected, the guard initiates a consequences dialogue.
- Confiscated items are removed or transferred using appropriate inventory-handling mechanisms.
- The player's equipment is restored when the interaction ends.

**Refuse the Inspection**

The guard issues a final warning.

The player may:

- Reconsider and accept the inspection.
- Continue refusing and face the consequences.

A final refusal may trigger Skyrim's existing crime, arrest or guard hostility mechanics, allowing the game and other compatible mods to handle the resulting situation.

**Attempt to Negotiate**

The player may attempt to avoid the inspection through:

- A persuasion attempt with a configurable success probability, initially planned at 50%.
- An adult interaction through SexLab, initially planned with guaranteed success.

If persuasion fails, the negotiation option becomes unavailable, and the player must either cooperate or refuse.

### 3. Contraband and Stolen Goods

The inspection system is intended to detect:

- Stolen inventory items.
- Skooma.
- Additional configurable contraband categories, if technically feasible.

Possible technical solutions include:

- Skyrim's native stolen-item handling functions.
- Steelfeathers' Papyrus Extensions.
- FormLists for contraband detection.
- Controlled confiscation routines.

Special attention will be given to item ownership, mixed inventory stacks, quest items and compatibility with other mods.

### 4. Consequences and Penalties

When illegal items are discovered, the player may face several outcomes:

**Imprisonment**

Transfer control to Skyrim's existing arrest and prison mechanics.

**Financial Penalty**

Pay an automatically calculated fine based on confiscated items.

Initial concept: 100 septims per stolen item or bottle of skooma, configurable through MCM.

**Alternative Adult Interaction**

An optional SexLab interaction may allow the player to avoid the standard penalty.

All outcomes must return the game to a valid and stable state.

---

## ⚙️ Planned MCM Configuration

The mod is expected to provide a SkyUI-based MCM.

Potential settings include:

| Setting | Initial Concept |
|---|---|
| Enable Guard Inspections | On |
| Player Gender Restriction | Female Only |
| Inspection Probability | 12% (example) |
| Maximum Guard Distance | 3–5 meters |
| Inspection Cooldown | 24 in-game hours |
| City-Only Inspections | On |
| Fine Per Illegal Item | 100 septims |
| Persuasion Success Chance | 50% |
| Enable SexLab Alternatives | On |
| Debug Logging | Off |

All values remain provisional and may change during development.

---

## 🧠 Technical Design Goals

Body Search Redux is intended to use a new and maintainable architecture.

The planned approach includes:

- One central quest responsible for inspection management.
- Strict prevention of simultaneous inspections.
- Dedicated dialogue and inventory-handling logic.
- An explicit interaction state system.
- Controlled actor selection and approach behavior.
- Automatic cleanup after completed or interrupted interactions.
- Minimal unnecessary Papyrus polling.
- Safe restoration of player movement and equipment.
- Modular adult-framework integration.

### Proposed Interaction States

```text
IDLE
  |
  v
DETECTED
  |
  v
APPROACHING
  |
  v
DIALOGUE
  |
  +--> SEARCH
  |
  +--> NEGOTIATION
  |
  +--> REFUSAL
  |
  +--> SANCTION
  |
  v
CLEANUP
  |
  v
IDLE
```

This state model is preliminary and will be refined during the architecture phase.

---

## 🛡️ Compatibility and Safety

A major design priority is avoiding conflicts with active quests, combat encounters and scripted gameplay systems.

Before initiating an inspection, the mod should verify that:

- The player and selected guard are alive and available.
- The player is not in combat.
- The player is not already engaged in dialogue.
- No incompatible animation or adult scene is active.
- No other guard inspection is running.
- The player is not currently being arrested or imprisoned.
- No conflicting defeat or captivity system is controlling the player.
- The player is not participating in a detectable scripted scene or protected quest interaction.
- The inspection cooldown has expired.

These conditions should be checked before selecting or approaching the player and again immediately before starting the dialogue.

Because Skyrim does not provide a universal indicator for every scripted quest interaction, some compatibility checks may require dedicated integration or exclusions.

### Failure Recovery

The mod should always provide a safe recovery mechanism.

Interrupted interactions must release reserved actors, restore temporary controls and prevent permanent dialogue or movement locks.

Compatibility with large modlists such as **NEFARAM** will be an important testing objective.

---

## 🔌 Dependencies and Frameworks

### Planned / Potential Requirements

- Skyrim Special Edition / Anniversary Edition
- SKSE64
- SkyUI
- SexLab Framework
- Steelfeathers' Papyrus Extensions (if selected after technical validation)

Additional dependencies will be identified during development.

### Future OStim Support

The initial implementation will prioritize SexLab.

A future OStim integration may be developed through a separate compatibility adapter, keeping the core guard inspection system independent from the animation framework whenever practical.

OStim support is not guaranteed and will depend on technical feasibility.

---

## 🏗️ Development Strategy

Body Search Redux is planned as a **new mod rather than a direct update** of the original SexLab Body Search.

Development will follow these stages:

1. Review the original mod and available source files.
2. Identify reusable concepts and implementation techniques.
3. Examine permissions and third-party dependencies.
4. Finalize the functional specification.
5. Design the Creation Kit and Papyrus architecture.
6. Implement a minimal guard interception and dialogue prototype.
7. Add inventory inspection and crime consequences.
8. Integrate SexLab interactions.
9. Implement the MCM configuration.
10. Perform stability, compatibility and gameplay testing.

No implementation work has started.

---

## 📚 Planned Documentation

Additional documentation will be published as the project progresses.

| Document | Purpose |
|---|---|
| `CONCEPT.md` | Detailed gameplay scenarios and dialogue branches |
| `FEASIBILITY.md` | Technical research, Papyrus functions and identified risks |
| `ARCHITECTURE.md` | Quest structure, scripts, aliases and state management |
| `CREDITS.md` | Original authors, contributors and permissions |
| `CHANGELOG.md` | Development and release history |

These documents will be added progressively.

---

## 📜 Original Mod and Credits

**Body Search Redux** is inspired by the original **SexLab Body Search** modification.

The original project's permissions explicitly allow modifications and derivative versions, provided that appropriate credit is given.

The original permission statement also requires credit to **Suzutsuki**.

The original author(s), contributors and source links will be documented in `CREDITS.md` after a complete review of the original project.

All third-party resources remain subject to their respective permissions.

This project does not claim ownership of the original mod or its third-party assets.

---

## 🚧 Current Development Status

**Concept and feasibility study only.**

At this stage:

- The main gameplay concept has been defined.
- The initial dialogue branches have been outlined.
- Basic MCM settings have been proposed.
- A new modular architecture has been selected.
- Potential technical dependencies have been identified.
- No Papyrus scripts or Creation Kit records have been developed.
- No playable build is available.
- No release date has been established.

Specifications may evolve as the original mod and Skyrim's scripting capabilities are investigated.

---

## 👤 Project Author

**Rhiven**

Part of the [Rhiven-SkyrimSE-Mods](https://github.com/Rhivens/Rhiven-SkyrimSE-Mods) repository.

---

*Keeping Skyrim's guards vigilant — and giving the Dragonborn a few more difficult choices.*

**⚔️ Work in progress — May your Papyrus scripts remain stable!**
