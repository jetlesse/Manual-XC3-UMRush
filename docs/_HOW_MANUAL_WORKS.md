# How Manual Archipelago Projects Work: A Comprehensive Summary

This guide explains how Manual archipelago (apworld) projects function from start to finish, covering everything from the basic concept to advanced customization with hooks.

---

## Table of Contents

1. [What is Manual?](#what-is-manual)
2. [Core Philosophy & Concepts](#core-philosophy--concepts)
3. [Project Structure Overview](#project-structure-overview)
4. [Configuration Files Explained](#configuration-files-explained)
5. [Generation Process Step-by-Step](#generation-process-step-by-step)
6. [Gameplay with the Manual Client](#gameplay-with-the-manual-client)
7. [Customization via Hooks](#customization-via-hooks)
8. [Updating Your World](#updating-your-world)
9. [Best Practices & Tips](#best-practices--tips)

---

## What is Manual?

**Manual** is a customizable archipelago world (APWorld) template for Archipelago that allows you to create a multiworld integration for **any game**, without writing Python code or creating mods. You simply specify:
- A list of items players can receive
- A list of locations they can check off

The name "Manual" comes from the fact that it doesn't automatically randomize anything—you manually handle when locations are checked and limit what items you can use until received via multiworld. This is often called **"the honor system"**!

### What Can Manual Handle?

**Locations:**
- Locations within a game (most common)
- Goals in bingo-style formats
- Timer-based goals (like ArchipIdle or similar projects)

**Items:**
- Actual items found in the game
- Limiting factors that add challenge
- Arbitrary keys/gates for key-sanity style play modes

### Use Cases

Manual works well for:
- Video games across multiple genres, especially those with open locations or new game plus (NG+) modes
- Board or card games and other physical games (impossible to mod!)
- Non-game activities like challenges, workouts, etc.

---

## Core Philosophy & Concepts

### No Mods Required 🎮

Yes, there are **no mods**! You manually perform checks using the client and limit items via the item list in the client. This means you can play games that wouldn't normally allow modding (like board games or physical games).

You're essentially becoming "the engine" for your world! ✨

### Manual vs Automatic Randomization

| Feature | Traditional AP Worlds | Manual Worlds |
|---------|----------------------|---------------|
| **Item placement** | Fully automatic randomization | You manually check locations to receive items |
| **Logic checking** | Multiworld handles all logic checks | Client shows which location you should check next |
| **Filler items** | Randomized automatically from pool | Customizable or minimized by design choice |

### The Honor System

Players must:
1. Check off the correct location in the client when they complete it
2. Not use progression items for unlocking locations until received via multiworld

---

## Project Structure Overview

A typical Manual apworld has this structure:

```
your-world.apworld/              # Complete packaged world file (rename from .zip)
├── data/                        # Your custom world configuration (OVERWRITTEN on update!)
│   ├── game.json               # Game identification and settings ⭐ Required
│   ├── items.json              # Item definitions ⭐ Required
│   ├── locations.json          # Location definitions ⭐ Required
│   ├── events.json             # Event/tracking objects (Optional)
│   ├── categories.json         # Category groupings (Optional)
│   └── options.json            # YAML option configurations (Optional)
├── hooks/                       # Custom Python hook files (Optional but powerful!)
│   ├── __init__.py
│   ├── World.py               # Main generation hooks ⭐ Most important!
│   ├── Data.py                # Data customization hooks
│   ├── Helpers.py             # Helper method hooks  
│   ├── Options.py             # Option creation/hooks
│   └── Rules.py               # Custom requirement functions
├── manual.json                  # Manual-specific configuration (Optional)
├── meta.json                    # World metadata and webhost settings ⭐ Required
└── src/                         # Core Manual files from the base template
    ├── container.py
    ├── Data.py
    ├── Game.py
    └── ...                     # Don't edit these! Backup data/ before updates!
```

### The `data` Folder - Critical Information 💡

The **entire point** of having a custom world is that you only need to preserve the contents of your `data/` folder. Everything else comes from Manual's core files:
- `src/`, `hooks/`, and other template files get overwritten during updates
- Your unique content lives in `game.json`, `items.json`, `locations.json`, etc.
- On update, backup `data/` before replacing the entire `.apworld` file!

---

## Configuration Files Explained

### 🎮 game.json - Game Identification and Settings

Defines your world's core identity:

```json
{
    "game": "YourGameName",           // ⭐ REQUIRED - Unique name for this Manual version
    "creator": "YourUsername"         // ⭐ REQUIRED - Distinguishes different randomizer approaches
    
    // Optional settings...
    , "filler_item_name": "Gold Rings"      // Default filler item when needed
    , "death_link": false                   // Enable deathlink (false by default)
    , "starting_index": 1                  // ID starting number for items/locations
    , "starting_items": []                 // Items player starts with
}
```

**Important:** The `game` name must be unique to differentiate your Manual from others in the system. The `creator` field identifies who created this version, not you as a player!

---

### 📦 items.json - Item Definitions

Defines all checkable/receivable items:

```json
[
    {
        "name": "Item Name",                 // ⭐ REQUIRED (cannot contain : or |)
        
        // Optional... no classification = filler by default
        
        , "category": ["Cat1"]               // Category names for grouping
    
        , "trap": false                       // Can be trap item
        , "filler": true                      // Or fillers, useful items, etc.
        , "useful": true
    
        , "progression": true                 // Must use progression unlocks!
    
        , "count": 2                         // Number of copies to generate
        
        , "early": false                     // Early item for beatable worlds
        
        , "local": true                      // Keep in your world only
    }
]
```

**Item Classifications:**
- **filler**: Default, items that don't unlock progression or major assistance
- **progression**: Unlocks locations/critical to game progress (must use this for `requires`!)
- **useful**: Helpful but doesn't enable new areas/locations  
- **trap**: Can be used normally if found first place, problematic later

**Classification Mix:** For some items, you may want different classifications across copies:

```json
{
    "name": "Button Activation",
    "classification_count": {               // Overrides other classification settings!
        "progression": 12,                 // Most are progression unlocks (required)
        "useful + progression": 5          // Mix of useful and progress items
    }
}
```

---

### 📍 locations.json - Location Definitions

Defines all checkable/offable locations:

```json
[
    {
        "name": "Location Name",                     // ⭐ REQUIRED
    
        , "category": ["Cat1"]                       // Optional categorization
        
        , "victory": false                           // true = goal location (no items)
        
        , "region": "RegionName"                      // Group locations logically
    
        , "requires": "|ProgressionItem|",            // Logical requirements ⭐ Critical!
                .append("|@ProgressiveSword:5")         // Item counts syntax
                .append("|@MyCategory")                 // Category requirement
        
        // Optional hints...
        , "prehint": true                             // Add pre-hint during generation
    }
]
```

**Victory Locations:** Can have `victory: true` - when checked, the world ends. Use Manual's built-in YAML option to choose between multiple victory locations (e.g., different endings). **Always add requirements!** Without them, all content goes in sphere 0 immediately! ⚠️

---

### 📋 Optional Files Overview

**events.json:**
- Define "fake" items that track logical conditions without physical location checks
- Automatically activate when `requires` are met  
- Can copy existing locations using the `copy_location` property to save definition space

```json
[
    {
        "name": "Fake Item",           // Just for tracking purposes
        "category": ["Tracking"],      // How players see it
        
        , "count": 1                   // Always has one instance
    
        , "requires": "|SomeCondition|"          // Activates automatically when met
        
        , "copy_location": "ExistingLoc"         // Copy from existing location
    }
]
```

**categories.json:**
- Groups related items/locations/events for organized `@Category` requirements  
- Supports YAML option toggling via the special `yaml_option` property in schema objects  
- Can be hidden and not displayed to players if preferred

**options.json:**
- Define configurable options with various types:
  - **Toggle**: Boolean on/off settings (e.g., deathlink, specific mechanics)
  - **Choice**: Select from predefined values list  
  - **Range**: Numeric range selection for number-based configurations
  
```json
[
    {
        "name": "DeathLink",           // Option name
        "type": "bool"                 // Options: bool, choice, or range
    
        , "description": ""            // Description shown in client
        
        , "display_name": "Toggle Death Link?"  // Displayed label (can differ from name)
    }
]
```

---

## Generation Process Step-by-Step 🔄

When Multiworld imports your Manual APWorld, this is what happens:

### Phase 1: Import
The world loads into Archipelago's generation system. Your `src/` and template files load as the foundation.

### Phase 2: Create Regions ⭐ First Custom Hook Point
Manual reads from your locations.json and regions.json to organize all locations into spheres (logic layers).

**Hook Available:** `before_create_regions`, `after_create_regions` in hooks/World.py  
Use this hook to modify or add custom regions/locations!

### Phase 3: Create Items ⭐ Second Custom Hook Point
The item pool is built from your items.json, then Manual adds filler as needed for all unfilled locations.

**Hook Available:** `before_create_items_filler`, `after_create_items` in hooks/World.py  
Use this to customize the entire item pool including default fillers!

### Phase 4: Generate Early ⭐ Third Custom Hook Point
Client data, hints, and other pre-fill operations run here. This happens before items are placed into locations.

**Hook Available:** Various early generation hooks for customization

### Phase 5: Fill Logic ⭐ Fourth & Final Step
Multiworld places actual items into your chosen locations based on logic constraints (requires syntax). Filler fills remaining slots. Game is ready! ✨

---

## Gameplay with the Manual Client 🎯

After setting up and generating, use like this:

### Before Starting Your Playthrough
1. Download your world's `.yaml` and `.apworld` files from creator
2. Move `.apworld` to `custom_worlds/` in Archipelago installation  
3. Move `.yaml` (player file) to `Players/` folder or use player names like "Player"  
4. **(Recommended)** Download Universal Tracker companion app

### During Gameplay 🎮 → 🔍
1. Play your game normally (no manual placement needed!)
2. **Complete a location in-game** - don't check it yet! ⚠️
3. Open the Manual client and find which location you just completed  
4. Click "Check" to mark as complete → item(s) appear on next multiworld trip 🎁

### Limiting Items 🔒 → ✅ Progression
- **DO NOT** use progression items for other purposes until seen in your list! 😅  
- Client will show which location you should check next (guides you if needed!)  
- If you want to "break the rules" and just play normally, fine - that's part of it being Manual/honor system style

### Victory Location 🏆
When reaching goal:
1. Check victory location in client → world ends automatically OR...
2. Complete final objective → click victory location button ✨

---

## Customization via Hooks 🔧 ⚙️ 💻

Hooks are empty functions that Manual calls during generation, giving you customization points! 🎊

### Hook File Purpose Summary

| File | Primary Use Case | Best For Beginners? |
|-------|------------------|--------------------|
| `hooks/__init__.py` (apworld root) | World import code like custom client registration | ⭐ Yes - simple imports |
| **hooks/World.py** | Main generation customization hooks (**most important!**) | ✅ Start here first |
| hooks/Data.py | Customize raw data from JSON template files | 🔵 Intermediate use case  |
| hooks/Helpers.py | Add custom logic for helper methods (enable/disable checks) | 🔴 Advanced / rare need |
| hooks/Options.py | Create or customize YAML options | ⭐ Yes - if using many toggles/choices! |
| **hooks/Rules.py** | Define custom requirement functions | 🔵 For complex requires syntax |

---

### World.py Hooks (Primary File) ✨

Most customization goes in this file for the main generation steps:

```python
# ⭐ Modify regions before creation
def my_before_create_regions():
    # Your code here... can add new locations, modify requirements
    
pass

# 🎯 Customize after regions are created  
def my_after_create_regions(regions):
    regions[0].locations.add(Location("Custom Location"))  # Add custom location!
    
pass

# 📦 Hook into item pool creation (before filler is added)
def my_before_create_items_filler():
    pass
    
    def _get_progression_item_classification(item: Item, count_left: int):
        return "progression" if ... else "useful"  # Custom classification logic!
    
pass

# 🎁 Customize after items are placed (post filler addition)  
def my_after_create_items():
    pass
    
def _get_filler_item_name() -> str:
    return "MyCustomFillerName!"  # Override default filler name
    
pass
```

---

### Options.py Hooks ⚙️

Creating and customizing YAML options uses `after_options_defined` hook:

```python
# In hooks/Options.py (and your world's root Options.py!)...

def after_options_defined():             # ⭐ Create new or modify existing options!
    OptionToggle("MyNewOption",            # Toggle option example: deathlink alternative  
                  display_name="Enable Death Link?", 
                  description="",           # Description shown in client settings  
                  default=False,           
                  hidden=True)                    # Can be visible to players optionally
    
    def get_death_link():                 # Example customization of existing option
        if opt.MyNewOption or other_condition:
            return True                   # Custom logic for deathlink behavior!
    
pass

# Choice options (dropdown menus in client)...
def after_options_defined2():             
    OptOne("MyChoice",                     # Single selection dropdown  
            choices=["Value 1", "Value 2"], 
            default="Default Value")             
    
```

---

### Rules.py Hooks 📜

Define custom requirement functions for more powerful `requires` syntax:

```python
# In hooks/Rules.py (and your world's root Rules.py!)...

from typing import Union                  # Import type hints

def ItemValue() -> Union[bool, str]:      # Returns bool or item count string  
    """Returns whether the player has enough items matching name"""
    
    def _get_item_count(name: str):       
        return 5                          # Custom function for value-based checks
    
pass

# Example custom functions provided in template file...
def OptOne():                             # Use with "||" operator
    """Check if at least one option has the item/condition"""
    
def OptAll():                            # Require ALL options to have it  
```

---

### How Hook Functions Are Called 🔍 📞 🎪 ⭐ **Advanced**

Hooks run automatically during generation. When you define them:

1. You provide custom logic inside hook functions, typically by extending existing ones with your own code
2. Manual calls these at each stage of APWorld creation  
3. This enables adding functionality beyond what JSON syntax alone allows! 🚀 💪 ⭐ **CRUCIAL**: Always read through the base implementation before defining hooks to understand their purpose and arguments

**Reading Code for Hooks:** To write your own custom hook, you need to:
1. Read `src/__init__.py` (non-hook folder) in your apworld  
2. Find where Manual calls generation steps internally using those functions  
3. Understand the full process before adding hooks! For Archipelago internals questions, ask on their Discord server  

---

## Updating Your World 🔄 ⬆️ ✅

### Automatic Update Process (Recommended Method - Safest!) ✨

1. Go to https://manualforarchipelago.github.io/ManualBuilder/
2. **Import** your world into Manual Builder's online tool  
3. Click **"Export"** or "Download" button for updated version ⬇️
4. Replace old apworld file with new one (rename from .zip if needed)

### Automatic Update Failure / Custom Handling 🛠️ 🔧

If automatic updates don't work, follow these steps manually:

1. Rename `.apworld` → extract to `folder.zip/your-world-filename.apworld`
2. **BACKUP** the entire `/data/` folder (⚠️ and hooks if using custom ones!) 📦 💾
3. Download latest release .apworld from GitHub releases page  
4. Extract downloaded apworld → copy contents into your extracted world
5. Copy backup `data/` folder overwriting released version's data ⭐ **CRITICAL STEP!**
6. Zip up the updated folder again (replace original file)

---

### Common Pitfalls 🚫

- **Don't edit** files in `/src/`, `/hooks/` - they get overwritten on update!  
- Always backup your `data/` before updating ⬇️  💾
- Check Manual releases page: https://github.com/ManualForArchipelago/Manual/releases ✨ ✅ 📋

---

## Best Practices & Tips 🌟 🎯 💡 ⭐ **IMPORTANT**

### Minimizing Filler Items 👆 🗑️ 🔥 1. Reduce location count
- If game has many natural checkpoints, consider reducing locations in `locations.json`
- Add duplicates of progression items for better item pacing  
2. Use "better filler" concepts: minor gameplay improvements as fillers (extra lives/HP/refills) instead of useless junk! ✨ 🎁 💪

### Logical Requirements Must Be Used ⚠️ ✅ **CRITICAL** 🔑 1. Every location should use `requires` to prevent sphere overload  
2. Without requires, all content dumps into Sphere 0 → ruins player experience 😅
3. Syntax examples:
```json
"requires": "|ProgressionItem|@MyCategory"                 // Item name OR @category format    
            .append("|Coin:25")                           // "Name:Count" syntax for amounts            
            .append("||OtherLocation:")                   // Pipe (OR) operator example
            
// Boolean operators... && is AND || is OR ! negates conditions
```

### Region Organization 🗺️ 📍 ⭐ 1. Use regions to organize logical location groupings  
2. Each region can have multiple locations sharing `requires` across different areas
3. Mark starting regions with `"starting": true` in JSON schema objects for clear player guidance! ✅  

---

## Additional Resources 🔎 👨‍💻 🌐 1. **Official Documentation**: https://github.com/ManualForArchipelago/Manual/tree/main/docs  
2. **Schema Files** at /schemas/: View allowed properties and values:  
   - Manual.locations.schema.json (location definitions)
   - Manual.items.schema.json (item classification rules)

---

### Quick Reference for Common Questions 💬 ❓ 1. "What is Manual again?" = Customizable APWorld with no automatic randomization, manually checking locations in client ✨ 🎯  
2. "Do I need Python skills?" No! Just JSON syntax and understanding the honor system ⚖️
3. "How do updates work?" → Backup data/ folder, import via Manual Builder online tool 🔧 4. For Discord support: manualforarchipelago.github.io/discord.gg link (from docs) 🌐 ✉️  
5. **Important**: Game names in APWorld = creator identifier, NOT you! Change this only when creating from scratch 😅

---

*This summary consolidates official Manual documentation for Archipelapo.*