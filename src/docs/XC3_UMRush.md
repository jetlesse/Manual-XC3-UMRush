# Xenoblade Chronicles 3 - Unique Monster Rush Archipelago

## Checks
- Defeat each Unique Monster
- Defeat a Unique Monster or Time Attack trial at level x * 10 or higher
- Complete each Time Attack challenge
- S-rank each Time Attack challenge

## Items
- Progressive Level Cap unlocks
    - Start at level 10, each Progresssive Level Cap increases level cap by 5
- Equipment
    - Number of gem slots, accessory slots, master arts and master skills
    - Start with 0 each and unlock up to 3
- Battle Mechanics
    - Add a hero to your party
    - Use chain attacks
    - Use Interlink
    - Use Unlimited Sword talent art
- Classes
    - Start with the original 6 classes
    - Each class unlock item corresponds to one of the rows in the Heros menu (Valdi is in row 1, Ashera is in row 2, ...)
- Access to new maps
    - Start in Aetia Region - Full
    - A region is unlocked when you receive the unlock item and have unlocked the regions needed to walk there (e.g. Pentelas region requires Pentelas and Fornis region access)

## Goal
Either defeat Seraphic Ceratinia (Lv 120 version) or complete "The Four Devas" Time Attack challenge.
- The choice is an option in the yaml
- The goal that is not chosen is not a check
- If Time Attack is excluded in the yaml options then the goal will always be Seraphic Ceratinia

## Logic
- You are expected to be able to defeat enemies up to 5 levels above your level cap
- You are expected to be able to complete a Time Attack challenge at the level cap the game sets
- Superbosses logically require you to have received all equipment unlocks and all battle mechanic unlocks, but not any regions

## Options
- Exclude Unique monsters from the certain regions
    - All regions (except Aetia Region - Full) can be set to be excluded, the unique monsters in that region are not included as locations
    - All superbosses are always locations
    - A minimum number of locations are required for generation to succeed so if too many locations are set to be excluded then some regions will be included anyway
        - Time Attack requires owning the DLC to play, so it will always be exluded first

## Notes
- Start the game by removing the hero from your party, turn Interlink control to Party in the game options, set main party to base classes, remove gem slots, accessory slots, master arts and master skills, set talent art to the original art, set party level to 10
- I suggest that the class unlock allows the main party to use those classes, the master arts, talent arts and master skills from those classes and for your hero to be that class
- You can't remove master arts from Unlimited Sword mode. It's up to you if you want to allow them if you don't have enough master art unlocks
- Level cap and number of accessories should also apply to your hero (the game doesn't allow you to remove their gems)
- Not having Interlink should also prevent you from using Interlink in chain attacks
- Skip travel directly to the gravestones is allowed, defeating this many unique monsters roughly on par with your level takes a long time already
- Force yourself to defeat all the other superbosses before finishing the goal if you want to make the game longer or more difficult