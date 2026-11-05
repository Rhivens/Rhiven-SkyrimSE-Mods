# 🛡️ Body Search Redux — Gameplay Concept

**Project:** Body Search Redux  
**Author:** Rhiven  
**Document:** CONCEPT.md  
**Version:** 0.1 — Initial Design Specification  
**Date:** November 5, 2026  
**Status:** Concept / Feasibility Study  
**Development:** Not Started

---

## 1. Project Vision

**Body Search Redux** is a planned immersive guard inspection system for **The Elder Scrolls V: Skyrim Special Edition / Anniversary Edition**.

The mod introduces occasional encounters in which city guards stop female player characters to conduct inspections for stolen property and illegal substances.

These encounters are intended to feel like natural extensions of Skyrim's existing guard behavior rather than isolated scripted events.

The player should always have meaningful choices, and those choices should lead to different consequences.

### Core Design Principles

- Immersive and believable guard encounters.
- Female player characters only.
- Randomized inspections with configurable frequency.
- Short-range NPC interactions.
- Branching dialogue with meaningful decisions.
- Inventory inspections and potential confiscation.
- Integration with Skyrim's existing crime and arrest systems.
- Optional adult interactions through SexLab.
- Strong compatibility with other gameplay mods.
- Reliable recovery from interrupted interactions.
- Minimal unnecessary Papyrus processing.

The mod will be developed as a **new implementation**, inspired by the original *SexLab Body Search*.

Existing source files may be studied and selectively reused where permissions allow.

---

## 2. Gameplay Overview

While traveling through a city, the player character may be selected for a random inspection by a nearby guard.

If all required conditions are satisfied, the guard approaches the player and initiates dialogue.

The guard informs the player that a search is required to verify that she is not carrying stolen goods or contraband.

The player then chooses how to respond.

### Main Dialogue Options

1. **Cooperate:** Agree to the inspection.
2. **Refuse:** Decline the guard's request.
3. **Negotiate:** Attempt to avoid the inspection through persuasion or an adult interaction.

Each choice leads to a specific dialogue branch.

The encounter must always terminate in a valid gameplay state.

---

## 3. Guard Detection and Interception

### 3.1 Detection Range

Guards should only initiate inspections when already close to the player.

**Target distance:** approximately 3–5 meters.

The exact in-game unit conversion and distance behavior will be calibrated during testing.

Long-distance pursuit solely to initiate an inspection is not intended.

### 3.2 Inspection Probability

Each eligible interception opportunity uses a configurable random probability.

A default value of **12%** is proposed for initial testing.

The probability will be adjustable through the MCM.

The precise trigger frequency and probability evaluation model remain to be defined.

### 3.3 Guard Selection

A guard must satisfy the following conditions:

- Belongs to an eligible city guard faction or is explicitly supported.
- Is alive and enabled.
- Is sufficiently close to the player.
- Is not engaged in combat.
- Is not involved in an incompatible scripted interaction.
- Is available to approach and speak to the player.
- Is not already participating in another Body Search encounter.

Only one guard may conduct an inspection at any given time.

### 3.4 Starting the Encounter

The intended sequence is:

1. The system detects an eligible guard near the player.
2. The system checks whether an inspection is permitted.
3. The inspection probability is evaluated.
4. A single guard is selected and reserved.
5. The guard approaches the player if necessary.
6. The player and guard are positioned and oriented for dialogue.
7. Player movement is temporarily restricted when required.
8. The guard initiates dialogue.

The desired interaction style is similar to the encounter system developed for **NORA Dangerous Nights** in Fallout 4.

This is a functional reference only; Skyrim-specific APIs and implementation methods will be used.

### 3.5 Interception Cooldown

An inspection cooldown is required to avoid repetitive encounters.

**Proposed initial value:** 24 in-game hours.

The cooldown will be configurable.

The system must prevent multiple guards from independently initiating inspections during the same encounter.

---

## 4. Main Dialogue — Initial Encounter

The guard approaches the player and requests an inspection.

### Example Dialogue

**Guard:**

> Hold there. Routine inspection. We've had reports of stolen goods and illegal substances being carried through the city. I'll need to search you.

**Player Options:**

**[1] Cooperate**

> Very well. You can search me.

**[2] Refuse**

> I haven't done anything wrong. I'm not letting you search me.

**[3] Negotiate**

> Surely we can settle this without a search?

These lines are illustrative placeholders.

Final dialogue writing and localization will be handled during development.

---

## 5. Scenario A — Cooperate with the Inspection

### 5.1 Guard's Instructions

The guard explains that the inspection must be thorough and requires the player to remove her clothing and armor.

**Example Dialogue:**

**Guard:**

> This will be a thorough search. Remove your armor and clothing so I can make sure you're not concealing anything.

The player receives a final choice.

**Player Options:**

- **Accept:** Comply with the inspection.
- **Refuse:** Decline the search and proceed to the refusal scenario.

### 5.2 Player Compliance

If the player accepts:

1. The system records the relevant equipped items.
2. The player automatically removes her clothing and armor.
3. The system inspects the player's inventory.
4. The guard evaluates the results.
5. The encounter proceeds to the appropriate outcome.

The player's equipped items must be restored when the inspection ends, unless another gameplay system legitimately changes their ownership or availability.

### 5.3 No Illegal Items Found

If no contraband or stolen goods are detected:

**Guard:**

> Everything appears to be in order. You're free to go.

The encounter ends.

The system must:

- Restore the player's equipment.
- Release any movement restrictions.
- Return the guard to normal AI behavior.
- Clear the active inspection state.
- Apply the configured cooldown.

### 5.4 Illegal Items Found

If stolen goods or contraband are detected:

1. The system identifies the relevant items.
2. The items are confiscated according to the selected confiscation mechanism.
3. The guard announces that illegal items have been discovered.
4. The encounter enters the **Consequences Dialogue**.

**Example Dialogue:**

**Guard:**

> Well, well. Looks like you've been carrying something you shouldn't. We'll have to deal with this.

The player is then offered the available consequences.

---

## 6. Scenario B — Refuse the Inspection

### 6.1 Initial Refusal

If the player refuses the inspection, the guard issues a final warning.

**Example Dialogue:**

**Guard:**

> Refusing a lawful inspection is a serious matter. This is your last warning. Cooperate, or face the consequences.

### 6.2 Final Decision

The player has two options.

**[1] Reconsider**

> Fine. I'll cooperate.

The encounter returns to **Scenario A — Cooperate with the Inspection**.

**[2] Continue Refusing**

> No. You're not searching me.

The refusal becomes final.

### 6.3 Final Refusal Consequences

The system transfers control to Skyrim's existing crime and guard behavior mechanisms.

Possible results include:

- Guards treating the player as resisting arrest.
- Hostility from the appropriate city guards.
- Combat.
- Escape.
- Surrender or arrest.
- Imprisonment.
- Intervention by compatible defeat or crime-related mods.

The exact crime handling mechanism must be validated during technical development.

The mod should avoid unnecessarily modifying permanent faction relationships or creating lasting hostility outside the relevant crime system.

Once the refusal outcome has been handed over to the game, Body Search Redux must terminate its active inspection safely.

---

## 7. Scenario C — Negotiate with the Guard

The player attempts to avoid the inspection.

Two negotiation options are available.

### 7.1 Option C1 — Adult Proposition

The player offers a sexual favor in exchange for the guard overlooking the inspection.

**Planned success probability:** 100%.

**Example Player Dialogue:**

> Perhaps there's another way I could persuade you to forget about this inspection...

The guard accepts the offer.

A consensually selected player dialogue option initiates the configured SexLab scene.

### Expected Sequence

1. The player selects the adult proposition.
2. The guard accepts.
3. The dialogue transitions to the SexLab interaction.
4. The selected animation scene begins.
5. The system waits for the scene to finish.
6. The guard releases the player.
7. The encounter ends and all temporary states are cleared.

The player is not searched.

No inventory inspection or confiscation occurs in this branch.

### 7.2 Option C2 — Persuasion Attempt

The player attempts to convince the guard that an inspection is unnecessary.

**Planned initial success probability:** 50%.

This probability should be configurable through the MCM.

**Example Player Dialogue:**

> I'm an honest traveler. Surely you can see there's no reason to search me?

#### Successful Persuasion

**Guard:**

> Very well. I'll let you go this time. Don't give me a reason to regret it.

The player is released.

No inspection takes place.

#### Failed Persuasion

**Guard:**

> Nice try. But you're not talking your way out of this. Now, will you cooperate or not?

The negotiation option becomes unavailable for the remainder of the encounter.

The player must choose:

- Cooperate with the inspection.
- Refuse the inspection.

### 7.3 Negotiation Restrictions

The player must not be able to repeat failed persuasion attempts indefinitely.

An unsuccessful persuasion roll permanently disables the negotiation branch for that encounter.

Whether persuasion should incorporate the player's Speech skill remains an open design question.

The initial concept uses a fixed configurable probability.

---

## 8. Consequences Dialogue

This dialogue is triggered after illegal goods have been found during an inspection.

The player is presented with three potential outcomes.

### 8.1 Consequence 1 — Imprisonment

The player accepts imprisonment.

**Example Player Dialogue:**

> All right. Take me to jail.

The system delegates imprisonment to Skyrim's existing crime and jail mechanics.

The intended behavior is to avoid creating a separate prison system.

Compatibility with third-party prison mods will be evaluated.

### 8.2 Consequence 2 — Financial Penalty

The player pays a fine calculated from the confiscated items.

**Proposed formula:**

`Fine = Number of Illegal Items × Fine Per Item`

**Initial value:** 100 septims per illegal item.

Example:

- 3 stolen items.
- 2 bottles of skooma.
- Total illegal item count: 5.
- Fine per item: 100 septims.

**Total fine: 500 septims.**

The amount must be configurable through MCM.

The final design must define how insufficient gold is handled.

The fine should not accidentally clear unrelated existing bounties unless explicitly intended.

### 8.3 Consequence 3 — Adult Proposition

The player offers a sexual favor to avoid imprisonment or paying the financial penalty.

**Planned success probability:** 100%.

A SexLab scene begins.

When the scene finishes:

- The guard releases the player.
- The encounter terminates.
- Normal gameplay resumes.

**Important design decision:** Goods already confiscated during the inspection remain confiscated. The adult alternative avoids the additional penalty; it does not reverse confiscation.

This behavior is provisional and may be revised.

---

## 9. Inventory Inspection and Confiscation

### 9.1 Stolen Items

The system must identify inventory items considered stolen by Skyrim.

Potential approaches include:

- Native Skyrim inventory and ownership functions.
- `RemoveAllStolenItems()`.
- Steelfeathers' Papyrus Extensions.
- Additional SKSE inventory functions if required.

Technical behavior must be verified before choosing the final implementation.

### 9.2 Contraband

The first supported illegal substance will be:

- **Skooma**

Additional forms or variants may be supported through configurable FormLists.

Future compatibility with mods introducing new contraband items may be considered.

### 9.3 Confiscation Rules

The system should:

- Detect relevant illegal items.
- Calculate the quantity of confiscated goods.
- Avoid removing unrelated inventory items.
- Avoid corrupting mixed inventory stacks.
- Preserve protected quest items whenever possible.
- Transfer confiscated goods to an appropriate container or use validated native confiscation behavior.
- Avoid destroying items unnecessarily.

The handling of mixed stolen and legitimate item stacks requires dedicated investigation.

### 9.4 Inspection Results

The system distinguishes two basic outcomes:

**Clean Inventory**

No stolen goods or supported contraband detected.

**Illegal Inventory**

At least one stolen item or supported contraband item detected.

The second outcome activates the consequences dialogue.

---

## 10. Optional Adult Framework Integration

### 10.1 Initial Framework — SexLab

SexLab will be the primary adult animation framework for the initial implementation.

The system must be able to:

- Identify the player and selected guard.
- Request an appropriate animation scene.
- Handle scene initiation failures.
- Detect scene completion.
- Resume the encounter afterward.
- Recover safely if the scene is interrupted.

SexLab scenes should never start if the framework or involved actors are unavailable.

### 10.2 Future Framework — OStim

OStim support is under consideration.

The preferred architecture separates adult scene handling from the core guard inspection logic.

Possible components:

**Body Search Redux Core**

Responsible for:

- Guard detection.
- Interception.
- Dialogues.
- Inventory inspection.
- Crime consequences.
- State management.

**SexLab Integration**

Responsible for SexLab scene execution and completion callbacks.

**Future OStim Integration**

Responsible for equivalent OStim interactions.

This modular approach is a design objective rather than a finalized technical implementation.

---

## 11. Compatibility and Safety Checks

Body Search Redux must not initiate encounters during incompatible gameplay situations.

### 11.1 Required Checks

Before starting an inspection, the system should verify:

- The player character satisfies the female-only restriction.
- The player is in an eligible location.
- The selected guard is available.
- Neither actor is in combat.
- The player is not currently engaged in another dialogue.
- No other Body Search inspection is active.
- The inspection cooldown has expired.
- The player is not already under arrest.
- The player is not imprisoned.
- The player is not participating in an incompatible adult animation.
- The player is not controlled by a defeat, restraint or captivity system.
- No detectable protected quest scene is active.
- The player is not otherwise in an incompatible scripted interaction.

### 11.2 Validation Timing

Checks should be performed at least twice:

**First Validation**

Before selecting and reserving a guard.

**Second Validation**

Immediately before starting dialogue.

If conditions become invalid, the encounter must be cancelled safely.

### 11.3 Known Compatibility Targets

Special attention should be paid to:

- SexLab Framework.
- Acheron.
- PAMA Prison Alternative.
- Devious Devices.
- Skyrim's vanilla arrest and crime systems.
- Other scripted dialogue and NPC interaction mods.
- Large modlists such as NEFARAM.

These are compatibility targets, not confirmed integrations.

Some third-party systems may require dedicated compatibility checks or patches.

### 11.4 Important Limitation

Skyrim does not expose a single universal function identifying every important quest scene or scripted interaction.

The final implementation may require several detection methods, exclusions and compatibility-specific conditions.

---

## 12. Interaction State Management

A single central manager should coordinate guard inspections.

### Proposed State Machine

```text
IDLE
  |
  v
ELIGIBILITY_CHECK
  |
  v
GUARD_SELECTED
  |
  v
APPROACHING
  |
  v
DIALOGUE
  |
  +--> COOPERATE
  |      |
  |      +--> SEARCH
  |              |
  |              +--> CLEAN INVENTORY
  |              |        |
  |              |        v
  |              |      RELEASE
  |              |
  |              +--> ILLEGAL ITEMS
  |                       |
  |                       v
  |                   CONSEQUENCES
  |
  +--> REFUSE
  |      |
  |      +--> RECONSIDER --> COOPERATE
  |      |
  |      +--> FINAL REFUSAL --> CRIME HANDOFF
  |
  +--> NEGOTIATE
         |
         +--> ADULT INTERACTION --> RELEASE
         |
         +--> PERSUASION
                  |
                  +--> SUCCESS --> RELEASE
                  |
                  +--> FAILURE --> COOPERATE / REFUSE

ALL COMPLETED OR ABORTED PATHS
                  |
                  v
               CLEANUP
                  |
                  v
                 IDLE
```

The final technical state machine may use fewer or more internal states.

### 12.1 Cleanup Responsibilities

The cleanup process must:

- Release the selected guard.
- Release any reserved aliases or references.
- Restore temporary player control restrictions.
- Restore equipment when appropriate.
- Cancel temporary AI behavior.
- Clear active encounter flags.
- Remove temporary state data.
- Apply or preserve cooldown information as appropriate.
- Avoid interfering with an arrest or another system that has legitimately taken control.

### 12.2 Failure Recovery

The system must handle:

- Player leaving the area.
- Guard becoming unavailable.
- Combat starting during an encounter.
- Dialogue failing to begin.
- Animation framework failing to start a scene.
- Animation scene interruption.
- Quest or cell transitions.
- Unexpected encounter cancellation.

Timeouts and emergency recovery mechanisms may be required.

---

## 13. Planned MCM Settings

| Category | Setting | Proposed Default |
|---|---|---|
| General | Enable inspections | On |
| General | Female player characters only | Fixed |
| General | City-only inspections | On |
| General | Inspection probability | 12% |
| General | Maximum detection distance | 3–5 meters |
| General | Inspection cooldown | 24 in-game hours |
| Dialogue | Persuasion success chance | 50% |
| Crime | Fine per illegal item | 100 septims |
| Contraband | Detect stolen goods | On |
| Contraband | Detect skooma | On |
| Adult Framework | Enable adult alternatives | On |
| Debugging | Enable debug logging | Off |

These settings represent the initial design proposal.

Additional options may be introduced after technical feasibility testing.

---

## 14. Open Design Questions

The following decisions remain unresolved.

### Guard Detection

- Which guard factions should be supported?
- Should guards inside buildings be eligible?
- Should inspections be disabled in certain cities?
- How frequently should the detection system evaluate nearby actors?

### Player Control

- What is the safest method for temporarily restricting player movement?
- How should camera orientation be handled?
- What happens when a guard cannot reach the player?

### Inventory

- How should stolen items within mixed inventory stacks be counted?
- Should all stolen items always be confiscated?
- Where should confiscated items be transferred?
- How should quest items and special equipment be protected?

### Crime and Penalties

- Should refusal immediately trigger resisting arrest or first create a bounty?
- How should insufficient gold be handled?
- Should the fine calculation use item quantity or distinct item types?
- How should existing bounties interact with custom penalties?

### Adult Interactions

- How should appropriate SexLab animations be selected?
- Should animation tags be configurable?
- Should the player retain control of the scene selection?
- What happens if the requested animation cannot start?
- Can OStim support be implemented without complicating the core architecture?

### Compatibility

- Which mod-specific states can be reliably detected?
- Which quests or locations require explicit exclusions?
- Which compatibility patches are necessary?

These questions will be addressed during the feasibility and architecture phases.

---

## 15. Project Scope

### Initial Planned Scope

- Female player characters.
- City guards.
- Random inspections.
- Three primary dialogue choices.
- Inventory detection for stolen goods and skooma.
- Confiscation.
- Prison, fines and adult alternatives.
- SexLab integration.
- SkyUI MCM.
- Safety checks and failure recovery.

### Outside the Initial Scope

- Male player character support.
- Fully independent prison gameplay.
- New guard factions unrelated to vanilla cities.
- Custom voice acting.
- New animations developed specifically for this mod.
- Guaranteed compatibility with every existing Skyrim mod.
- Complete OStim integration in the first release.

These limitations may be reconsidered in later versions.

---

## 16. Development Status and Next Steps

**Current phase: Concept and Feasibility Study**

### Completed

- [x] Initial gameplay concept defined.
- [x] Three main dialogue branches outlined.
- [x] Inspection and confiscation goals established.
- [x] Basic consequences defined.
- [x] Female-only gameplay scope established.
- [x] Single active guard restriction agreed.
- [x] Short-range interception agreed.
- [x] Initial MCM settings proposed.
- [x] SexLab selected as the primary framework.
- [x] New implementation strategy selected.

### Not Yet Started

- [ ] Original mod source audit.
- [ ] Technical feasibility validation.
- [ ] Detailed Creation Kit architecture.
- [ ] Papyrus implementation.
- [ ] Dialogue records and quest development.
- [ ] Inventory and crime-system testing.
- [ ] SexLab integration testing.
- [ ] MCM development.
- [ ] Compatibility testing.
- [ ] Playable release.

No development schedule has been established.

---

## 17. Credits and Attribution

This project is inspired by **SexLab Body Search**.

The original permissions allow patches and derivative versions, provided that appropriate credit is given.

The original permission statement also explicitly requires credit to **Suzutsuki**.

Full author attribution, original source links and applicable permissions will be documented in a dedicated `CREDITS.md` file.

All third-party resources remain subject to their respective permissions.

---

## 18. Final Design Objective

The goal of Body Search Redux is not simply to add random guard interruptions.

It is to create a coherent and immersive interaction system that integrates naturally with Skyrim's world, respects player decisions and remains stable even in heavily modded environments.

Every encounter should have a clear beginning, meaningful choices, appropriate consequences and a reliable conclusion.

**Immersion, player choice, stability and maintainability are the project's guiding principles.**

---

*Body Search Redux — Concept Specification v0.1*

*Designed by Rhiven — November 2026*
