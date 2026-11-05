# 📜 Body Search Redux — Credits, Permissions & Attribution

**Project:** Body Search Redux  
**Author:** Rhiven  
**Document:** CREDITS.md  
**Version:** 0.1 — Initial Credits and Permissions  
**Date:** November 5, 2026  
**Status:** Preliminary Documentation — Attribution Audit Pending

---

## 1. Purpose

This document records the authorship, permissions, acknowledgments and third-party dependencies associated with **Body Search Redux**.

The project is inspired by the original **SexLab Body Search** modification for Skyrim.

Body Search Redux is planned as a new implementation rather than a direct continuation of the original plugin.

Nevertheless, the original modification provides an important conceptual and technical reference.

This document aims to ensure that:

- Original authors receive proper recognition.
- Existing permissions are respected.
- Third-party code and assets are not redistributed without authorization.
- Dependencies are identified and credited.
- The origins of reused resources remain traceable.
- Community contributions are acknowledged appropriately.

**Respect for original creators is a fundamental principle of this project.**

---

## 2. Original Mod — SexLab Body Search

### Original Project

**Mod Name:** SexLab Body Search  
**Platform:** The Elder Scrolls V: Skyrim  
**Original Author:** To be confirmed from the original distribution page  
**Additional Contributor:** Suzutsuki  
**Original Publication:** To be verified  
**Original Download Page:** To be added after verification

### Relationship to Body Search Redux

Body Search Redux takes inspiration from the original mod's central gameplay concept:

A city guard may stop the player character and request an inspection, potentially leading to additional gameplay interactions and consequences.

The new project intends to expand and modernize this concept through:

- A redesigned guard detection system.
- Configurable inspection probabilities.
- Short-range guard interception.
- A new dialogue structure.
- Inventory inspection and confiscation.
- Multiple player negotiation options.
- Integration with Skyrim's criminal justice systems.
- Modular adult-framework support.
- Improved safety checks and compatibility.
- Centralized state management and recovery.

These features represent the planned design of Body Search Redux.

They do not imply that the original mod already implements all of them.

### Development Approach

Body Search Redux is intended to be developed as a new mod.

The original plugin and available source files may be examined to understand existing technical approaches.

Any actual reuse of original source code, scripts, dialogue records or assets must be documented.

The final implementation may use entirely new code or selectively adapt permitted components.

No source-code audit has been completed at the time of writing.

---

## 3. Original Author's Permissions

The original author provided the following permission statement, as previously recorded from the original mod description:

> Permissions: If something in this mod doesn't jive with you, or you feel like you want to change something...or fix something...you're free to do so. You have a number of options along these lines.
>
> 1. You can patch it, and post it in the comments.
> 2. You can fork it, and upload a new version. You must give credit. i.e: "Based on Dialog Patch" etc. You must also credit Suzutsuki.
>
> Just give credit where it's due is all I ask.

### Permission Interpretation

Based on this statement, the original author expressly permits:

- Creating patches.
- Modifying or fixing the original modification.
- Publishing a fork or modified version.
- Redistributing a derivative version, subject to the stated attribution requirements.

The permission statement requires:

1. Appropriate credit to the original author.
2. Appropriate credit to Suzutsuki.
3. Clear acknowledgment of the original work when publishing a derived version.

### Important Limitations

These permissions apply to the work covered by the original author's authorization.

They do not automatically grant redistribution rights for:

- Third-party scripts.
- External libraries.
- Animation assets.
- Textures or meshes created by other authors.
- Voice recordings.
- Assets governed by separate permissions.
- Other external modifications or dependencies.

Individual permissions must be verified before incorporating or redistributing such resources.

The original permission statement should also be checked against the current original distribution page before any public release.

---

## 4. Required Attribution

Any future release of Body Search Redux must clearly identify its relationship to SexLab Body Search.

### Proposed Release Credit

**Body Search Redux**

Developed by **Rhiven**.

Inspired by and based on concepts from the original **SexLab Body Search** modification.

**Original SexLab Body Search author:** To be confirmed.

**Additional credit:** Suzutsuki.

Special thanks to the original creators for their work and for permitting community modifications and derivative projects under the stated conditions.

### Attribution Placement

Credits should appear in:

- The project repository.
- The release description.
- The mod's documentation.
- Any published installation instructions.
- Additional distribution pages, where appropriate.

If original source files are reused, their provenance should also be recorded in the development documentation.

### Attribution Audit

Before releasing the first playable version, the project must confirm:

- The original author's exact name or username.
- The original mod's publication URL.
- The relevant permission statement.
- The role of Suzutsuki.
- Whether additional contributors require attribution.
- Whether any reused assets have separate restrictions.

---

## 5. Body Search Redux — Original Development

### Project Author

**Rhiven**

GitHub:

https://github.com/Rhivens

Project Repository:

https://github.com/Rhivens/Rhiven-SkyrimSE-Mods

Project Directory:

https://github.com/Rhivens/Rhiven-SkyrimSE-Mods/tree/main/Projects/Body-Search-Redux

### Planned Original Work

The following components are intended to be designed and developed specifically for Body Search Redux:

- Central encounter management.
- Guard selection and reservation.
- Inspection eligibility checks.
- Dialogue branching and decision logic.
- Inventory inspection coordination.
- Confiscation management.
- Criminal consequence handling.
- Equipment handling and restoration.
- SexLab integration.
- MCM configuration.
- Cleanup and recovery systems.
- Technical documentation.

This list describes planned work rather than completed features.

The actual authorship of individual components will be recorded as development progresses.

---

## 6. External Frameworks and Dependencies

Body Search Redux may rely on existing Skyrim modding frameworks.

These frameworks remain the intellectual property of their respective creators and contributors.

### 6.1 Skyrim Script Extender — SKSE64

**Purpose:** Extended scripting functionality.

Official Website:

https://skse.silverlock.org/

GitHub:

https://github.com/ianpatt/skse64

SKSE is an external dependency.

Its executable, libraries and source files are not part of Body Search Redux unless separate permission explicitly allows a particular form of redistribution.

### 6.2 SkyUI

**Purpose:** Mod Configuration Menu (MCM).

Nexus Mods:

https://www.nexusmods.com/skyrimspecialedition/mods/12604

Body Search Redux intends to use SkyUI for its configuration interface.

SkyUI remains an independent project.

### 6.3 SexLab Framework

**Purpose:** Adult animation framework.

SexLab is the planned primary animation framework for the initial implementation.

Body Search Redux will interact with SexLab through supported scripting mechanisms.

SexLab and its animation resources remain subject to their respective permissions.

The framework and its assets must not be bundled without appropriate authorization.

The exact supported SexLab version and official source reference will be documented before release.

### 6.4 Steelfeathers' Papyrus Extensions

**Purpose:** Additional Papyrus functionality, potentially including stolen-item detection.

Nexus Mods:

https://www.nexusmods.com/skyrimspecialedition/mods/154540

This extension is a potential technical dependency.

If the project uses its native functions:

- The extension must be listed as a requirement.
- Users must obtain it from an authorized distribution source.
- Its DLL must not be bundled without explicit redistribution permission.
- Version compatibility must be verified.

The final dependency decision will follow technical testing.

### 6.5 OStim — Possible Future Integration

OStim support is being considered for a future version.

No OStim adapter has been developed.

If implemented, the project will document the exact OStim distribution, API version, relevant authors and permissions.

No OStim compatibility is currently guaranteed.

---

## 7. Compatibility-Related Projects

Body Search Redux may require compatibility testing with additional Skyrim modifications.

Potential compatibility targets include:

- Acheron.
- PAMA Prison Alternative.
- Devious Devices.
- Guard dialogue modifications.
- Vanilla crime and prison systems.
- Large curated modlists, including NEFARAM.

Listing a project here does not imply:

- Official support.
- Formal collaboration.
- Permission to redistribute its assets.
- A verified compatibility patch.
- Any endorsement from the project's authors.

Relevant authors and projects will be credited when actual integration work or permitted code reuse takes place.

---

## 8. Third-Party Resource Policy

### 8.1 Source Code

Third-party code may be studied for technical understanding.

Actual copying, modification or redistribution must comply with the applicable permissions.

Whenever third-party code is incorporated, its origin must be documented.

### 8.2 Creation Kit Records

If original quests, dialogue records, AI packages or other plugin records are reused, their provenance should be recorded.

The project should distinguish original implementation from modified or directly reused material.

### 8.3 Animations

Body Search Redux does not initially plan to distribute custom adult animations.

Existing SexLab animations are expected to remain external dependencies.

Animation authors retain their respective rights.

### 8.4 Visual and Audio Assets

No third-party visual or audio assets are currently planned for redistribution.

Any future use must be reviewed independently.

### 8.5 Native Libraries

External SKSE DLLs and other native libraries should generally remain separately installed dependencies.

They must not be included in release packages unless redistribution is permitted.

---

## 9. Project Repository and Licensing

The repository is intended to contain original technical documentation and, eventually, source files related to Rhiven's Skyrim modding projects.

At this stage, Body Search Redux does not have a finalized project-specific software license.

### Licensing Principles

- Original source code may receive a license chosen by its author.
- Third-party code remains governed by its original license or permissions.
- Derivative components must respect applicable attribution requirements.
- Different project components may require different licensing terms.
- A general repository license should not be assumed to supersede third-party restrictions.

### Current Licensing Status

**License decision: Pending.**

Public availability of repository content does not, by itself, grant unrestricted reuse or redistribution rights.

A suitable license will be considered after the source audit and before the first public code release.

---

## 10. Planned Attribution Register

As development progresses, this document should maintain a record of reused third-party materials.

| Component | Original Project / Author | Usage | Permission Status |
|---|---|---|---|
| Gameplay inspiration | SexLab Body Search / Author to confirm | Conceptual reference | Permission statement recorded |
| Original contributor | Suzutsuki | Attribution required | Explicitly named in original permissions |
| Original scripts | SexLab Body Search | Potential technical reference | Source audit pending |
| SexLab framework | Respective framework authors | External dependency | Version and permissions to verify |
| SKSE64 | SKSE team | External dependency | No redistribution planned |
| SkyUI | SkyUI team | External dependency | No redistribution planned |
| Papyrus extensions | Steelfeathers | Potential external dependency | No DLL redistribution planned |
| OStim | Respective project authors | Possible future integration | Not yet evaluated |

This register is preliminary.

It must be updated whenever source code, assets or dependencies are added.

---

## 11. Community Contributions

Future community contributions may include:

- Bug reports.
- Compatibility testing.
- Gameplay suggestions.
- Translation improvements.
- Documentation corrections.
- Papyrus optimizations.
- Compatibility patches.

Contributors should receive appropriate recognition for accepted contributions.

Contribution procedures and permissions may be established once public development begins.

Submitting an external resource does not automatically grant permission for its redistribution.

---

## 12. Pre-Release Attribution Checklist

Before publishing the first playable release:

- [ ] Confirm the original SexLab Body Search author's identity.
- [ ] Record the original distribution URL.
- [ ] Verify the original permission statement against the source page.
- [ ] Confirm Suzutsuki's contribution and required credit.
- [ ] Audit all reused source files.
- [ ] Audit all copied or modified Creation Kit records.
- [ ] Identify any third-party assets.
- [ ] Verify redistribution permissions for every included resource.
- [ ] Document the required external frameworks.
- [ ] Confirm native DLL redistribution restrictions.
- [ ] Select an appropriate license for original project code.
- [ ] Add full credits to the public release description.
- [ ] Update the attribution register.
- [ ] Review this document before release.

**No public release should be considered attribution-complete until these checks have been addressed.**

---

## 13. Acknowledgments

Special thanks to:

**The original SexLab Body Search author** — For developing the original guard inspection concept and explicitly permitting community modifications and derivative versions subject to attribution.

**Suzutsuki** — For contributions to the original project and for being explicitly identified in the original permissions as requiring credit.

**The Skyrim modding community** — For the tools, frameworks, documentation and accumulated technical knowledge that make projects like Body Search Redux possible.

**The SKSE, SkyUI and animation-framework communities** — For maintaining and extending the technical ecosystem used by Skyrim mod developers.

Additional acknowledgments will be added as the project develops.

---

## 14. Final Statement

Body Search Redux is intended to build upon an existing gameplay idea while respecting the work of its original creators.

The project will strive to distinguish clearly between:

- Original work by Rhiven.
- Concepts inspired by other modifications.
- Modified or reused third-party code.
- External frameworks and dependencies.
- Community contributions.

**Proper attribution is not optional. It is part of responsible mod development.**

---

*Body Search Redux — Credits & Permissions v0.1*

*Prepared for Rhiven — November 2026*
