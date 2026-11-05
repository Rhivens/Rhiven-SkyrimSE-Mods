# 🏗️ Body Search Redux — Technical Architecture

**Project:** Body Search Redux  
**Author:** Rhiven  
**Document:** ARCHITECTURE.md  
**Version:** 0.1 — Proposed Architecture  
**Date:** November 5, 2026  
**Status:** Architecture Design / Not Implemented  
**Primary Framework:** SexLab  
**Future Framework:** OStim (under consideration)

---

## 1. Purpose

This document describes the proposed technical architecture of **Body Search Redux**, an immersive guard inspection system for Skyrim Special Edition / Anniversary Edition.

The architecture must support:

- Random guard inspections.
- Short-range guard interception.
- Automatic dialogue initiation.
- Branching player choices.
- Inventory inspection and confiscation.
- Crime and punishment outcomes.
- SexLab interactions.
- Configurable MCM settings.
- Compatibility with other scripted systems.
- Reliable recovery from interrupted encounters.

The main objective is to build a modular and maintainable system that remains stable in heavily modded environments.

**This document is a design proposal, not a description of existing implementation.**

All proposed scripts, quests, aliases and functions are subject to technical validation.

---

## 2. Architecture Principles

The following principles guide the technical design.

### 2.1 Centralized Encounter Management

A single manager controls the entire inspection process.

Only one inspection can be active at any given time.

Other components must not independently initiate new encounters.

### 2.2 Separation of Responsibilities

Guard detection, dialogue, inventory handling, crime consequences and adult animation integration should remain logically separated.

### 2.3 State-Driven Execution

Every encounter moves through explicit states.

Components should verify the current encounter state before performing operations.

### 2.4 Defensive Programming

Unexpected conditions must not leave the player immobilized, unequipped or trapped in dialogue.

### 2.5 Minimal Papyrus Overhead

The mod should avoid unnecessary polling, excessive reference scanning and uncontrolled script registrations.

### 2.6 Compatibility First

Vanilla records should be modified only when necessary.

Interactions with external mods should use dedicated integrations or compatibility mechanisms whenever possible.

---

## 3. High-Level Architecture

```text
                  BODY SEARCH REDUX
                          |
                          v
                +-------------------+
                |   MAIN MANAGER    |
                | Encounter Control |
                +-------------------+
                          |
           +--------------+--------------+
           |              |              |
           v              v              v
    +------------+ +-------------+ +-------------+
    | Detection  | | Eligibility | | MCM Settings|
    | & Guards   | | & Safety    | |             |
    +------------+ +-------------+ +-------------+
           |              |
           +------+-------+
                  |
                  v
           +-------------+
           | Guard Alias |
           | AI Package  |
           +-------------+
                  |
                  v
           +-------------+
           | Dialogue    |
           | Controller  |
           +-------------+
                  |
        +---------+----------+
        |         |          |
        v         v          v
    COOPERATE   REFUSE    NEGOTIATE
        |         |          |
        v         v          v
    Inventory   Crime     Persuasion
    Inspection  Handoff   / Adult
        |                    |
        v                    v
    Confiscation         SexLab Adapter
        |                    |
        +---------+----------+
                  |
                  v
           +-------------+
           |   CLEANUP   |
           | & RECOVERY  |
           +-------------+
                  |
                  v
                 IDLE
```

This diagram represents logical responsibilities, not necessarily one Papyrus script per box.

Some functions may ultimately be combined or implemented using Creation Kit conditions rather than separate scripts.

---

## 4. Proposed Creation Kit Structure

### 4.1 Main Quest

**Proposed EditorID:** `RHI_BSR_MainQuest`

**Purpose:** Coordinate guard inspections and maintain persistent configuration and encounter state.

Proposed responsibilities:

- Initialize the system.
- Enable or disable inspection monitoring.
- Maintain the active inspection state.
- Reserve the selected guard.
- Coordinate dialogue and outcome processing.
- Apply cooldowns.
- Recover from interruptions.

The quest should remain available throughout gameplay while avoiding unnecessary repeated processing.

The precise quest flags and startup mechanism will be selected during implementation.

### 4.2 Dialogue Quest

**Proposed EditorID:** `RHI_BSR_DialogueQuest`

**Purpose:** Organize dialogue topics, responses and conditions.

Potential responsibilities:

- Initial guard interception dialogue.
- Cooperation.
- Final refusal.
- Persuasion.
- Adult proposition.
- Inspection results.
- Consequences.
- Release dialogue.

A separate dialogue quest may simplify organization, but this separation is not mandatory.

The decision should be revisited after the first Creation Kit prototype.

### 4.3 MCM Configuration

**Proposed script:** `RHI_BSR_MCM`

A SkyUI MCM script will expose configurable settings and communicate changes to the central manager.

The configuration system must not directly initiate encounters.

---

## 5. Proposed Reference Aliases

### 5.1 Player Alias

**Alias:** `PlayerAlias`

**Reference:** PlayerRef

Purpose:

- Access the player.
- Support dialogue and quest conditions.
- Associate player-related quest functionality.

### 5.2 Active Guard Alias

**Alias:** `ActiveGuardAlias`

Purpose:

- Hold the guard currently assigned to an inspection.
- Support ForceGreet packages.
- Support dialogue conditions.
- Provide the actor reference for adult interactions.
- Allow controlled release during cleanup.

### 5.3 Alias Safety

The active guard reference must be cleared when an encounter ends.

Alias filling and clearing operations must be tested to ensure that they do not interfere with other quests or the guard's normal AI behavior.

The manager must not silently replace an active guard during an encounter.

---

## 6. Guard Detection System

**Proposed component:** `RHI_BSR_Detection`

### 6.1 Responsibilities

- Determine whether the player is in an eligible city.
- Identify suitable nearby guards.
- Evaluate distance restrictions.
- Exclude unavailable actors.
- Request authorization from the encounter manager.

### 6.2 Detection Range

Target: approximately **3–5 meters**.

The corresponding game-unit distance must be calibrated during testing.

The system should avoid selecting guards behind walls, in inaccessible areas or on unrelated vertical levels whenever practical.

### 6.3 Detection Strategy

A hybrid activation model is proposed:

1. Detect entry into an eligible city or location.
2. Enable controlled inspection eligibility checks.
3. Identify a nearby guard candidate.
4. Verify guard and player availability.
5. Evaluate the configured inspection probability.
6. Request encounter reservation.

The actual actor discovery implementation remains unresolved.

### 6.4 Detection Restrictions

No guard selection should occur when:

- The mod is disabled.
- An encounter is already active.
- The player is outside eligible locations.
- The cooldown has not expired.
- The player is in combat.
- A conflicting scripted interaction is detected.

The selected guard must also pass independent eligibility checks.

---

## 7. Encounter Manager

**Proposed component:** `RHI_BSR_Manager`

This is the central component of the architecture.

### 7.1 Responsibilities

- Maintain the global encounter state.
- Authorize new inspections.
- Reserve one guard.
- Coordinate approach and dialogue.
- Track player decisions.
- Initiate inspection and outcome processing.
- Coordinate cleanup.
- Apply cooldowns.
- Recover from interrupted encounters.

### 7.2 Single Encounter Rule

Only one active inspection may exist at a time.

The manager must verify the current state before accepting a new encounter request.

Proposed conceptual interface:

```text
CanStartEncounter()
ReserveGuard(guard)
StartEncounter()
SetEncounterState(state)
CompleteEncounter()
AbortEncounter(reason)
```

These are design-level function names, not verified Papyrus implementations.

### 7.3 Encounter Reservation

When an encounter begins:

1. Validate eligibility.
2. Reserve the selected guard.
3. Mark the encounter as active.
4. Prepare aliases and AI behavior.
5. Begin the approach phase.

If any operation fails, the manager must initiate safe cleanup.

### 7.4 Reentrancy Protection

Scripts and callbacks must not start a second encounter while the first is active.

Delayed callbacks must verify that they still belong to the current encounter.

An encounter identifier or equivalent validation mechanism may be used to reject stale callbacks.

---

## 8. Encounter State Machine

**Proposed component:** `RHI_BSR_State`

The state machine should be managed centrally.

### 8.1 Proposed States

| State | Meaning |
|---|---|
| `IDLE` | No active inspection |
| `VALIDATING` | Eligibility checks |
| `RESERVED` | Guard selected and reserved |
| `APPROACHING` | Guard moving toward the player |
| `DIALOGUE` | Player interacting with guard |
| `SEARCHING` | Inspection in progress |
| `CONFISCATING` | Illegal items being processed |
| `NEGOTIATING` | Persuasion or adult choice processing |
| `ADULT_SCENE` | SexLab interaction active or pending |
| `CRIME_HANDOFF` | Vanilla criminal response being invoked |
| `RELEASING` | Normal completion |
| `ABORTING` | Encounter interrupted |
| `CLEANUP` | Temporary state restoration |

These are preliminary logical states.

The final implementation may combine or subdivide them.

### 8.2 State Transition Rules

Each state should permit only explicitly supported transitions.

Examples:

- `IDLE` → `VALIDATING`
- `VALIDATING` → `RESERVED`
- `RESERVED` → `APPROACHING`
- `APPROACHING` → `DIALOGUE`
- `DIALOGUE` → `SEARCHING`
- `DIALOGUE` → `NEGOTIATING`
- `SEARCHING` → `CONFISCATING`
- `NEGOTIATING` → `ADULT_SCENE`
- `ADULT_SCENE` → `RELEASING`
- `RELEASING` → `CLEANUP`
- `ABORTING` → `CLEANUP`
- `CLEANUP` → `IDLE`

Some transitions depend on gameplay outcomes.

### 8.3 Invalid Transitions

Unexpected transitions should be rejected or handled safely.

For example:

- Starting a second inspection during `SEARCHING`.
- Launching another adult scene during `ADULT_SCENE`.
- Confiscating items after the encounter has ended.
- Attempting to release an unrelated guard.

---

## 9. Guard Approach and ForceGreet

**Proposed component:** `RHI_BSR_GuardController`

### 9.1 Responsibilities

- Apply the guard's temporary interaction package.
- Coordinate approach behavior.
- Monitor whether the guard can reach the player.
- Trigger the greeting dialogue.
- Report failure or success to the manager.

### 9.2 Proposed Mechanism

A dedicated Creation Kit **ForceGreet AI package** is the primary candidate.

Potential package EditorID:

`RHI_BSR_ForceGreetPackage`

### 9.3 Interaction Sequence

```text
Guard reserved
      |
      v
Apply interaction package
      |
      v
Guard approaches player
      |
      v
Check distance and availability
      |
      v
Initiate greeting dialogue
      |
      v
Transfer control to dialogue system
```

### 9.4 Timeouts

The approach phase must not remain active indefinitely.

If the guard fails to reach the player or the dialogue cannot begin, the encounter should be cancelled.

### 9.5 Movement and Camera

Temporary player control restrictions may be needed.

The method must be Skyrim-specific and reversible.

Camera adjustments should be minimal and must not interfere with other camera modifications.

The Fallout 4 mod **NORA Dangerous Nights** serves as a gameplay design reference, not a source of directly reusable Skyrim scripting functions.

---

## 10. Dialogue Controller

**Proposed component:** `RHI_BSR_Dialogue`

### 10.1 Responsibilities

- Evaluate player dialogue choices.
- Track persuasion attempts.
- Prevent repeated failed persuasion attempts.
- Notify the manager of selected outcomes.
- Trigger inventory checks or adult interactions.
- Ensure dialogue branches match the gameplay concept.

### 10.2 Initial Choices

```text
GUARD REQUESTS INSPECTION
          |
          +--> COOPERATE
          |
          +--> REFUSE
          |
          +--> NEGOTIATE
```

### 10.3 Immediate Adult Proposition

If the player offers an adult favor before attempting persuasion:

- The guard accepts.
- No inspection occurs.
- No inventory confiscation occurs.
- A SexLab scene is requested.
- The player is released afterward.

### 10.4 Persuasion

Initial success chance: **50%**, configurable through MCM.

Successful persuasion:

- No inspection.
- No confiscation.
- Immediate release.

Failed persuasion:

- Persuasion becomes unavailable.
- The player may cooperate.
- The player may refuse.
- The player may offer an adult favor with mandatory inspection.

### 10.5 Adult Proposition After Failed Persuasion

If the player offers an adult favor after failing persuasion:

1. The guard accepts the proposal.
2. The player submits to inspection.
3. Illegal goods are identified and confiscated.
4. A SexLab scene is initiated.
5. The player is released.
6. No additional fine or imprisonment is imposed.

### 10.6 Adult Proposition After Confiscation

If illegal items were discovered during a normal inspection:

- Previously confiscated items remain confiscated.
- The adult proposition replaces additional criminal penalties.
- The player is released after the SexLab interaction.

### 10.7 Dialogue State Tracking

Possible values include:

```text
PersuasionAttempted
PersuasionSucceeded
PersuasionFailed
InspectionCompleted
IllegalGoodsDetected
ConfiscationCompleted
AdultAlternativeSelected
```

These values should be reset for each new encounter.

Persistent state should be limited to information that must survive save/load.

---

## 11. Inventory Controller

**Proposed component:** `RHI_BSR_Inventory`

### 11.1 Responsibilities

- Inspect the player's inventory.
- Detect stolen goods.
- Detect supported contraband.
- Count illegal item quantities.
- Avoid duplicate counting.
- Report inspection results.
- Perform controlled confiscation.
- Preserve necessary information for fines.

### 11.2 Stolen Goods

Potential mechanisms:

- Native Skyrim stolen-item functionality.
- `RemoveAllStolenItems()`.
- Steelfeathers' Papyrus Extensions.
- Additional SKSE inventory functionality where necessary.

The exact approach will depend on prototype results.

### 11.3 Contraband

A dedicated `FormList` should contain supported contraband.

Initial support:

- Skooma.

Future additions may include modded substances.

### 11.4 Inventory Result

Proposed logical result:

```text
InspectionResult
    |
    +--> StolenItemCount
    +--> ContrabandCount
    +--> TotalIllegalCount
    +--> IllegalGoodsDetected
    +--> ConfiscationStatus
```

This structure is conceptual.

Papyrus does not necessarily provide the same data structures as general-purpose programming languages.

### 11.5 Counting Rules

The system must count individual items rather than only distinct form types.

Stolen contraband must not be counted twice.

Mixed stolen and legitimate stacks require special handling and validation.

### 11.6 Confiscation

The preferred approach is to use native item-handling functions where practical.

Potential methods include:

- `RemoveAllStolenItems()`.
- `RemoveItem()`.
- Transfers to a controlled container.

The final method must not be selected until testing confirms its behavior with mixed stacks, protected objects and quest items.

### 11.7 Transaction Safety

Inventory inspection should follow a predictable sequence:

1. Validate encounter state.
2. Inspect inventory.
3. Calculate item quantities.
4. Record expected confiscation data.
5. Perform confiscation.
6. Verify the result where possible.
7. Report completion or failure.

A failed inventory operation must not silently continue as a successful confiscation.

---

## 12. Equipment Controller

**Proposed component:** `RHI_BSR_Equipment`

### 12.1 Purpose

Manage temporary equipment removal and restoration during an inspection.

### 12.2 Responsibilities

- Record equipped clothing and armor.
- Unequip permitted items.
- Track items removed by Body Search Redux.
- Restore eligible equipment afterward.
- Avoid interfering with externally controlled equipment.

### 12.3 Special Cases

The system must account for:

- Weapons.
- Armor and clothing slots.
- Modded equipment.
- Quest-protected items.
- Devious Devices.
- Equipment changes caused by SexLab.
- Equipment changes caused by other mods.

### 12.4 Restoration Policy

Restore equipment only when doing so is safe.

The mod must not:

- Re-equip confiscated items.
- Override an active restraint system.
- Replace equipment selected by another mod.
- Re-equip items that no longer exist in the player's inventory.

The exact equipment snapshot mechanism requires testing.

---

## 13. Crime and Consequences Controller

**Proposed component:** `RHI_BSR_Crime`

### 13.1 Responsibilities

- Process final refusal.
- Calculate financial penalties.
- Deduct gold when appropriate.
- Initiate validated jail or arrest behavior.
- Preserve unrelated crime information.
- Coordinate handoff to vanilla or compatible external systems.

### 13.2 Fine Calculation

Initial proposal:

```text
Fine = Illegal Item Quantity × Fine Per Item
```

Default amount:

`100 septims per illegal item`

The fine must use quantities recorded before confiscation.

### 13.3 Imprisonment

Vanilla Skyrim crime and jail systems should be preferred.

The exact API calls and conditions must be validated.

### 13.4 Final Refusal

Final refusal should trigger a believable guard or crime response.

Uncontrolled permanent faction hostility must be avoided.

The final implementation may need to hand responsibility over to an existing vanilla crime interaction.

### 13.5 Existing Bounties

Body Search Redux must not accidentally clear unrelated bounties or modify unrelated crime records.

A custom inspection fine should remain logically separate from standard bounty payment unless integration is explicitly intended and tested.

### 13.6 Insufficient Gold

The behavior remains undecided.

Potential options:

- Imprisonment.
- Alternative penalty.
- Other configurable outcome.

No implementation decision has been finalized.

---

## 14. Adult Framework Integration

**Proposed component:** `RHI_BSR_AdultBridge`

### 14.1 Design Objective

Keep adult animation framework logic separate from guard inspection gameplay.

The central manager should request a scene through a dedicated interface.

### 14.2 Conceptual Interface

```text
IsFrameworkAvailable()
CanStartScene(player, guard)
StartScene(player, guard)
OnSceneStarted()
OnSceneCompleted()
OnSceneFailed()
OnSceneInterrupted()
```

These names represent desired behavior rather than confirmed Papyrus API functions.

### 14.3 Initial SexLab Adapter

**Proposed component:** `RHI_BSR_SexLab`

Responsibilities:

- Access the SexLab framework.
- Verify actor eligibility.
- Select suitable animations.
- Request scene initialization.
- Detect actual scene startup.
- Receive completion information.
- Report results to the manager.

### 14.4 Scene Completion

The manager must not assume a scene has completed merely because a start request succeeded.

A completion callback or other validated framework mechanism is required.

### 14.5 Failure Recovery

Possible failures:

- Framework unavailable.
- Missing animations.
- Actor unavailable.
- Scene startup failure.
- Scene interruption.
- Unexpected actor state.

A failure must not leave the encounter permanently active.

The gameplay consequence of an unsuccessful adult scene remains an open design decision.

### 14.6 Future OStim Adapter

A future component may implement equivalent behavior using OStim.

**Proposed component:** `RHI_BSR_OStim`

OStim support is not guaranteed and is outside the initial release scope.

---

## 15. Eligibility and Safety Controller

**Proposed component:** `RHI_BSR_Safety`

### 15.1 Responsibilities

Evaluate whether an inspection can safely begin or continue.

### 15.2 Player Checks

The system should verify:

- Female player character.
- Eligible city or location.
- Player alive and available.
- No active combat.
- No incompatible dialogue.
- No arrest or imprisonment in progress.
- No conflicting adult scene.
- No detected defeat or captivity state.
- No known protected scripted interaction.
- Cooldown expired.

### 15.3 Guard Checks

The selected guard should be:

- Alive.
- Enabled.
- Available.
- A valid guard candidate.
- Within the configured distance.
- Not in combat.
- Not participating in an incompatible scene.

### 15.4 Validation Points

Eligibility checks must occur:

1. Before guard selection.
2. Before guard reservation.
3. Immediately before dialogue.
4. Before initiating an adult scene.
5. Before applying significant inventory or crime operations, where appropriate.

### 15.5 Important Limitation

Skyrim does not provide a universal mechanism for identifying every important scripted quest interaction.

The safety controller can reduce interference but cannot guarantee compatibility with every third-party scene.

Known conflicts may require explicit exclusions or compatibility patches.

---

## 16. Cleanup and Recovery Controller

**Proposed component:** `RHI_BSR_Cleanup`

This is one of the most important components of the architecture.

### 16.1 Responsibilities

Cleanup should:

- Release temporary guard reservations.
- Reset temporary encounter variables.
- Remove temporary AI package influence when appropriate.
- Restore player controls when owned by Body Search Redux.
- Restore eligible equipment.
- Cancel pending encounter operations.
- Clear temporary references.
- Return the manager to a valid state.

### 16.2 Ownership-Aware Cleanup

Body Search Redux must only undo changes that it owns.

For example, if another mod legitimately takes control of the player during an arrest or defeat sequence, cleanup must not override that system.

### 16.3 Cancellation Conditions

Potential cancellation triggers include:

- Guard becomes unavailable.
- Player enters combat.
- Guard enters combat.
- Player leaves the location.
- Dialogue fails.
- Adult scene cannot start.
- Encounter exceeds an allowed timeout.
- Another system takes control.

### 16.4 Recovery States

Normal completion and emergency cancellation should share common cleanup logic where appropriate.

However, cleanup must distinguish between:

- Normal release.
- Failed encounter.
- Vanilla crime handoff.
- Adult scene completion.
- External takeover.

### 16.5 Emergency Reset

An MCM recovery option may be considered.

Such a feature should clear Body Search Redux's own temporary state without disrupting unrelated gameplay systems.

---

## 17. Save and Load Handling

**Status:** Requires validation.

### 17.1 Risk

A player may save or load during:

- Guard approach.
- Dialogue.
- Inventory inspection.
- Equipment removal.
- SexLab interaction.
- Cleanup.

### 17.2 Proposed Strategy

Persistent encounter information should be kept minimal and consistent.

On load, the system should evaluate whether the active encounter can safely resume.

If not, it should perform controlled recovery.

### 17.3 Important Consideration

Papyrus state, active dialogues, AI packages and animation callbacks may not all recover identically after loading a save.

The final recovery policy must be established through dedicated tests.

---

## 18. MCM Architecture

**Proposed component:** `RHI_BSR_MCM`

### 18.1 General Settings

- Enable or disable inspections.
- Inspection probability.
- Maximum detection distance.
- Cooldown duration.
- Enable city-only restrictions.

### 18.2 Dialogue Settings

- Persuasion success probability.
- Enable adult alternatives.

### 18.3 Inventory Settings

- Enable stolen goods detection.
- Enable skooma detection.
- Fine per illegal item.

### 18.4 Debug Settings

- Enable debug logging.
- Display encounter state.
- Optional manual recovery.
- Optional test mode.

### 18.5 Configuration Changes

Changing settings during an active encounter should not invalidate ongoing state unexpectedly.

Some changes may need to apply only after the current encounter finishes.

---

## 19. Proposed Script Responsibilities

The following is an initial list of logical Papyrus components.

| Proposed Script | Responsibility |
|---|---|
| `RHI_BSR_Manager.psc` | Central encounter coordinator |
| `RHI_BSR_Detection.psc` | Guard selection and proximity |
| `RHI_BSR_GuardController.psc` | AI approach and ForceGreet |
| `RHI_BSR_Dialogue.psc` | Dialogue outcomes |
| `RHI_BSR_Inventory.psc` | Inventory detection and confiscation |
| `RHI_BSR_Equipment.psc` | Equipment removal and restoration |
| `RHI_BSR_Crime.psc` | Fines and arrest handling |
| `RHI_BSR_Safety.psc` | Eligibility and conflict checks |
| `RHI_BSR_AdultBridge.psc` | Adult integration interface |
| `RHI_BSR_SexLab.psc` | SexLab implementation |
| `RHI_BSR_Cleanup.psc` | Recovery and release |
| `RHI_BSR_MCM.psc` | SkyUI configuration |

**Important:** These are proposed responsibilities and filenames, not final script declarations.

Some components may be implemented as quest scripts, alias scripts, utility scripts, CK dialogue fragments or combinations of these.

Papyrus does not support conventional object-oriented interfaces in the same manner as languages such as C# or Java.

The actual script organization must reflect Papyrus capabilities and Creation Kit requirements.

---

## 20. Proposed EditorID Naming Convention

Use a consistent prefix to avoid conflicts with other mods.

**Prefix:** `RHI_BSR_`

Examples:

```text
RHI_BSR_MainQuest
RHI_BSR_DialogueQuest
RHI_BSR_ForceGreetPackage
RHI_BSR_GuardFaction
RHI_BSR_ContrabandList
RHI_BSR_ConfiscationChest
RHI_BSR_Manager
RHI_BSR_MCM
```

These identifiers are illustrative.

Not every proposed record is necessarily required.

---

## 21. Performance Strategy

The architecture should minimize continuous processing.

### 21.1 Recommended Behavior

- Activate inspection monitoring only in eligible locations.
- Use controlled detection intervals where necessary.
- Avoid repeated full inventory scans.
- Perform inventory checks only during inspections.
- Stop unnecessary detection during active encounters.
- Avoid duplicate event registrations.
- Clear aliases and temporary state after completion.

### 21.2 Diagnostics

Debug builds should record important events:

```text
[BSR] Encounter eligibility passed
[BSR] Guard selected
[BSR] Guard reserved
[BSR] Approach started
[BSR] Dialogue started
[BSR] Persuasion failed
[BSR] Inspection started
[BSR] Confiscation completed
[BSR] SexLab scene started
[BSR] SexLab scene completed
[BSR] Cleanup completed
```

Debug logging should be configurable and disabled by default in normal gameplay.

### 21.3 Performance Validation

Future tests should examine:

- Papyrus log volume.
- Repeated update registrations.
- Script latency.
- Encounter initiation delays.
- Behavior in crowded cities.
- Stability under NEFARAM.

---

## 22. Development and Integration Order

The development process should follow the independent prototypes defined in `FEASIBILITY.md`.

### Phase A — Foundation

- Create plugin.
- Create main quest.
- Configure player and guard aliases.
- Establish basic manager state.
- Implement debugging.

### Phase B — Guard Interaction

- Select nearby guard.
- Reserve guard.
- Apply ForceGreet.
- Start dialogue.
- Test cancellation and recovery.

### Phase C — Dialogue Logic

- Implement cooperation.
- Implement refusal.
- Implement persuasion.
- Implement adult proposition branches.
- Validate all conditions.

### Phase D — Inventory and Crime

- Detect stolen goods.
- Detect skooma.
- Count items.
- Confiscate items.
- Calculate fines.
- Integrate vanilla crime outcomes.

### Phase E — SexLab

- Connect SexLab adapter.
- Test scene initiation.
- Test callbacks.
- Handle scene interruption.
- Validate cleanup.

### Phase F — MCM

- Implement configuration.
- Connect settings to manager.
- Add debugging and recovery options.

### Phase G — Compatibility

- Test with minimal dependencies.
- Introduce Acheron.
- Introduce PAMA.
- Introduce Devious Devices.
- Test in NEFARAM.
- Fix identified conflicts.

---

## 23. Architecture Validation Checklist

Before considering the architecture stable:

- [ ] Only one guard can initiate an inspection.
- [ ] Detection range behaves as expected.
- [ ] Guards approach naturally.
- [ ] Dialogue starts reliably.
- [ ] Encounter cancellation restores normal controls.
- [ ] Persuasion can only be attempted once per encounter.
- [ ] Immediate adult proposition skips inspection.
- [ ] Failed persuasion followed by an adult proposition forces inspection.
- [ ] Illegal items are counted correctly.
- [ ] Confiscation does not remove unrelated items.
- [ ] Adult alternatives preserve the correct confiscation rules.
- [ ] Custom fines do not corrupt vanilla bounty behavior.
- [ ] SexLab scene completion is handled correctly.
- [ ] Active encounters cannot overlap.
- [ ] Equipment restoration respects other mods.
- [ ] Saving and loading do not cause permanent locks.
- [ ] Known quest interactions are protected.
- [ ] Papyrus activity remains controlled.
- [ ] NEFARAM compatibility has been evaluated.

---

## 24. Open Architectural Decisions

The following questions must be resolved before implementation is finalized.

### Detection

- How will nearby guards be enumerated efficiently?
- Which location conditions will activate detection?
- How should inaccessible guards be excluded?

### Player Control

- Which Skyrim-specific mechanism should temporarily restrict movement?
- Is explicit camera control necessary?

### Inventory

- Can native stolen-item confiscation preserve mixed stacks correctly?
- How should exact stolen quantities be obtained?
- Where should confiscated goods be stored?

### Crime

- How should final refusal be translated into vanilla crime behavior?
- How should existing bounties be preserved?
- What happens when the player cannot afford the fine?

### Adult Integration

- Which SexLab API and callbacks are appropriate?
- How should animation selection work?
- What should happen when a scene cannot start?

### Compatibility

- Which external framework states can be detected reliably?
- Which compatibility adapters will be necessary?

### Recovery

- What state should survive save/load?
- Which interrupted encounters can resume?
- When should the manager abort instead of resume?

---

## 25. Conclusion

The proposed architecture favors:

- A single central encounter manager.
- Dedicated Creation Kit quests and aliases.
- Explicit state management.
- Modular inventory and crime handling.
- A separate SexLab integration component.
- Defensive cleanup and recovery.
- Minimal unnecessary Papyrus processing.

The architecture is intentionally conservative.

Complexity should be introduced only when a validated gameplay requirement justifies it.

The first technical priority will be to prove reliable guard selection, ForceGreet interaction and safe player control restoration.

Once that foundation is stable, inventory inspection, consequences and adult-framework integration can be added incrementally.

**The objective is not merely to make Body Search Redux work, but to make it reliable, maintainable and compatible with heavily modded Skyrim installations.**

---

*Body Search Redux — Technical Architecture v0.1*

*Designed by Rhiven — November 2026*
