# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview
Hyper-cat is a Hyper terminal plugin that adds a nyan cat animation with rainbow trails while typing. The plugin overlays animated SVG cat parts and canvas-based rainbow effects on the terminal cursor.

## Critical Issue to Fix
The plugin breaks with newer Hyper versions due to API changes. The fix requires changing line 47 in index.js from:
```js
config = Object.assign(config, electron.remote.app.config.getConfig().hyperCat);
```
to:
```js
config = Object.assign(config, { staggerHeight: 2, rainbowMaxAlpha: 1, audioEnabled: false, videoEnabled: "whileTyping" });
```

## Known Theme Compatibility Issue
After applying the above fix, there's a visual issue where the terminal theme appears to obstruct the rainbow effect in the main terminal area, while the animated border works correctly. This suggests a z-index or layering conflict with certain Hyper themes.

## Architecture

### Main Components
- **index.js** - Core plugin logic that:
  - Decorates Hyper's Term component
  - Creates overlay canvas for rainbow effects
  - Positions SVG cat parts (head, legs, tail) relative to cursor
  - Manages audio playback based on configuration
  - Handles rainbow trail spawning and animation

### Key Functions
- `decorateTerm()` - Main entry point that wraps Hyper's Term component
- `initOverlay()` - Sets up canvas and SVG elements for animation
- `onCursorMove()` - Updates cat position and spawns rainbow segments
- `drawFrame()` - Animation loop that renders rainbow fade effects
- `spawnRainbow()` - Creates new rainbow trail segments

### Configuration System
The plugin expects configuration in ~/.hyper.js under `config.hyperCat`:
- `staggerHeight` - Pixels for cat bounce animation
- `rainbowMaxAlpha` - Maximum opacity for rainbow trails
- `audioEnabled` - Controls nyan audio (true/false/"whileTyping")
- `videoEnabled` - Controls animation (true/false/"whileTyping")

## Development Notes

### Testing the Plugin
1. Clone this repository
2. Run `npm install` to install the color dependency
3. In ~/.hyper.js, add the local path to plugins:
   ```js
   localPlugins: ['/path/to/hyper-cat']
   ```
4. Reload Hyper (Cmd+Shift+R on macOS)

### Debugging Theme Conflicts
- Check z-index values in the plugin's overlay elements
- Inspect the terminal's DOM structure for theme-specific wrappers
- Test with different Hyper themes to identify compatibility patterns

### No Build Process
This plugin has no build step - it's vanilla JavaScript that runs directly in Hyper's Electron environment.