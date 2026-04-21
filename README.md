# macro-recorder-mod-final
MADE BY ODDKHAN420
# Macro Recorder Mod - Fabric 1.21.1

A powerful Minecraft Fabric mod that allows you to record and replay keyboard & mouse actions with millisecond precision.

## Features

- **Record keyboard and mouse actions** - Capture key presses, releases, mouse clicks, and scroll wheel events
- **Replay macros with precise timing** - Millisecond-accurate delays between actions
- **Multiple named macros** - Create and save different macro sets for different tasks
- **Per-macro custom keybinds** - Assign unique toggle keys to each macro
- **User-friendly GUI** - Manage all your macros from an intuitive interface
- **Real-time status display** - See recording/playback status on your HUD
- **Stop playback anytime** - Press the play key again to cancel mid-execution

## Default Keybinds

| Key | Action |
|-----|--------|
| **R** | Start/Stop Recording |
| **P** | Play/Stop Macro Playback |
| **Backspace** | Clear saved macro |
| **G** | Open Macro Manager GUI |

All keybinds can be customized in Minecraft's Controls settings under "Macro Recorder".

## Installation

1. Install [Fabric Loader 0.17.3+](https://fabricmc.net/)
2. Install [Fabric API 0.116.9+1.21.1](https://modrinth.com/mod/fabric-api)
3. Download the latest `macromod-1.0.0.jar` from the releases page
4. Place it in your `.minecraft/mods/` folder
5. Launch Minecraft with the Fabric profile

## Usage

### Quick Start (Keyboard Only)

1. **Start Recording**: Press **R** - you'll see `§cRecording [Macro Name]` in chat
2. **Perform Actions**: Do whatever you want to record (move, click, interact, etc.)
3. **Stop Recording**: Press **R** again - your actions are saved
4. **Play Macro**: Press **P** - the macro executes with the exact timing
5. **Stop Playback**: Press **P** again to cancel mid-playback
6. **Clear Macro**: Press **Backspace** to delete the recording

### Using the Macro Manager GUI

#### Opening the GUI
- Press **G** to open the Macro Manager interface
- The game continues running while the GUI is open (not paused)

#### Creating Macros
1. Type a name in the **"Active macro:"** text field at the top
2. Click the **"+ New"** button to create a new macro with that name
3. Click the **"● Record"** button to start recording actions
4. Click it again to stop and save the macro

#### Renaming the Active Macro
- Change the name in the **"Active macro:"** text field
- Next time you record, the new name will be used

#### Recording While GUI is Open
- Click the **"● Record"** button in the GUI to start/stop recording
- You can see real-time feedback in the status area

#### Managing Saved Macros

Each saved macro appears as a row in the scrollable list with the following information:
- **Macro Name** - The name of your saved macro
- **Action Count** - Number of recorded actions (in parentheses)
- **Bound Key** - The currently assigned toggle key (or "None")

**For each macro, you have 3 buttons:**

| Button | Function | Color |
|--------|----------|-------|
| **Bind** | Click to enter keybind mode, then press any key to assign a toggle for this macro. Press **Esc** to clear the binding. | Orange |
| **▶** | Click to play this macro immediately (or stop it if already playing) | Green |
| **✕** | Click to delete this macro permanently | Red |

#### Scrolling the Macro List
- Use your **mouse wheel** to scroll through your saved macros
- If you have many macros, a "Scroll to see more" message appears at the bottom

#### Selecting a Macro
- Click anywhere on a macro's row (except buttons) to select it as the active macro
- Selected macros appear with a **yellow** highlight
- You can then record new actions into the selected macro

#### Binding Custom Keybinds
1. Click the **Bind** button next to your desired macro
2. The status message changes to "Press any key to bind to [Macro Name]"
3. Press any key (F, C, X, etc.) to assign that key as a toggle
4. Press **Esc** instead to clear any existing keybind
5. Once bound, you can press that key in-game to toggle the macro on/off

#### Status Indicators
The center of the GUI shows different messages depending on what's happening:
- **Recording Mode**: `§c● RECORDING [Macro Name]...` (red)
- **Playback Mode**: `§a▶ PLAYING [Macro Name]...` (green)
- **Keybind Mode**: `§ePress any key to bind to [Macro Name] (Esc to clear)` (yellow)
- **Normal Mode**: Shows saved macro count and help text (gray)

#### Closing the GUI
- Click the **"Close"** button to exit the Macro Manager
- Or press **G** again to toggle it closed

## Building from Source

### Requirements
- Java 21 JDK
- Gradle (included via wrapper)

### Build Steps

```bash
# Clone the repository
git clone https://github.com/Oddkhan-420/macro-recorder-mod.git
cd macro-recorder-mod

# Build the mod JAR
./gradlew build

# Output location:
# build/libs/macromod-1.0.0.jar
```

## How It Works

### Recording Mechanism
- Two Mixins intercept Minecraft's internal input callbacks:
  - **KeyboardMixin** - Hooks `Keyboard#onKey()` for key events
  - **MouseMixin** - Hooks `Mouse#onMouseButton()` and `Mouse#onMouseScroll()`
- When recording is active, each input event creates a `MacroAction` object
- The action stores: event type (key press/release, mouse click/scroll), key code, and a timestamp relative to when recording started
- All actions are stored in a `List<MacroAction>` until you stop recording

### Playback Mechanism
- Runs on a **background daemon thread** to avoid freezing the game
- Iterates through saved actions and calculates the delay between each one
- Uses `Thread.sleep()` to reproduce the exact timing
- Dispatches each action to the main game thread via `MinecraftClient.execute()`
- Calls the same GLFW callback methods that real inputs use, so the game can't tell the difference

### Storage
- Macros are stored in a `LinkedHashMap<String, List<MacroAction>>`
- Per-macro keybinds are stored in a `Map<String, Integer>` (macro name → GLFW key code)
- Currently stored only in memory (not persisted to disk between sessions)

## Notes & Limitations

- **Single-playback only** - Press P again after the macro finishes to loop it
- **No mouse movement recording** - Only mouse clicks and scroll wheel are captured
- **No persistence** - Macros are lost when you exit the game (consider adding a save/load feature!)
- **Control keys in macros** - If you record while holding R/P/Backspace, those inputs will be replayed. Use different keybinds if needed
- **In-game only** - Macros only capture inputs while in-game; menus and chat are excluded
- **GUI doesn't pause game** - You can manage macros while playing (useful for PvP servers)
- **Mixin conflicts** - Some mods may conflict with the keyboard/mouse mixins (rare)

## Troubleshooting

### Mod doesn't load (Crash on startup)
- Ensure you have Fabric Loader and Fabric API installed
- Check your `.minecraft/logs/latest.log` for mixin errors
- Verify Java 21+ is installed

### Macros don't play
- Make sure you're in-game and the game window is focused
- Check that you didn't accidentally delete the macro
- Try rebinding the macro's keybind

### Game lags during playback
- This is normal for complex macros with many actions
- Consider recording shorter macros
- Close other background applications

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Contributing

Found a bug? Have a feature idea? Feel free to open an issue or pull request on GitHub!
