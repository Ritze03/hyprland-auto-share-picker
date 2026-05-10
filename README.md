# hyprland-auto-picker

## Description
This script is a SELECTION-STRING supplier for XDPH. You can set it up, to automatically accept screencopy requests, making the Hyprland/Wayland experience more streamlined.

The priority of SELECTION-STRING's is as follows:
1. Temporary Selection (Gets returned once and is then cleared)
2. Default Selection (Always gets returned, if set)
3. Fallback: Manual Picker (Invokes the configured picker, to select a Source manually)

### Usage Examples
#### Only automatically pick the next Selection
This is perfect, if you use OBS as a clipping software and have it start automatically.
If no default is configured, other requests will go to your configured manual picker.

Example for OBS:
```bash
hyprland-auto-picker "[SELECTION]r/screen:DP-1"; obs
```
*Launch OBS and automatically allow screencopy with "DP-1" as a source.*

#### Always automatically pick a static Selection
In this setup, the Selection will always be the same and you never have to select anything. This is perfect for single screen setups.

```bash
hyprland-auto-share-picker --pick-default # This opens the default manual picker and sets its output as the default source
```

## Installation (Using the Wizard)
*This is the way to go, if you want to get it up and running in ~2 Minutes.*

#### 1. Clone the repo
```bash
git clone https://github.com/Ritze03/hyprland-auto-share-picker.git
cd hyprland-auto-share-picker
```

#### 2. Run the Installation Wizard
*The default values should work for most people. If you have no clue, keep the defaults!*
```bash
./hyprland-auto-share-picker --wizard
```

#### 3. Additional Information: Pick the default Selection
*This is already done during the wizard, but if you want to change the default later, use the following command:*
```bash
hyprland-auto-share-picker --pick-default
```

## Installation (Using Commands)
*Installing it manually will give you a good baseline to write simple Scripts.*

#### 1. Clone the repo
```bash
git clone https://github.com/Ritze03/hyprland-auto-share-picker.git
cd hyprland-auto-share-picker
```

#### 2. Install to `/usr/bin/`
```bash
./hyprland-auto-share-picker --install
```

#### 3. Modify the XDPH config

**Using the integrated Helper**
```bash
hyprland-auto-share-picker --xdph-config-helper
```

**Alternative: Print the configs and apply manually**
```bash
hyprland-auto-share-picker --xdph-print-configs
hyprland-auto-share-picker --xdph-apply-config <CONFIG_NAME/CONFIG_INDEX>
hyprland-auto-share-picker --xdph-restart
```

#### 4. Optional: Configure a custom picker (Uses the one shipped with Hyprland by default)
```bash
hyprland-auto-share-picker --set-picker <COMMAND>
```

#### 5. Manually get a SELECTION-STRING
```bash
hyprland-auto-share-picker --picker
```
*Copy the value, that was printed to the console. A valid SELECTION-STRING always starts with `[SELECTION]`*
*I will use `[SELECTION]r/screen:DP-1` as an example in the next steps.*

#### 6. Set a default Selection
```bash
hyprland-auto-share-picker --set-default "[SELECTION]r/screen:DP-1" # Sets the supplied SELECTION-STRING as the default selection
```

#### 7. Scripting: Set a temporary Selection
*Temporary Selections are only used once, making it perfect, to run before an application like OBS*
```bash
hyprland-auto-share-picker --set-temp "[SELECTION]r/screen:DP-1"; obs
```

***Pro Tip:*** *`--set-temp` can be omitted*
```bash
hyprland-auto-share-picker "[SELECTION]r/screen:DP-1"; obs
```

#### 8. Using Variables
*You can use variables, which get replaced on runtime. This means, that the default could always be your active monitor for example.*
```bash
hyprland-auto-share-picker --set-default "%ACTIVE-MONITOR-SELECTION-TOKEN%"
```

#### 9. Checking the Help Page
*I used the full argument names for readability, but you can always use the shortened ones.*
Check the Help Page, to see the full capabilities of this software.
```bash
hyprland-auto-share-picker --help
```

## Scripting
This script is made in a way to be highly scriptable. 
Commands return `ok` or `err` as a status codes.

### Example: Stream active monitor using OBS Virtual Camera
I use the OBS Virtual Camera, to stream my desktop using **TeamSpeak 6**, since normal streams crash my TeamSpeak frequently.
```bash
hyprland-auto-share-picker "%AMST%"; \
pkill obs && while pgrep obs >/dev/null;do sleep 0.1; done; sleep 1; \
obs --startvirtualcam --minimize-to-tray --disable-missing-files-check
```
*This sets the current monitor as a temporary source, kills OBS, and launches OBS with the VirtualCam enabled.*

**You have to bind this to a key, to make it useful!**

## Full Usage (Help Page)
You can check all the available commands using `hyprland-auto-share-picker --help`.

```bash
hyprland-auto-share-picker is a simple tool, that lets you automate your Source Selection for Recording and Screen Sharing, with a big focus on scriptability.

Usage: hyprland-auto-share-picker [OPTIONS]/<SELECTION-STRING>

Configs in /home/mo/.config/hyprland-auto-share-picker/:
Each Config File only contains a single value. Configs get checked in the same order as shown below.

 SELECTION-TEMP                              1. Returns the specified value as the SELECTION-STRING to XDPH and empties the file
 SELECTION-DEFAULT                           2. Returns the specified value as the SELECTION-STRING to XDPH
 MANUAL-PICKER                               3. Launches the specified MANUAL-PICKER to obtain the SELECTION-STRING

Options:
 -h, --help                                  Prints this help page
 -w, --wizard                                Installation Helper Wizard
 -i, --install                               Install to "/usr/bin/hyprland-auto-share-picker" (Requires sudo)
 -s, --selection <SELECTION-STRING>          Alias for '--set-temp'
 -p, --picker                                Launches the MANUAL-PICKER defined in file "MANUAL-PICKER"
 -a, --allow-token                           Passes the argument through to the MANUAL-PICKER, if it is invoked
 -xt, --pop-temp                             Prints and clears the specified SELECTION-STRING from file "SELECTION-TEMP"
 -gt, --get-temp                             Prints the specified SELECTION-STRING from file "SELECTION-TEMP"
 -st, --set-temp <SELECTION-STRING>          Writes the specified SELECTION-STRING to file "SELECTION-TEMP"
 -pt, --pick-temp                            Runs the MANUAL-PICKER and writes the SELECTION-STRING to file "SELECTION-TEMP"
 -ct, --clear-temp                           Clears the SELECTION-STRING from file "SELECTION-TEMP"
 -gd, --get-default                          Prints the specified SELECTION-STRING from file "SELECTION-DEFAULT"
 -sd, --set-default <SELECTION-STRING>       Writes the specified SELECTION-STRING to file "SELECTION-DEFAULT"
 -pd, --pick-default                         Runs the MANUAL-PICKER and writes the SELECTION-STRING to file "SELECTION-DEFAULT"
 -cd, --clear-default                        Clears the SELECTION-STRING from file "SELECTION-DEFAULT"
 -gp, --get-picker                           Prints the specified MANUAL-PICKER from file "MANUAL-PICKER"
 -sp, --set-picker <COMMAND>                 Writes the specified MANUAL-PICKER to file "MANUAL-PICKER"
 -pam, --active-monitor                      Prints the Port of the currently active monitor
 -pams, --active-monitor-selection           Prints the SELECTION-STRING for the currently active monitor
 -pamst, --active-monitor-selection-token    Prints the SELECTION-STRING for the currently active monitor with tokens allowed

 "[SELECTION]*" / "%VARIABLE%"               Writes the Selection String to the file "SELECTION-TEMP"

Advanced Options:
 -v, --version                               Prints the current Version
 --defaults                                  Sets the default values again in file "/home/mo/.config/hyprland-auto-share-picker/"
 --reset                                     Deletes and recreates the config folder in file "/home/mo/.config/hyprland-auto-share-picker/"
 --xdph-config-helper                        A helper for selecting one of the shipped configs
 --xdph-print-configs                        Prints all shipped configs [MINIMAL/RECOMMENDED/EXTENDED]
 --xdph-apply-config <CONFIG_NAME>           Applies the selected config to "/home/mo/.config/hypr/xdph.conf"

SELECTION-STRING Variables:
hyprland-auto-share-picker supports Variables in the SELECTION-STRING, these get replaced with their corresponding value
This happens at runtime, so you can place variables in configs. For scripting, use "$(hyprland-auto-share-picker --print-active-monitor-selection-token)"

 %AM%   / %ACTIVE-MONITOR%                   Gets replaced with the SELECTION-STRING of the currently active monitor
 %AMS%  / %ACTIVE-MONITOR-SELECTION%         Gets replaced with the SELECTION-STRING of the currently active monitor
 %AMST% / %ACTIVE-MONITOR-SELECTION-TOKEN%   Gets replaced with the SELECTION-STRING of the currently active monitor

Env:
 HASP_LOG=1                                  Enables logging

Examples:
 hyprland-auto-share-picker "[SELECTION]r%ACTIVE-MONITOR-SELECTION%"               # Writes the Selection String of the active window to
 hyprland-auto-share-picker -s "[SELECTION]r%ACTIVE-MONITOR-SELECTION-TOKEN%"      # Writes the Selection String of the active monitor to
 hyprland-auto-share-picker --pick-default                                           # Use the MANUAL-PICKER to set the default SELECTION-STRING in SELECTION-DEFAULT

 # Launches OBS and streams DP-1 to the virtualcam (uses the default scene in OBS)
 hyprland-auto-share-picker "[SELECTION]r/screen:DP-1"; \
 pkill obs && while pgrep obs >/dev/null;do sleep 0.1; done; sleep 1; \
 obs --startvirtualcam --minimize-to-tray --disable-missing-files-check
```
