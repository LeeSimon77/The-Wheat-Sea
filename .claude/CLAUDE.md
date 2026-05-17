# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

This is a collaborative worldbuilding and campaign management repository for a homebrew TTRPG campaign called **The Wheat Sea** (also referred to as **Sea of Wheat**). It is not a software project — it is a living document set for a Dungeon Master and their six players. All files are Markdown.

## AI Use Policy

Per [AI_Policy.md](AI_Policy.md): Claude is permitted for lore logistics, logic puzzles, resource management, and information lookup — **not** for creative writing. The DM's prose and creative decisions should not be ghostwritten or overwritten by AI. No AI-generated images or music. No OpenAI, Grok, or Gemini.

## Repository Structure

- **[Sea_of_Wheat.md](Sea_of_Wheat.md)** — The primary world document. Contains languages, the full noble hierarchy of the Cerisian Empire, and general lore. This is the canonical reference for worldbuilding.
- **[Beastiary.md](Beastiary.md)** — Stat blocks for NPCs and creatures using a custom rule system (morale scores, mob mechanics, variant tables).
- **[Characters/](Characters/)** — One file per player character (Alex, Andrej, Coleton, Jose, Lee, Mia). Contains background notes and prompts the DM gave players for fleshing out backstories.
- **[Prologues/](Prologues/)** — Player-written or DM-written prologues for each character.
- **[Countries/](Countries/)** — One subdirectory per nation or region. The Cerisian Empire is the primary setting.
- **[GM_notes/](GM_notes/)** — DM-facing private notes: encounter ideas, NPC lists, spell drafts, species guides (common_species_v2 parts 1–3), and bestiary reference images.
- **[Language/](Language/)** — Language reference organized by rarity: `Common_Languages/` (Cerik, Eema, Raconta), `Uncommon_Languages/` (Ardulian, Celestial, Danumoc, Dwarven_Dialects), `Rare_Languages/` (Ardo, Dead_Eema, Grek, Mannoc, Southern_Hand_Language). Mirrors the structure of `GM_notes/Language/`.
- **[Magic/](Magic/)** — Custom spell definitions.
- **[Archive/](Archive/)** — Legacy .odt/.pdf versions of documents; treat as read-only history.

## World Quick Reference

**Primary setting:** The Cerisian Empire — a Theocratic Monarchy ruled by the Triamo (exclusively Aasimar bloodline), with a three-pillar noble structure: Herald Nobles (land/military), Ordained Nobles (church/old families), and the Triamo's direct ministries.

**Common language:** Cerik. Other major languages: Raconta, Ardulian, Grek, Ardo, Eema. Dead Eema (also called Demonic) is the language of summoning magic — no living native speakers.

**Species framing:** No species-based monocultures. Languages evolved around regional populations, not species. Aasimar are a recessive magical mutation, not divinely chosen (though the in-world belief says otherwise). Dragonborn are sapient lizards unrelated to dragons.

**Rule system:** D&D 5e-adjacent with significant homebrew — morale saves, mob/swarm mechanics, custom spell key terms (Sequence, Crystal, Shield), and spells tied to specific cultural traditions (Cerrik, Racontan spell schools).

**Players:** Alex (elf spy/soulknife), Andrej, Coleton, Jose, Lee, Mia.

## Enforced Lessons

Read **[.claude/Lessons.md](.claude/Lessons.md)** at the start of every conversation and apply all listed rules throughout the session.

## Conventions

- Spelling inconsistencies (e.g. "Cerisian" vs "Cersian", "Barret" vs "Barret") exist throughout and reflect organic document growth — do not silently "correct" them without flagging.
- GM_notes content is not player-facing; treat it as confidential campaign material.
- The `//feedback` inline comment style (seen in DM.notes.md) marks the DM's own design notes to themselves.
