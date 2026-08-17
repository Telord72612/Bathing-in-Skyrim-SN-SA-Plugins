# Bathing in Skyrim Renewed SN&SA Plugins

**V2** — Surfaces **Bathing in Skyrim - Renewed** dirtiness into **SkyrimNet** as a fourth survival
signal, alongside the hunger / fatigue / cold that **SeverActions** already tracks — and gives NPCs a
full **Bathing action tree**: they notice they're dirty, find water, walk to it, bathe, wash each
other's backs, and ask you for whatever's missing. All of it is the character's own choice — they are
never forced to wash.

It ships as a self-contained companion mod. It does **not** edit Bathing in Skyrim, SeverActions, or
SkyrimNet — everything is layered on top. Works on **SE, AE and VR** from one download.

## What it does

- **Auto-tracking.** Every follower SeverActions tracks for survival is automatically enrolled in
  Bathing in Skyrim's dirtiness tracking — no "share bathing concern" dialogue needed. It honours
  SeverActions' own per-follower exclusion (`SeverActions_Survival_Excluded`).
- **Awareness (informative prompt).** Riding in the *same* character-bio context SkyrimNet builds for
  hunger/sleep/cold, a tracked follower gets a short factual note: how dirty they look and smell, where
  the nearest water is ("about 20 metres north — a short walk away"), and exactly what is missing
  before they could wash (soap / water / privacy). The prompt never tells the character what to do —
  the LLM decides from personality and situation.
- **A "Bathing" action category** the LLM can open, containing the whole tree below. Every action
  re-checks Bathing in Skyrim's real rules at execution time: the LLM only ever *proposes* — if the
  character can't actually bathe (no water, no soap while grimy, someone watching, just bathed), the
  action refuses and the character privately learns *why*, so they can react in character ("I'd need a
  bar of soap...").

### The action tree

- **bathe** — the core action, and it handles distance itself:
  - standing at water → bathe right there (Bathing in Skyrim's full animated wash);
  - water within ~20 m with a clear path → **walk there on their own and bathe on arrival**
    (precise walking + facing via the bundled Standpoint, then a wade-in to a knee-to-waist-deep
    spot picked by the bundled Water Finder);
  - water a real walk away (20–100 m) → they *say out loud* that they wish they could bathe and
    would like to go to the water — you can lead them, or the LLM can decide to walk;
  - water far away (100 m+) → just a private longing;
  - no water known → they look around and honestly find none.
- **FindClosestWater** — take stock of where the nearest water is (works indoors too: bath tubs and
  placed water are detected), how far, and whether there's a way to reach it.
- **MoveToWater** — walk to scouted water without bathing.
- **AskForBathingHelp** — ask the player for what's missing: soap, water, or privacy.
- **Washing each other** — see the next section (optional, needs Wash Me).

A clean follower can still choose a comfort soak; **soap is only required once noticeably unwashed**
(≥ Bathing in Skyrim tier 1, ~20% dirty). Short per-action cooldowns stop the LLM picking the same
action twice in a row.

### Everyone notices

Bathing isn't invisible: a follower undressing to bathe becomes something nearby characters *know*
(without being forced to react), co-bathers share the moment, onlookers remember what they saw, and
the bather comes out with their own thoughts about finally being clean. When **you** wash an NPC,
they know it was your hands.

### Where bathing won't happen

All bathing actions are unavailable **in combat**, **inside dungeons and hostile lairs** (caves,
crypts, bandit camps, falmer hives, vampire/warlock lairs and the rest of the hostile-lair family),
and **during a SexLab or OStim scene**. Guards are enforced twice: SkyrimNet never even offers the
actions there, and the scripts refuse if one slips through.

## Washing each other (OPTIONAL — needs **Bathing in Skyrim - Wash Me - Renewed**)

Nine additional actions for two-person washing:

- **Wash someone's back** (you, or another NPC) — the direct route, for when it's already agreed,
  ordered, or the washee has no say in it (prisoner roleplay works).
- **Offer / request a back wash** — the consent route: the other NPC answers for themselves
  (**accept**, **decline**), and on acceptance the wash starts by itself. Ask a follower out loud
  and they'll agree in speech, then wash you when you invite them to.
- **Intimate washing** (offer / request / do) — pressed close, washing with her own body; female
  washer only, for genuinely intimate pairs in private. Onlookers are never informed of this variant.

Both actors get properly cleaned through Bathing in Skyrim, soap is consumed by the same rules.

**Without Wash Me these actions remove themselves from SkyrimNet at load** — the LLM never sees an
action it cannot perform. The FOMOD installer also lets you skip installing them entirely if you want
a leaner action list.

- **In VR** only the NPC animates — your own hands are your half of it. Walk more than ~2 m away
  mid-wash and the NPC stops, and remembers you walked off.
- **On SE/AE** both actors play the paired animation.
- ⚠️ **Run your animation-behavior generator (Pandora/Nemesis/FNIS) after installing Wash Me** —
  without that the washes still work (positioning, cleaning, reactions), just without the visible
  animation.

## What is bundled (you do NOT install these separately)

Two small SKSE frameworks of mine ship inside the mod:

- **Water Finder** — finds the nearest water for an actor (rivers, lakes, indoor tubs and placed
  water), measures depth, and picks a knee-to-waist wading spot so nobody bathes under the floor or
  swims out of their depth.
- **Standpoint** (`Standpoint.esp`, ESL — costs no load-order slot) — walks an NPC to an exact point
  with exact facing (centimetre / ~1° arrivals), with stuck recovery.


## Compatibility

**Skyrim Special Edition, Anniversary Edition, and VR — one download for all three.**

- Both plugins are standard SSE-format ESLs (header 1.7, `Skyrim.esm` the only master).
- The two bundled SKSE DLLs are **multi-runtime**: each exports the SE/AE entry point *and* the VR
  ones, so the same file loads on any edition.
- The one VR-exclusive call (PLANCK, keeping physics from shoving a co-located NPC) runs **only**
  when the game reports VR.

Install the **edition-matching build of each dependency** below.

## Requirements (hard dependencies)

- **Bathing in Skyrim - Renewed — version 2.79 or newer** (`Bathing in Skyrim.esp`). Built against
  the 2.79 script API; older builds (e.g. 2.62) changed function signatures and are not supported.
  If you update Bathing in Skyrim mid-playthrough, follow its own update guidance (a clean save is
  usually needed).
- **SeverActions** (with its survival system enabled). v6+ recommended — its travel orchestrator is
  the fallback walker with stuck recovery.
- **SkyrimNet** — Beta 23.1 or newer recommended (23.1 fixed action-eligibility refresh).
- **PapyrusUtil** — the build for your game (SE for SE/AE, VR for VR)
- **Powerofthree's Papyrus Extender (po3)** — already a Bathing in Skyrim requirement.
- **SKSE** — SKSE64 (SE/AE) or SKSEVR (VR), plus the matching Address Library
- **Skyrim VR only:** the **`skyrimvresl`** ESL loader. SE/AE load ESLs natively.

Optional: **Bathing in Skyrim - Wash Me - Renewed** for the paired washing actions.

If Bathing in Skyrim or SeverActions is not present, the mod cleanly no-ops.

## Install

FOMOD installer: **Core** (everything solo — bathing, water finding, walking, asking for help) plus
an optional **Paired washing actions** page (the nine two-person actions; safe to leave checked even
without Wash Me). Load after Bathing in Skyrim, SeverActions, and SkyrimNet; both plugins are
ESL-flagged and cost no regular load-order slot.

## Required Bathing in Skyrim MCM settings

- **Shyness: ON** — powers the won't-bathe-in-front-of-strangers privacy gate. With Shyness off,
  "private" is always true and followers may wash anywhere.
- **Automate Follower Bathing: OFF (Disabled)** — so the *only* thing washing your followers is
  their own LLM-driven choice.
- **Bathing Animation Style (Followers): any style other than None** — with None, Bathing in Skyrim
  skips the animation and just applies the clean.

## Notes

- **The bath is Bathing in Skyrim's own** — undress → bathe idle → clean → re-dress, driven by BiS
  itself. During it the follower is briefly undressed and can't be talked to; that's normal. (NPC
  bathing idles work fine in VR — the "PlayIdle breaks in VR" issue only affects the player's
  VRIK-driven skeleton.)
- **Soap:** an actual bar of soap (a washrag doesn't count). The "dirty / soap-needed" line follows
  BiS's tier-1 threshold and tracks MCM changes; an optional `SADirt_MinDirtTier` global can move it
  to tier 2/3.
- **The player** is tracked by Bathing in Skyrim natively and bathes via its own hotkey/menu. Your
  dirtiness shows in NPC perception, and NPCs can wash your back when asked.

## What's new in V2

- Full **Bathing action category** (14 actions) replacing the single V1 bathe action.
- **Autonomous walk-to-water bathing**: scout → precise walk (bundled Standpoint) → depth-aware
  wade-in (bundled Water Finder) → bathe on arrival, with distance-tiered behaviour (walk / spoken
  wish / private thought).
- **Paired washing** with a real consent chain (offer / request / accept / decline) plus a direct
  route for already-settled situations.
- **Awareness events**: onlookers, co-bathers, post-bath reactions, player-washing-NPC noticed.
- **Guards**: combat, dungeons/hostile lairs, SexLab/OStim scenes — enforced at both the
  action-eligibility layer and in script.
- **Per-action cooldowns** to stop LLM double-picks.
- **Water Finder + Standpoint bundled** — no external framework requirements.
- Interior water detection (tubs / placed water), so indoor bathhouses work.
- FOMOD installer; SE/AE/VR single package.

## Uninstall

Save-safe. The plugins are ESL + loose files; removing the mod stops the bridge. Followers it
enrolled stay enrolled in Bathing in Skyrim's own tracking (harmless, managed by Bathing in Skyrim),
and a few inert `SADirt_*` StorageUtil flags / a dropped faction membership remain with no effect.
