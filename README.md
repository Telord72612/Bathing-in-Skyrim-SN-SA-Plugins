# Bathing-in-Skyrim-SN-SA-Plugins
Just a small plugins that do integration between Bathing in Skyrim Renewed and SkyrimNet/SeverAction

Surfaces **Bathing in Skyrim - Renewed** dirtiness into **SkyrimNet** as a fourth survival
signal, alongside the hunger / fatigue / cold that **SeverActions** already tracks. NPCs become
*aware* of how dirty they are and can *choose* to wash — they are never forced to.

It ships as a small, self-contained companion mod. It does **not** edit Bathing in Skyrim,
SeverActions, or SkyrimNet — everything is layered on top.

## What it does

- **Auto-tracking.** Every follower SeverActions tracks for survival is automatically enrolled in
  Bathing in Skyrim's dirtiness tracking — no "share bathing concern" dialogue needed. It honours
  SeverActions' own per-follower exclusion (`SeverActions_Survival_Excluded`): excluded followers
  are dropped here too.
- **Awareness (informative prompt).** Riding in the *same* character-bio context SkyrimNet builds
  for hunger/sleep/cold, a tracked follower gets a short, factual note: how dirty they look and
  smell (when they're dirty), and — when there's water in reach — whether they **can bathe right
  now** and exactly **what is missing** (soap / a private spot). The prompt never tells the
  character how to feel or what to do; the LLM decides that from the character's personality and the
  situation. A grimy follower with no soap can, for example, choose to ask you for some.
- **Bathing (LLM action).** Whenever a follower is in (or under) water and in a private spot, a
  `bathe` action becomes available — they do **not** have to be dirty (some characters enjoy a hot
  soak). **Soap is only required once they're at least noticeably unwashed (~20% dirty):** a clean
  follower can soak for comfort with no soap; a grimy one needs a bar of soap (and asks for one if
  they have none). If the character chooses it, they wash in Bathing in Skyrim — a dirty NPC scrubs
  fully clean (consuming one soap + its bonus), a clean NPC just soaks — then registers a private
  thought that they feel clean and refreshed. A short cooldown stops a clean NPC in water being
  offered it on a loop.

There is **no autonomous bathing** — the action is only ever *offered*. Some characters keep
themselves clean and enjoy a soak; others won't bother even when filthy. That is the point.

## Compatibility

Works on **Skyrim Special Edition, Anniversary Edition, and VR.** The plugin is the standard
SSE format (an ESL light master, single master `Skyrim.esm`) and the scripts use only vanilla +
SKSE + PapyrusUtil functions — nothing VR-exclusive. Just install the **edition-matching build of
each dependency** below.

## Requirements (hard dependencies)

- **Bathing in Skyrim - Renewed** (`Bathing in Skyrim.esp`)
- **SeverActions** (with its survival system enabled)
- **SkyrimNet**
- **PapyrusUtil** — the build for your game (PapyrusUtil SE for SE/AE, PapyrusUtil VR for VR)
- **SKSE** — SKSE64 (SE/AE) or SKSEVR (VR), plus the matching Address Library
- **Skyrim VR only:** the **`skyrimvresl`** ESL loader (so the light plugin loads into FE space).
  SE/AE load ESL plugins natively — no loader needed there.

If Bathing in Skyrim or SeverActions is not present, the mod cleanly no-ops.

## Required Bathing in Skyrim MCM settings

- **Shyness: ON** — this is what stops a follower from bathing in front of strangers (in the
  middle of Whiterun, etc.). The bathe action's privacy gate relies on it. With Shyness off,
  "private" is always true and followers may wash anywhere.
- **Automate Follower Bathing: OFF (Disabled)** — leave Bathing in Skyrim's own follower
  auto-bathing off, so the *only* thing washing your followers is their own LLM-driven choice.
  Leaving it on means a follower could be auto-bathed by Bathing in Skyrim independently of this
  mod.

## Load order

Load after Bathing in Skyrim, SeverActions, and SkyrimNet. The plugin is ESL-flagged (one tiny
faction + one quest, master = `Skyrim.esm` only — all other forms are resolved at runtime), so it
costs no regular load-order slot and can go anywhere a light plugin is allowed.

## Notes

- **Bathing animation — set it in the BiS MCM.** When a follower bathes they play Bathing in
  Skyrim's full bathing sequence (undress → bathe idle → clean → re-dress), driven by BiS itself,
  the same way BiS's own follower auto-bathe does. For the animation to appear you must set BiS MCM
  **"Bathing Animation Style (Followers)"** to a style other than **None** — if it's None, BiS skips
  the animation and just applies the clean. (NPC bathing idles work in VR; the "PlayIdle breaks in
  VR" issue only affects the *player's* VRIK-driven skeleton. BiS also guards the idle with a
  timeout, so the clean always completes even if an idle stalls.) During the bath the follower is
  briefly undressed and can't be talked to — that's BiS's normal bathing, not a glitch.
- **When soap is needed.** Bathing isn't gated behind being dirty — a clean follower can take a soak
  for comfort with no soap. **Soap is required only once a follower is at least noticeably unwashed
  (≥ Bathing in Skyrim's tier 1, ~20% dirty):** below that they soak without it; at or above it they
  need an actual bar of soap to scrub the grime off (a washrag doesn't count), and if they have none
  the prompt notes it so they can ask you for one. (The "dirty / soap-needed" line follows BiS's
  tier-1 threshold, so it tracks any change you make to it in the BiS MCM; an optional
  `SADirt_MinDirtTier` global can move it to tier 2/3.)
- **The player** is tracked by Bathing in Skyrim natively and bathes via its own hotkey/menu; this
  mod's action is for NPCs. The player's dirtiness still shows in NPC perception (an NPC can notice
  the dirty Dragonborn).

## Uninstall

Save-safe. The plugin is ESL + loose files; removing it stops the bridge. Followers it enrolled
stay enrolled in Bathing in Skyrim's own tracking (which is harmless and managed by Bathing in
Skyrim), and a few inert `SADirt_*` StorageUtil flags / a dropped faction membership remain on
actors with no effect.
