# PoE Watcher

![GitHub](https://img.shields.io/github/license/yourusername/poe-watcher)
![Python](https://img.shields.io/badge/python-3.7+-blue.svg)

**PoE Watcher** is a Windows system tray utility that automatically detects and manages trading tools for Path of Exile 1 and Path of Exile 2.

## Features

-  **Automatic Detection** - Detects PoE 1 vs PoE 2 by reading window titles
-  **Smart Tool Management** - Launches the correct trading tool for each game version:
  - **PoE 1** → [Awakened PoE Trade](https://snosme.github.io/awakened-poe-trade/)
  - **PoE 2** → [Exiled Exchange 2](https://kvan7.github.io/Exiled-Exchange-2/)
-  **Auto-Install Helper** - Opens download pages if tools aren't installed
-  **Auto-Cleanup** - Kills tools when game exits or version changes
-  **Configurable Delay** - Set startup delay (5s, 10s, or 15s)
-  **Live Status Display** - Real-time status submenu with visual indicators
-  **System Notifications** - Get notified when tools start/stop
-  **System Tray Integration** - Runs quietly in the background

## Prerequisites

- Windows 10/11
- Python 3.7+ (for building from source)
- Path of Exile 1 and/or Path of Exile 2

## Quick Start

### Option 1: Download Pre-built Executable (Recommended)

1. Download `PoE Watcher.exe` from the [Releases](https://github.com/0xE666/path-of-exile-watcher/releases) page
2. (Optional) Place your custom `favicon.ico` in the same folder
3. Run `PoE Watcher.exe`
4. The icon will appear in your system tray

### Option 2: Build from Source

**Why build from source?**
- Verify the code yourself (no need to trust pre-built executables)
- Customize settings before compiling
- Add your own features

#### Step-by-Step Build Instructions:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/poe-watcher.git
   cd poe-watcher
   ```

2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```
   
   This installs:
   - `psutil` - Process monitoring
   - `Pillow` - Image handling for the icon
   - `pystray` - System tray integration
   - `pyinstaller` - Converts Python script to .exe

3. **Build the executable:**
   ```bash
   pyinstaller --onefile --windowed --icon=favicon.ico --name "PoE Watcher" poe_watcher.py
   ```
   
   **Command breakdown:**
   - `--onefile` - Bundles everything into a single .exe (no dependencies needed)
   - `--windowed` - No console window (runs silently in system tray)
   - `--icon=favicon.ico` - Sets the icon for the .exe file
   - `--name "PoE Watcher"` - Names the output file "PoE Watcher.exe"
   - `poe_watcher.py` - The Python script to compile

4. **Find your executable:**
   - Location: `dist/PoE Watcher.exe`
   - This is your standalone executable - no Python installation needed to run it!

5. **Optional: Clean up build files:**
   ```bash
   # PyInstaller creates these folders during build
   rmdir /s /q build
   del "PoE Watcher.spec"
   ```

**Build time:** ~30-60 seconds depending on your system.

## Run on Windows Startup (Recommended)

To automatically start PoE Watcher when Windows boots:

1. Press `Win + R` and type: `shell:startup`
2. Copy `PoE Watcher.exe` (or a shortcut) into the Startup folder
3. PoE Watcher will now start automatically with Windows

**Performance Impact:** PoE Watcher is extremely lightweight and uses minimal system resources:
- **RAM Usage:** ~15-25 MB
- **CPU Usage:** <1% (only active when monitoring)
- **No impact on gaming performance** - runs silently in the background

## Usage

### System Tray Menu

Right-click the system tray icon to access:

- **restart watcher** - Restart the monitoring thread
- **start awakened now** - Manually launch Awakened PoE Trade
- **start exiled exchange 2 now** - Manually launch Exiled Exchange 2
- **kill all tools** - Terminate all trading tools
- **check status**
  - `✓ PoE 2 running` / `✗ not running`
  - `✓ Awakened: installed & running`
  - `○ Awakened: installed` (not running)
  - `✗ Awakened: not installed`
  - Same for Exiled Exchange 2
- **set delay**
  - 5 seconds
  - 10 seconds
  - 15 seconds ✓
- **watching enabled** ✓ - Toggle automatic detection
- **force run next time** - Force tool launch on next PoE start
- **notifications enabled** ✓ - Toggle system notifications
- **exit** - Close PoE Watcher

### Status Indicators

- **✓** = Running / Installed & Running (Good)
- **○** = Installed but not running (Partial)
- **✗** = Not running / Not installed (Bad)

## Configuration

### Tool Paths

PoE Watcher automatically detects tools using Windows environment variables, so it works for **any username**:

**Awakened PoE Trade:**
```python
AWAKENED_EXE = os.path.join(
    os.environ["LOCALAPPDATA"],  # Expands to: C:\Users\{YourUsername}\AppData\Local
    "Programs",
    "Awakened PoE Trade",
    "Awakened Poe Trade.exe"
)
```

**Exiled Exchange 2:**
```python
EXILED_EXCHANGE_2_EXE = os.path.join(
    os.environ["LOCALAPPDATA"],  # Expands to: C:\Users\{YourUsername}\AppData\Local
    "Programs",
    "Exiled Exchange 2",
    "Exiled Exchange 2.exe"
)
```

**To use custom installation paths:**

If your tools are installed elsewhere, edit `poe_watcher.py` before building:

```python
# Example: Custom paths
AWAKENED_EXE = r"D:\Games\Tools\Awakened Poe Trade.exe"
EXILED_EXCHANGE_2_EXE = r"C:\MyTools\Exiled Exchange 2.exe"
```

Then rebuild the executable with PyInstaller.

### Default Settings

You can modify default settings in `poe_watcher.py`:

```python
delay_seconds = 15              # Startup delay (seconds)
watcher_enabled = True          # Auto-detection on/off
notifications_enabled = True    # System notifications on/off
```

## How It Works

1. **Process Detection** - Monitors for PoE processes:
   - `PathOfExile.exe`
   - `PathOfExile_x64.exe`
   - `PathOfExileSteam.exe`
   - `PathOfExile_x64Steam.exe`

2. **Version Detection** - Reads window titles using Windows API:
   - Contains "Path of Exile 2" → PoE 2
   - Contains "Path of Exile" (without 2) → PoE 1

3. **Tool Management**:
   - Waits for configurable delay (default 15s)
   - Launches appropriate trading tool
   - Monitors game process
   - Cleans up when game exits

## 🛠️ Development

### Project Structure

```
poe-watcher/
├── poe_watcher.py      # Main script
├── favicon.ico         # System tray icon
├── requirements.txt    # Python dependencies
├── README.md          # This file
└── .gitignore         # Git ignore rules
```

### Dependencies

- `psutil` - Process monitoring
- `Pillow` - Image handling
- `pystray` - System tray integration
- `pyinstaller` - Executable building

### Building

```bash
# Install dev dependencies
pip install -r requirements.txt

# Build executable
pyinstaller --onefile --windowed --icon=favicon.ico --name "PoE Watcher" poe_watcher.py

# Output will be in dist/PoE Watcher.exe
```

## Credits

- **Trading Tools:**
  - [Awakened PoE Trade](https://snosme.github.io/awakened-poe-trade/) by SnosMe
  - [Exiled Exchange 2](https://kvan7.github.io/Exiled-Exchange-2/) by kvan7


---
