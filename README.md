# Omni CNC

English | [简体中文](README.zh-CN.md)

Welcome, CNC, automation, and DIY enthusiasts!

Omni CNC is control software for CNC learning, simulation, and research. This
repository distributes Debian / Ubuntu DEB packages, release notes, and usage
guides, so users do not need to build the software themselves. Available packages,
supported platforms, and known issues are documented in the corresponding release notes.

> [!WARNING]
> **This project is under development and is intended only for learning, research, and SIM simulation. Connecting it to real machinery or using it for actual machining or production is strictly prohibited.**
>
> The software has not completed safety and reliability validation on real machinery.
> Control failures, software errors, and data loss may occur. Keep it physically
> disconnected from real machine tools, servo drives, spindles, and hazardous output
> devices. Do not perform real-machine jogging, homing, test cuts, or machining.
> **Anyone who uses the software in violation of these restrictions assumes responsibility for resulting personal injury, equipment damage, property loss, data loss, and other consequences.**
> Successful simulation, passing configuration checks, or meeting the recommended
> cycle-jitter target does not mean that connecting real machinery is permitted.

## Features

The current development version provides the following features in SIM mode.
Exact availability depends on the release.

| Feature | Description |
| --- | --- |
| Programs and toolpaths | G-code loading, toolpath preview, simulated execution, and execution status |
| Multi-axis simulation | Multi-axis motion, kinematics configuration, and trajectory planning, including five-axis features |
| User interfaces | Local Qt and Web interfaces displaying coordinates, programs, alarms, and device status |
| System configuration | A graphical wizard for selecting the platform and machine type and configuring axes, I/O, and other parameters |
| Tool management | Tool configuration, usage records, and tool-life information |
| Monitoring and diagnostics | Runtime monitoring, alarms, system events, and operation records |
| CAM-assisted programming | Facing, hole machining, threaded holes, image/sketch/SVG-to-program conversion, tangential cutting, and tangential following for friction stir welding |

See the corresponding release notes for supported G-code commands, available CAM
modules, and feature limitations.

## Screenshots

These screenshots show a development version. Layouts, labels, and available
features depend on the release.

### Initialization and system configuration

Select an installation template, platform, and SIM backend, then review the
configuration validation results.

![Initialization and system configuration with the SIM backend selected](doc/setup-wizard.png)

### Qt interface

The local interface displays coordinates, execution status, program files, and a
3D toolpath preview. The screenshot uses the SIM backend.

![Qt interface showing SIM status, coordinates, and a 3D toolpath preview](doc/1.png)

### CAM toolbox

Select an assisted-programming module from the toolbox. Items marked as coming
soon (预告即将面世) are not yet available. See the release notes for available modules.

![CAM toolbox and assisted-programming modules](doc/2.png)

### Web interface

View coordinates, task summaries, and alarms in a browser and access the available
controls. The screenshot uses the SIM execution backend. The default local address
is [http://127.0.0.1:8080](http://127.0.0.1:8080). See “Web access” below for access
from other devices.

![Web interface showing SIM status, coordinates, task summary, and controls](doc/Screenshot%202026-10-27%20at%2018.31.49.png)

## Hardware requirements

| Item | Requirement or recommendation |
| --- | --- |
| CPU architecture | 64-bit x86 (`amd64`) or 64-bit ARM (`arm64`), with a matching installation package |
| x86 CPU | The current generic x86 configuration requires at least 4 logical CPUs |
| ARM platform | RK3588 / Orange Pi 5 Plus platform configurations are available; use an OS image and package matching the board |
| Memory | 8 GB as a reference configuration; allow more for complex previews, large programs, or vision processing |
| Storage | An SSD is recommended, with approximately 20 GB free for software, release copies, programs, and logs |
| Display | A Linux graphical desktop and working Qt / OpenGL drivers; 1920 × 1080 recommended |
| Input devices | Mouse and keyboard; touchscreen compatibility must be checked for each device |

Memory and storage figures are configuration guidance, not published, measured
minimum requirements. CPU model and core count alone do not guarantee cycle-jitter
performance. No real machine or fieldbus slaves need to be connected for evaluation.

## Operating system and dependencies

### Choosing an operating system

**We recommend installing an official LinuxCNC PREEMPT-RT OS image before installing Omni CNC.**

- [Official LinuxCNC downloads](https://linuxcnc.org/downloads/)
- [Official LinuxCNC installation documentation](https://linuxcnc.org/docs/html/getting-started/getting-linuxcnc.html)

Choose an image matching your hardware and Omni CNC package, follow the official
instructions to install it to disk, and log into the graphical desktop. Back up
personal files before installing the OS. This recommendation refers to the complete
OS image; installing the LinuxCNC software package alone is not equivalent.

You may also prepare a compatible Debian / Ubuntu system yourself, but you will
need to resolve environment compatibility and cycle-jitter issues.
**Cycle jitter below 50 μs (microseconds) under realistic load is recommended**;
consider the maximum observed value when evaluating results. A LinuxCNC image does
not guarantee that every hardware configuration meets this target, and this value
is not a safety acceptance criterion for real machinery.

Report installation or runtime problems through this repository's Issues.

### Installing dependencies

- **You must install IgH yourself, including for SIM mode.** Before installing
  Omni CNC, prepare an IgH EtherCAT Master and dependencies matching your kernel,
  CPU architecture, and platform. These include `ethercat-master` and
  `libethercat-dev`, or an equivalent installation providing the required tools,
  headers, and libraries.
- **The DEB installation process automatically checks and installs other runtime
  dependencies.** Keep your network connection and the appropriate APT repositories available.
- The DEB does not automatically install IgH or a real-time kernel, bind a network
  adapter, or configure slaves. Installing IgH dependencies does not require real
  fieldbus devices. Connecting real machinery remains strictly prohibited.

## Installation

### 1. Prepare the operating system and IgH

Install the OS and IgH as described above. ARM boards require an OS image matching
the board. Do not use an x86 PC ISO or assume that another ARM board's image is compatible.

### 2. Choose a package

Download a DEB from the release attachments or version directories in
[this repository](https://github.com/zhangqiangauto/omni-cnc), and read the release
notes for that version. Choose a package matching all of the following:

- Distribution and version, such as Debian 12, Debian 13, or the corresponding Ubuntu version.
- CPU architecture: `amd64` or `arm64`.
- Hardware platform: `generic` or a platform specified in the release notes.

Package filenames generally follow this pattern:

```text
keniucnc_<version>_<distribution>_<hardware-platform>_<architecture>.deb
```

The distribution version refers to the OS actually used by the image, not the
LinuxCNC application's version. Packages for different distributions, OS versions,
or architectures are not interchangeable. Platform-specific packages may require
particular CPU instruction sets; matching the architecture alone is insufficient.

Check your OS and architecture with:

```bash
cat /etc/os-release
dpkg --print-architecture
```

### 3. Inspect and install the DEB

Open a terminal in the directory containing the package and replace the filename
below with the actual downloaded filename:

```bash
DEB_FILE='./your-downloaded-package.deb'
dpkg-deb --field "$DEB_FILE" Package Version Architecture Depends
sha256sum "$DEB_FILE"
```

Verify that the package name is `keniucnc` and that the version and architecture
match your intended download. If the release notes provide a SHA-256 checksum,
compare it with the calculated value. Then run in the same terminal:

```bash
sudo apt update
sudo apt install "$DEB_FILE"
```

Installation handles runtime dependencies other than IgH. If dependencies cannot
be satisfied or another error occurs, retain the complete error message and open
an Issue. Do not force installation while ignoring dependencies.

### 4. Launch and explore simulation

The product is named Omni CNC, while packages, commands, and desktop entries
currently retain the `keniucnc` / **KeniuCNC** names.

1. Open **KeniuCNC System Configuration** (KeniuCNC 系统配置) from the desktop
   applications menu. Check the platform, machine type, and **SIM** operating mode.
   The factory template is a generic x86 five-axis machine; check the platform
   selection especially carefully on ARM devices.
2. Apply the configuration, exit the wizard, and manually open **KeniuCNC** from
   the menu. Completing configuration does not automatically start the main application.
3. Confirm SIM mode, then open a sample program to explore toolpath previews,
   coordinates, alarms, and simulated operations.

If the initialization wizard opens automatically at startup, complete configuration
first, then manually open the main application. Use your normal logged-in desktop
account. **Do not launch the interface with `sudo`.** You can also run
`keniucnc-launcher` in a terminal within the graphical desktop.

User programs are stored in `~/keniucnc/programs`. On first launch, missing sample
programs are added without overwriting existing files of the same name. The package
includes a desktop-session autostart entry, so the application may start when you
log in. Manage this entry in your desktop's autostart settings.

## Web access

Start **KeniuCNC** from the desktop and wait for the application and Web service
to start, then open the appropriate address in a browser:

| Access location | Address |
| --- | --- |
| The computer running Omni CNC | [http://127.0.0.1:8080](http://127.0.0.1:8080) |
| Alternative on the same computer | [http://localhost:8080](http://localhost:8080) |
| Another computer or tablet on the same LAN | `http://<Omni-CNC-computer-IP>:8080` |

For example, if the computer running Omni CNC has IP address `192.168.1.100`, other
devices can use `http://192.168.1.100:8080`. Replace this example with your own IP.
Run the following on the Omni CNC computer and choose an address on the same LAN
as the device accessing it:

```bash
hostname -I
```

`127.0.0.1` and `localhost` always refer to the device running the browser; they
cannot be used to access a different computer. The default Web port is **8080**.
If the platform configuration changes it, use the configured port. Open the control
page directly at `http://<Omni-CNC-computer-IP>:8080/#control`.

LAN access requires the Web service to listen on the appropriate network address
and the network and firewall to allow the port. Use only on a trusted LAN; do not
expose the control interface directly to the Internet. Keep all evaluation in SIM
mode. If access fails, confirm that the main application is running and check the
IP, port, and network connectivity. Report errors or browser screenshots through
[Issues](https://github.com/zhangqiangauto/omni-cnc/issues).

See the [repository](https://github.com/zhangqiangauto/omni-cnc) for downloads and
release notes. Submit installation and usage questions through
[Issues](https://github.com/zhangqiangauto/omni-cnc/issues).

## Upgrades and backups

Before upgrading, stop simulation tasks, exit the application, and back up:

| Path | Contents |
| --- | --- |
| `/etc/keniucnc` | System and machine configuration |
| `/var/lib/keniucnc` | Persistent state and tool/job data |
| `/var/log/keniucnc` | Logs, system events, and operation records |
| `~/keniucnc/programs` | Programs for the current user; back up separately for each user |

Download a matching new package, set its path as described above, and run
`sudo apt install "$DEB_FILE"`. After upgrading, manually restart the application,
check the configuration, and continue using SIM mode.

## Uninstallation

Exit the application and back up any data you need before choosing one of the following.

**Standard removal:**

```bash
sudo apt remove keniucnc
```

Removes the current package and launch entries while retaining release copies,
user data, and caches.

**Purge the software and release copies:**

```bash
sudo apt purge keniucnc
```

In addition to standard removal, deletes `/opt/keniucnc/current`,
`/opt/keniucnc/releases`, and `/var/cache/keniucnc`.

Both methods retain `/etc/keniucnc`, `/var/lib/keniucnc`, `/var/log/keniucnc`, and
each user's `~/keniucnc/programs`. **`purge` does not delete all user data.**
Uninstalling Omni CNC does not uninstall LinuxCNC, the real-time kernel, or
separately installed IgH components.

## Reporting problems

Report installation failures, startup failures, interface issues, and other problems at:

**[Omni CNC Issues](https://github.com/zhangqiangauto/omni-cnc/issues)**

Please include, where possible:

- Software version and full package filename.
- OS image source, OS version, CPU architecture, memory, and kernel version.
- Steps to reproduce, complete error messages, and relevant logs.
- For cycle-jitter issues, the measurement tool, load conditions, and results.

Check the installed version, kernel, and key logs with:

```bash
dpkg-query -W keniucnc
uname -r
journalctl -t keniucnc -b --no-pager
journalctl -t keniucnc-launcher -b --no-pager
```

Application logs are in `/var/log/keniucnc`. Remove keys, personal information, and
sensitive device information before publishing logs.

## Licensing

This repository is publicly available for downloads and discussion. Public access
does not imply an open-source license or unrestricted redistribution rights.
Use, copying, and distribution of the software and third-party components are
subject to the license agreements and copyright notices for the corresponding release.
