# PowerShell Auto-Clicker for Microsoft Teams

## Overview

This PowerShell script automatically executes mouse clicks at regular intervals. Its primary purpose is to keep the Microsoft Teams status active and prevent automatic switching to "Away". The script uses the Windows API (`user32.dll`) to simulate natural mouse events and is controlled via a global hotkey (F1).

---

## Features

- **Automatic Clicks**: Performs a left mouse click at the current mouse position every 5 seconds by default
- **Global Hotkeys**:
  - `F1` → Toggle auto-clicker on/off
  - `Ctrl + C` → Terminate the script completely
- **Clean Resource Management**: Registered hotkey is automatically released via `UnregisterHotKey` when exiting

---

## Requirements

- **Operating System**: Windows (tested on Windows 10/11)
- **PowerShell**: Minimum version 5.x (Windows default)
- **Execution Rights**: Script execution must be allowed

### Adjusting ExecutionPolicy

If necessary, adjust the execution policy:

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

---

## Usage

### 1. Installation

Save the script as a file, e.g., `teams-autoclicker.ps1`

### 2. Open PowerShell

```powershell
cd C:\Path\to\your\Script
```

### 3. Start Script

```powershell
.\teams-autoclicker.ps1
```

### 4. Controls

After starting, the script displays the message:
```
Auto-Klicker bereit. Drücke F1 zum Start/Stop, Strg+C beendet.
```

Control commands:
- **F1** → Start/stop auto-clicker
  - On start: `Auto-Klick AKTIV (alle 5 Sekunden) - erneut F1 zum Stoppen.`
  - On stop: `Auto-Klick PAUSIERT.`
- **Ctrl + C** → Exit script

**Important**: Position the mouse on a safe area (e.g., desktop background) during execution to avoid unintended actions.

---

## Configuration

### Adjusting Click Interval

Default setting (every 5 seconds):

```powershell
Start-Sleep -Seconds 5
```

For longer intervals (e.g., 10 seconds):

```powershell
Start-Sleep -Seconds 10
```

**Note**: Very short intervals will keep the mouse practically busy at all times - use with caution!

---

## Technical Details

The script works based on the following principles:

### C# Integration in PowerShell

The script uses `Add-Type` to embed C# code directly into PowerShell:

1. **Clicker Class**: Wrapper for `mouse_event` from `user32.dll`
2. **HotKey Class**: Functions for hotkey registration and message processing
   - `RegisterHotKey`: Registers F1 as a global hotkey
   - `UnregisterHotKey`: Releases the hotkey on exit
   - `PeekMessage`: Checks for hotkey events in the message queue
   - `MSG` Structure: Windows message structure for event handling

### Mouse Event Simulation

Left click is simulated by two consecutive `mouse_event` calls:
- `0x0002` → MOUSEEVENTF_LEFTDOWN (press mouse button)
- `0x0004` → MOUSEEVENTF_LEFTUP (release mouse button)

### Hotkey Configuration

- **Hotkey ID**: 1
- **Virtual Key Code**: 0x70 (F1)
- **Windows Message**: 0x0312 (WM_HOTKEY)
- **Modifiers**: None (0) - F1 without additional keys

### Event Loop

The script runs in a `try-finally` loop:
- Continuous checking for F1 hotkey via `PeekMessage`
- Toggle mechanism: F1 switches between active and paused state
- In active mode: Click every 5 seconds at current mouse position
- In paused mode: Short wait time (200ms) to save CPU
- `finally` block ensures the hotkey is always released

### System Behavior

The script behaves like a normal user clicking periodically. Clicks occur at the current mouse position (no coordinate parameters).

**Note**: Detectability by monitoring or compliance solutions depends on the specific environment.

---

## Legal and Organizational Notes

⚠️ **Important Warning**:

- Using this tool may violate policies of your employer, organization, or educational institution
- You are solely responsible for knowing and complying with applicable rules
- This code is provided **without any warranty**
- There is **no guarantee** regarding inconspicuousness, undetectability, or compatibility with organizational policies

**Use at your own risk.**
