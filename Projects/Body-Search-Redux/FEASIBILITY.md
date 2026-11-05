# 🔬 Body Search Redux — Technical Feasibility Study

**Project:** Body Search Redux  
**Author:** Rhiven  
**Document:** FEASIBILITY.md  
**Version:** 0.1 — Preliminary Technical Study  
**Date:** November 5, 2026  
**Status:** Research / Feasibility Study  
**Development:** Not Started

---

## 1. Purpose

This document examines the technical feasibility of implementing **Body Search Redux** for Skyrim Special Edition / Anniversary Edition.

The project introduces randomized city guard inspections, branching dialogue, inventory searches, confiscation, criminal penalties and optional SexLab interactions.

The goal of this study is to identify:

- Available Skyrim and Papyrus functionality.
- Creation Kit mechanisms.
- SKSE requirements.
- Potential external dependencies.
- Technical limitations.
- Compatibility risks.
- Required prototypes and tests.

This document does not represent a completed implementation or a guarantee of compatibility.

The functional specification is maintained separately in `Concept.md`.

---

## 2. Feasibility Status Definitions

Each technical component will be classified using the following categories:

| Status | Meaning |
|---|---|
| VERIFIED API | The relevant function or mechanism is documented |
| FEASIBLE IN PRINCIPLE | A plausible implementation approach exists |
| REQUIRES TESTING | Actual behavior must be validated in game |
| REQUIRES RESEARCH | Technical implementation has not been established |
| POTENTIAL RISK | Known or suspected compatibility/stability concern |

**Important:** A documented API is not equivalent to a fully tested gameplay feature.

No Body Search Redux gameplay functionality has been implemented or tested yet.

---

## 3. Target Environment

### 3.1 Supported Game Versions

Planned targets:

- Skyrim Special Edition.
- Skyrim Anniversary Edition.

Exact runtime compatibility will depend on the versions supported by SKSE and external native plugins.

Support must be validated against specific Skyrim runtime versions before release.

### 3.2 Development Tools

| Tool | Planned Use |
|---|---|
| Creation Kit | Quests, dialogue, aliases and AI packages |
| Papyrus Compiler | Gameplay scripting |
| SKSE64 | Additional scripting functionality |
| SSEEdit / xEdit | Plugin analysis and conflict inspection |
| Mod Organizer 2 | Mod installation and testing |
| SkyUI | MCM configuration |
| Visual Studio Code | Script and documentation editing |
| GitHub | Source control and documentation |

### 3.3 Main Test Environment

The NEFARAM modlist is a planned compatibility and stress-testing environment.

Testing should also be performed using a smaller development profile.

A minimal test environment is essential for distinguishing mod bugs from conflicts with unrelated modifications.

---

## 4. Guard Detection

**Status:** FEASIBLE IN PRINCIPLE — REQUIRES TESTING

### 4.1 Objective

Detect an available city guard within approximately 3–5 meters of the female player character.

The guard must already be nearby.

The system should not cause guards to pursue the player across large areas solely to initiate an inspection.

### 4.2 Available Functionality

Skyrim provides actor and reference functionality suitable for evaluating candidate guards.

Examples include:

- `Actor.IsGuard()`
- `Actor.IsInCombat()`
- `ObjectReference.GetDistance()`
- Faction membership checks.
- Quest aliases and Creation Kit conditions.

`Actor.IsGuard()` is a documented Skyrim Papyrus function.

However, identifying a guard is only part of the problem.

The system must also identify nearby actors efficiently.

### 4.3 Candidate Detection Approaches

**Approach A — Quest and Alias System**

Use a dedicated quest with aliases and appropriate conditions to identify suitable actors.

Advantages:

- Integrates with Creation Kit systems.
- Can centralize actor reservations.
- Supports quest-based dialogue and packages.

Limitations:

- Alias selection and refresh behavior require testing.
- Nearby guard selection is not automatically solved by an alias.

**Approach B — Event-Driven Detection**

Use appropriate gameplay events or location transitions to activate a lightweight detection process.

Advantages:

- Avoids unnecessary processing while outside supported areas.
- May reduce repeated script activity.

Limitations:

- An efficient and reliable source of nearby guard candidates remains necessary.

**Approach C — Controlled Periodic Checks**

Evaluate inspection eligibility at configurable intervals while the player is in an eligible city.

Advantages:

- Predictable behavior.
- Relatively straightforward to prototype.

Limitations:

- Excessive polling can become inefficient.
- Detection logic must avoid expensive operations.

### 4.4 Preliminary Recommendation

Use a hybrid approach combining:

- Location-based activation.
- Lightweight periodic eligibility checks.
- A clearly defined guard selection mechanism.
- One globally reserved guard per encounter.

The exact discovery mechanism must be selected after prototype testing.

### 4.5 Required Tests

- Detection in Whiterun.
- Detection in Solitude.
- Detection in Windhelm.
- Detection in Riften.
- Detection in Markarth.
- Guards behind walls or inaccessible obstacles.
- Guards on different vertical levels.
- Multiple guards within detection range.
- Guards currently participating in other scenes.
- Detection under heavily modded conditions.

**Important:** Distance alone must not determine whether a guard can realistically approach the player.

---

## 5. Guard Approach and Automatic Dialogue

**Status:** VERIFIED MECHANISM — REQUIRES INTEGRATION TESTING

### 5.1 Objective

Once a valid guard has been selected:

1. Reserve the guard.
2. Verify player availability.
3. Make the guard approach the player.
4. Initiate dialogue automatically.
5. Temporarily restrict player movement if necessary.
6. Restore control after the interaction.

### 5.2 Creation Kit Approach

A dedicated AI package using the **ForceGreet** mechanism is a strong candidate for initiating dialogue.

The Creation Kit supports ForceGreet packages and quest dialogue branches.

This approach is preferable to assuming that simply calling an actor interaction function will reproduce a complete dialogue encounter.

### 5.3 Required Components

Potential components include:

- Main quest.
- Player reference.
- Selected guard reference alias.
- ForceGreet package.
- Blocking dialogue branch.
- Dialogue conditions.
- Papyrus encounter manager.

### 5.4 Player Movement Restrictions

The intended behavior is similar to the interaction system developed for the Fallout 4 mod **NORA Dangerous Nights**.

However, Skyrim and Fallout 4 use different scripting environments.

The Fallout 4 implementation must not be copied directly.

Possible Skyrim-specific approaches require investigation.

Any movement restriction must be temporary and reversible.

### 5.5 Failure Scenarios

The implementation must recover when:

- The guard cannot reach the player.
- Dialogue fails to start.
- The player enters combat.
- The guard enters combat.
- The player leaves the area.
- The guard becomes unavailable.
- The encounter is interrupted by another scripted event.

### 5.6 Preliminary Recommendation

Prototype the complete guard approach and dialogue system before implementing inventory inspection or SexLab integration.

A basic successful ForceGreet encounter would establish one of the most important technical foundations of the mod.

---

## 6. Single Active Guard Restriction

**Status:** FEASIBLE IN PRINCIPLE

Only one guard may initiate or conduct a Body Search inspection at a time.

This restriction should be enforced centrally.

### Proposed Mechanism

The encounter manager maintains:

- Active encounter state.
- Reserved guard reference.
- Encounter identification or equivalent state information.
- Cooldown information.

Before reserving a guard, the manager verifies that no other inspection is active.

The system must prevent duplicate activation caused by overlapping events or callbacks.

### Important Consideration

A single boolean is not sufficient as the complete recovery strategy.

The implementation should distinguish:

- No active encounter.
- Guard selected.
- Guard approaching.
- Dialogue active.
- Search active.
- Adult scene active.
- Cleanup in progress.

This reduces the risk of contradictory or abandoned states.

---

## 7. Stolen Item Detection

**Status:** VERIFIED API — REQUIRES INVENTORY TESTING

### 7.1 Objective

Identify items recognized by Skyrim as stolen.

This should correspond as closely as possible to the stolen-property information exposed by the game's inventory system.

### 7.2 Skyrim's Ownership System

Skyrim maintains ownership information for inventory objects.

The stolen status is not simply a permanent property of the base item.

Identical base items may exist with different ownership histories.

This distinction is important when working with stacked inventory items.

### 7.3 Steelfeathers' Papyrus Extensions

The following function is documented:

```papyrus
bool function IsFormStolen(
    ObjectReference inventoryObjRef,
    Form item
) global native
```

Example:

```papyrus
bool isStolen = SteelfeathersPapyrusExtensions.IsFormStolen(PlayerRef, itemForm)
```

This function provides a promising mechanism for determining whether an inventory form is stolen.

**Important limitation:**

The behavior with mixed inventory stacks and item instances must be tested.

A boolean result alone should not be assumed to provide the exact number of stolen instances within a stack.

### 7.4 Dependency Decision

Steelfeathers' Papyrus Extensions is an accepted potential dependency.

It must be documented in the mod requirements if used.

The DLL must not be redistributed unless its license and permissions explicitly permit it.

### 7.5 Required Tests

- One stolen item.
- Several stolen items.
- Identical stolen and legitimate items.
- Stolen enchanted equipment.
- Stolen items inside stacks.
- Items stolen from different owners.
- Items transferred between inventories.
- Quest-protected objects.
- Items added or modified by other mods.

---

## 8. Stolen Item Confiscation

**Status:** VERIFIED API — REQUIRES TESTING

### 8.1 Native Skyrim Function

Skyrim provides:

```papyrus
PlayerRef.RemoveAllStolenItems(ConfiscationChest)
```

The documented function is:

```papyrus
void function RemoveAllStolenItems(
    ObjectReference akTransferTo = None
) native
```

This removes stolen items from the specified inventory and optionally transfers them to another container.

### 8.2 Advantages

- Uses native inventory handling.
- Avoids manually evaluating every object for basic confiscation.
- May reduce implementation complexity.
- Supports transferring confiscated goods into a designated container.

### 8.3 Limitations

The following behaviors remain unverified:

- Exact handling of mixed stolen and legitimate stacks.
- Behavior involving quest items.
- Whether transfer changes ownership information.
- Interaction with inventory management mods.
- Exact quantity accounting.
- Retrieval of transferred property.
- Interactions with Skyrim's normal jail confiscation system.

### 8.4 Important Design Concern

`RemoveAllStolenItems()` may be suitable for performing confiscation, but it does not by itself solve the fine calculation problem.

The mod needs to know the number of illegal items before calculating any penalty.

The relationship between detection, counting and confiscation must therefore be tested carefully.

### 8.5 Preliminary Recommendation

Investigate native confiscation first.

Do not introduce a complex custom inventory transfer system unless native behavior proves insufficient.

Use a controlled test chest during development.

Never permanently delete items during prototype testing.

---

## 9. Contraband Detection

**Status:** FEASIBLE IN PRINCIPLE

### 9.1 Initial Contraband

The initial supported substance will be:

- Skooma.

### 9.2 Proposed Implementation

Use a dedicated `FormList` containing supported contraband forms.

Inventory checks can evaluate these forms using standard inventory functions.

For example, `GetItemCount()` can be used for known form references.

### 9.3 Potential Extensions

Later versions may support:

- Additional skooma variants.
- Illegal substances introduced by other mods.
- Configurable contraband FormLists.

### 9.4 Limitations

The game does not provide a universal classification covering every illegal substance introduced by third-party mods.

Additional modded substances must be explicitly recognized or incorporated through a supported configuration mechanism.

### 9.5 Confiscation Strategy

Contraband may require controlled `RemoveItem()` operations rather than the stolen-item confiscation function.

The implementation must avoid counting or confiscating the same item twice if an item is both stolen and classified as contraband.

---

## 10. Inventory Counting and Financial Penalties

**Status:** REQUIRES TESTING

### 10.1 Proposed Fine Calculation

The initial design uses:

```text
Total Fine = Illegal Item Quantity × Fine Per Item
```

Default proposal:

```text
Fine Per Item = 100 septims
```

Example:

```text
Stolen Items:       3
Skooma Bottles:     2
---------------------
Total Items:        5
Fine Per Item:    100
---------------------
Total Fine:       500
```

### 10.2 Key Technical Issue

The system must count individual illegal items rather than merely distinct base item forms.

This is particularly important for inventory stacks.

A single inventory entry may represent multiple items.

### 10.3 Counting Requirements

The final implementation must:

- Count relevant quantities accurately.
- Distinguish item types from quantities.
- Avoid duplicate counts.
- Handle stolen contraband.
- Preserve the correct count before confiscation.
- Handle items removed by another system during the encounter.

### 10.4 Insufficient Gold

The behavior when the player cannot pay remains undefined.

Possible future options include:

- Imprisonment.
- A reduced or alternative penalty.
- A configurable debt system.

No specific behavior has been selected.

### 10.5 Preliminary Recommendation

Develop the item counting system as a standalone prototype.

Validate its results against known test inventories before integrating fines or confiscation.

---

## 11. Skyrim Crime and Arrest Mechanics

**Status:** FEASIBLE IN PRINCIPLE — REQUIRES RESEARCH

### 11.1 Objective

Integrate with Skyrim's existing criminal justice systems rather than implementing a new prison framework.

The mod must support:

- Final refusal of a lawful inspection.
- Possible guard hostility.
- Arrest.
- Imprisonment.
- Financial penalties.
- Compatibility with existing bounties.

### 11.2 Potential Papyrus Mechanisms

Relevant native functions and systems to investigate include:

- `Actor.SendPlayerToJail()`
- `Actor.PlayerPayCrimeGold()`
- `Actor.SetPlayerResistingArrest()`
- Crime faction relationships.
- Existing vanilla arrest dialogue and quests.

Exact function signatures, preconditions and effects must be verified before implementation.

### 11.3 Major Risk: Unrelated Bounties

Custom Body Search penalties must not unintentionally:

- Clear existing unrelated bounties.
- Confiscate unrelated property.
- Alter unrelated crime records.
- Permanently modify guard hostility.
- Trigger duplicate punishments.

### 11.4 Final Refusal

A final refusal should transfer control to a validated crime response.

The correct mechanism may involve a specific crime-state transition or guard dialogue outcome.

Simply making guards hostile is not necessarily equivalent to a normal arrest response.

### 11.5 Preliminary Recommendation

Keep custom fines and vanilla bounty handling logically separate until their interactions are understood.

Test arrest behavior in a minimal load order before introducing third-party prison frameworks.

---

## 12. Dialogue Branching

**Status:** VERIFIED CK CAPABILITY — REQUIRES IMPLEMENTATION

### 12.1 Main Dialogue

The initial encounter provides three choices:

1. Cooperate.
2. Refuse.
3. Negotiate.

### 12.2 Cooperation

A successful inspection may result in:

- Immediate release when inventory is clean.
- Confiscation and consequences when illegal items are found.

### 12.3 Refusal

The player receives a final warning.

Final refusal transfers control to the crime response.

### 12.4 Negotiation

Negotiation must preserve an important gameplay distinction.

**Immediate Adult Proposition**

- Guaranteed acceptance.
- No inventory inspection.
- No confiscation.
- SexLab interaction.
- Release.

**Successful Persuasion**

- No inspection.
- No confiscation.
- Release.

**Failed Persuasion Followed by Adult Proposition**

- Guaranteed acceptance of the adult proposition.
- Mandatory inspection.
- Confiscation of illegal goods.
- SexLab interaction.
- No additional fine or imprisonment.
- Release.

**Adult Proposition After Illegal Goods Are Found**

- Confiscated goods remain confiscated.
- SexLab interaction replaces additional penalties.
- Release.

### 12.5 Required Internal Flags

Potential temporary flags include:

- PersuasionAttempted.
- PersuasionFailed.
- InspectionCompleted.
- IllegalGoodsDetected.
- ConfiscationCompleted.
- AdultAlternativeSelected.

These are conceptual flags, not finalized script variable names.

### 12.6 Important Restriction

Failed persuasion cannot be repeated indefinitely during the same encounter.

The player may still choose the adult alternative, but confiscation must then occur.

The dialogue conditions must enforce this rule reliably.

---

## 13. SexLab Integration

**Status:** FEASIBLE IN PRINCIPLE — API TESTING REQUIRED

### 13.1 Objective

Initiate a SexLab interaction between the player and selected guard when the player chooses an adult alternative.

### 13.2 Required Functionality

The integration must:

- Verify framework availability.
- Verify actor availability.
- Select an appropriate supported animation.
- Start the scene.
- Detect successful initialization.
- Wait for completion or interruption.
- Resume the gameplay encounter.
- Release temporary actor and player restrictions.

### 13.3 Major Risks

Potential issues include:

- No matching animation.
- Scene initialization failure.
- Actors becoming unavailable.
- Another adult scene already active.
- Scene interruption.
- Player or guard leaving the expected state.
- Equipment restoration conflicts.
- Animation framework errors.

### 13.4 Preliminary Architecture

SexLab integration should be isolated from the core inspection logic.

The core manager should request an adult interaction without directly managing every animation-specific detail.

A dedicated integration component should handle framework-specific calls and callbacks.

### 13.5 Failure Policy

An adult scene must not be treated as completed simply because a start request was issued.

If scene initialization fails, the encounter must return to a safe state.

The exact gameplay outcome after failure remains to be defined.

---

## 14. Future OStim Compatibility

**Status:** REQUIRES RESEARCH

OStim support is not part of the initial required implementation.

The preferred design is to isolate animation framework integration.

Potential structure:

```text
Body Search Redux Core
          |
          v
Adult Scene Interface
          |
    +-----+-----+
    |           |
    v           v
 SexLab        OStim
 Adapter       Adapter
```

The adapters would expose equivalent high-level behavior where technically possible.

Their internal APIs and animation handling may differ significantly.

OStim compatibility must not be assumed merely because the core architecture is modular.

---

## 15. Player Control and Equipment Restoration

**Status:** REQUIRES TESTING

### 15.1 Temporary Control

The player may need to be temporarily restricted during dialogue initiation or inspection.

The chosen method must not create permanent movement or camera locks.

### 15.2 Equipment Removal

Before the inspection:

1. Record equipped items.
2. Remove the relevant equipment.
3. Perform the inspection.
4. Restore the equipment when appropriate.

### 15.3 Special Cases

The system must consider:

- Quest equipment.
- Equipped weapons.
- Multiple equipment slots.
- Modded clothing.
- Devious Devices.
- Equipment intentionally changed by another mod.
- Combat interruptions.
- Equipment removed during an adult scene.

### 15.4 Important Safety Requirement

The mod must not automatically force-equ ip restricted equipment or override legitimate changes made by other gameplay systems.

Equipment restoration should be conditional rather than unconditional.

### 15.5 Preliminary Recommendation

Build an equipment restoration prototype separately from the main inspection quest.

Use ordinary clothing and armor first.

Test Devious Devices only after standard behavior is stable.

---

## 16. Quest and Scripted Scene Protection

**Status:** PARTIALLY FEASIBLE — SIGNIFICANT COMPATIBILITY CHALLENGE

### 16.1 Objective

Body Search Redux must avoid interrupting:

- Important quest scenes.
- Active dialogue.
- Combat.
- Arrest sequences.
- Prison sequences.
- Adult animations.
- Defeat and captivity systems.
- Other interactions that control the player.

### 16.2 Available Detection Mechanisms

Some conditions can be checked through actor state, location, quest conditions and framework-specific APIs.

`IsInDialogueWithPlayer()` may help determine whether a particular actor is currently talking to the player.

However, it is not a universal global dialogue or quest-scene detector.

### 16.3 Important Limitation

Skyrim does not expose a universal Papyrus flag identifying every protected scripted interaction.

Therefore, complete protection cannot be guaranteed through one simple check.

### 16.4 Proposed Approach

Combine several layers:

**General Eligibility Checks**

Verify player, guard and encounter availability.

**Quest/Scene Checks**

Use detectable quest conditions, scene information or controlled exclusions where practical.

**Framework Checks**

Inspect supported SexLab, prison, defeat or restraint states.

**Location Exclusions**

Disable inspections in problematic areas when appropriate.

**Repeat Validation**

Check eligibility before guard reservation and immediately before dialogue.

### 16.5 Emergency Recovery

If a conflict is detected during the approach phase, cancel the encounter.

If another system takes control during an active encounter, determine whether Body Search Redux can safely terminate without interfering with that system.

---

## 17. Compatibility with Other Mods

**Status:** REQUIRES TESTING

### 17.1 Priority Targets

- SexLab Framework.
- Acheron.
- PAMA Prison Alternative.
- Devious Devices.
- Skyrim vanilla crime and arrest systems.
- Guard dialogue modifications.
- Large modlists such as NEFARAM.

### 17.2 Potential Conflicts

The following systems may affect Body Search Redux:

- Custom guard AI.
- Additional ForceGreet packages.
- City overhauls.
- Arrest and prison replacements.
- Defeat frameworks.
- Equipment control systems.
- SexLab scene managers.
- Quest scenes involving the player.

### 17.3 Compatibility Philosophy

Avoid unnecessary overrides of vanilla records.

Prefer dedicated quests, dialogue branches and aliases where possible.

Use framework integrations or compatibility patches when required.

### 17.4 Validation Strategy

Start with a minimal profile.

Then introduce compatibility targets in controlled groups.

Finally, test in a full NEFARAM installation.

---

## 18. Papyrus Performance

**Status:** DESIGN REQUIREMENT

Body Search Redux should avoid unnecessary repeated processing.

### 18.1 Performance Goals

- No uncontrolled high-frequency polling.
- No repeated scanning while the player is outside eligible areas.
- No simultaneous inspection managers.
- Minimal work during active dialogue or SexLab scenes.
- Clear registration and cleanup of temporary events.
- Avoid unnecessary script allocations and repeated form lookups.

### 18.2 Recommended Direction

Prefer location-driven eligibility and event-driven state changes where practical.

Controlled periodic checks may be acceptable when necessary.

### 18.3 Risk Areas

- Repeated nearby actor scanning.
- Overlapping update events.
- Unreleased aliases.
- Repeated dialogue start attempts.
- Incomplete cleanup.
- Long-running scripts waiting for events that never occur.

---

## 19. Proposed Prototype Roadmap

The first development prototypes should be small and independently testable.

### Prototype P1 — Guard Encounter

**Goal:** Prove that a nearby guard can approach and initiate dialogue.

Tests:

- Guard selection.
- Distance.
- AI package.
- Automatic dialogue.
- Player movement restoration.
- Timeout and cancellation.

### Prototype P2 — Inventory Inspection

**Goal:** Prove reliable illegal item detection and counting.

Tests:

- Stolen items.
- Mixed inventory stacks.
- Skooma.
- Item quantities.
- Duplicate categories.

### Prototype P3 — Confiscation

**Goal:** Prove that illegal goods can be removed safely.

Tests:

- Native confiscation.
- Controlled contraband removal.
- Quest items.
- Transfer containers.
- Item count verification.

### Prototype P4 — Crime Outcomes

**Goal:** Validate fines, imprisonment and refusal consequences.

Tests:

- Vanilla jail.
- Existing bounty preservation.
- Insufficient gold.
- Guard hostility.
- Crime faction behavior.

### Prototype P5 — SexLab Adapter

**Goal:** Validate reliable adult scene transitions.

Tests:

- Scene initiation.
- Scene completion.
- Scene cancellation.
- Actor availability.
- Player control recovery.

### Prototype P6 — Full Encounter

**Goal:** Combine the validated components into a complete gameplay interaction.

Tests:

- Every dialogue branch.
- Failed persuasion.
- Confiscation rules.
- SexLab alternatives.
- Cleanup.
- Repeated encounters.
- Save/load behavior.

### Prototype P7 — Compatibility

**Goal:** Validate behavior in increasingly complex mod environments.

Tests:

- Acheron.
- PAMA.
- Devious Devices.
- Guard dialogue mods.
- NEFARAM.

---

## 20. Main Technical Risks

| Risk | Severity | Proposed Mitigation |
|---|---|---|
| Guard fails to approach | Medium | Timeout and cancellation |
| Dialogue fails to start | High | ForceGreet prototype and recovery |
| Simultaneous inspections | High | Central encounter manager |
| Incorrect stolen item counting | High | Dedicated inventory prototype |
| Wrong inventory confiscation | Critical | Controlled transfers and extensive testing |
| Quest item loss | Critical | Protection rules and test saves |
| Incorrect bounty modification | High | Isolated crime-system tests |
| Permanent player control loss | Critical | Safe cleanup and recovery |
| SexLab scene initialization failure | High | Adapter validation and fallback |
| Quest scene interruption | High | Eligibility checks and exclusions |
| Devious Devices equipment conflicts | High | Conditional restoration and compatibility tests |
| Heavy Papyrus processing | Medium | Controlled activation and profiling |
| Save/load during an active encounter | High | Persistent state recovery tests |

---

## 21. Preliminary Feasibility Assessment

| Component | Preliminary Assessment |
|---|---|
| City guard identification | Supported by documented actor functionality |
| Nearby guard discovery | Requires implementation research |
| Automatic guard dialogue | Strong CK/ForceGreet approach |
| Single active inspection | Feasible through centralized state management |
| Branching dialogue | Supported by Creation Kit |
| Stolen item detection | Promising documented extension |
| Native stolen item confiscation | Documented Skyrim API |
| Mixed stolen stack handling | Requires dedicated testing |
| Skooma identification | Feasible through explicit FormLists |
| Custom fine calculation | Feasible in principle; quantity handling unresolved |
| Vanilla arrest integration | Requires detailed API and quest research |
| SexLab interaction | Feasible in principle; API validation required |
| Future OStim adapter | Architectural possibility; not confirmed |
| Quest interruption prevention | Partially feasible; cannot guarantee universal detection |
| Equipment restoration | Feasible in principle; compatibility risks |
| NEFARAM compatibility | Not tested |

### Overall Assessment

**Body Search Redux appears technically achievable in principle.**

Its main gameplay concepts can be mapped to known Skyrim scripting and Creation Kit mechanisms.

However, several critical components remain unvalidated:

1. Reliable nearby guard selection.
2. Safe automatic dialogue initiation.
3. Exact stolen item quantities.
4. Mixed inventory stack confiscation.
5. Correct interaction with crime and bounty systems.
6. Reliable SexLab callbacks and interruption recovery.
7. Detection of incompatible scripted interactions.
8. Equipment restoration in heavily modded environments.

These areas should be addressed before committing to a full implementation.

---

## 22. Verified Technical References

The following sources provide technical background for the study.

### Skyrim Papyrus — Native Functions

**RemoveAllStolenItems**

https://papyrus.bellcube.dev/skyrimse/script/objectreference/function/removeallstolenitems/

**RemoveItem**

https://papyrus.bellcube.dev/skyrimse/script/objectreference/function/removeitem/

**IsGuard**

https://papyrus.bellcube.dev/skyrimse/script/actor/function/isguard/

**IsInDialogueWithPlayer**

https://papyrus.bellcube.dev/skyrimse/script/objectreference/function/isindialoguewithplayer/

**SKSE64 Script Sources**

https://github.com/ianpatt/skse64

### Steelfeathers' Papyrus Extensions

**Nexus Mods**

https://www.nexusmods.com/skyrimspecialedition/mods/154540

**GitHub**

https://github.com/Steelfeathers/SteelfeathersPapyrusExtensions

**IsFormStolen Documentation**

https://papyrus.bellcube.dev/skyrimse/script/steelfeatherspapyrusextensions/function/isformstolen/

### Creation Kit / ForceGreet

**Beyond Skyrim — World Interactions**

https://wiki.beyondskyrim.org/wiki/Arcane_University:World_Interactions

These references document relevant functionality but do not constitute validation of the complete Body Search Redux implementation.

---

## 23. Next Steps

The next documentation phase will define the proposed internal architecture in:

`ARCHITECTURE.md`

That document should describe:

- Quest structure.
- Reference aliases.
- Guard selection.
- AI packages.
- Dialogue branches.
- Papyrus script responsibilities.
- Inventory services.
- Crime services.
- SexLab integration.
- State transitions.
- Cleanup and recovery.
- Save/load handling.

Final implementation decisions should remain open until the corresponding prototypes have been validated.

---

## 24. Final Conclusion

**Body Search Redux is considered a viable candidate for development, subject to technical validation.**

The project does not appear to require a custom SKSE DLL at this stage.

A Creation Kit and Papyrus implementation, supported by SKSE and selected external libraries, is the preferred initial direction.

The most important development principle will be to validate risky subsystems independently before integrating them into the complete encounter.

**Stability, predictable behavior, player choice and compatibility must take priority over implementation complexity.**

---

*Body Search Redux — Technical Feasibility Study v0.1*

*Prepared for Rhiven — November 2026*
