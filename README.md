# Key Changes:
- Resolves the incompatible pointer type error in the  `clevo_xsm_platform_driver` by casting the `clevo_xsm_wmi_remove` function to `void *` in the `remove` callback. This change is required for kernel version 6.11 and later. ([Fix](https://github.com/xDMPx/clevo-xsm-wmi/commit/5f981262dc40aacdb2fc7df43e0190461d588411))
- Resolve the incompatible pointer type error in `clevo_xsm_wmi_notify` by updating the function to accept an `acpi_object union` instead of a `u32` value, but for now without properly handling of received `acpi_object`.  This change is required for kernel version 6.12 and later. ([Fix](https://github.com/xDMPx/clevo-xsm-wmi/commit/5f513d2386e7cbbf42f0a03924cc19ac2e52f8af))
- Add entry for Clevo model N850EP (N8xxEP6). This entry is a copy of an P870DM entry with adjusted `indent` and `DMI_PRODUCT_NAME`. Currently, only the keyboard backlight controls have been verified to work, along with the hotkeys that manage the backlight.

---

**Disclaimer:**
The remainder of this README contains the original, unaltered content from the upstream repository.

---

# The status and the future of TUXEDO / Clevo WMI
Read this:
https://bitbucket.org/tuxedocomputers/clevo-xsm-wmi/issues/44/the-status-and-the-future-of-tuxedo-clevo

# clevo-xsm-wmi

Kernel module for keyboard backlighting of Clevo SM series notebooks.
(And several EM/ZM/DM series models)

Based upon tuxedo-wmi, created by TUXEDO Computers GmbH.
http://www.linux-onlineshop.de/forum/index.php?page=Thread&threadID=26

### Additions over tuxedo-wmi
* Sysfs interface to control the brightness, mode, colour,
  on/off state after the module has loaded.
  In the original code you can only set these before the module loads.
* Small QT based application to visually control the keyboard lighting using the sysfs interface.
* Cycle through colours rather than modes with the keyboard key.
* Experimental support for touchpad illumination / lower led bar on the front of the machine.

### Supported Devices

| Product Name         | Clevo Name             | TUXEDO Name            |
|----------------------|------------------------|------------------------|
| P15SM                | Clevo P15SM            | ???                    |
| P15SM1-A             | Clevo P15SM1-A         | ???                    |
| P15SM-A              | Clevo P15SM-A          | ???                    |
| P150EM               | Clevo P150EM           | TUXEDO XC1501          |
| P15xEMx              | Clevo P150EM           | TUXEDO XC1503          |
| P17SM-A              | Clevo P17SM-A          | ???                    |
| P17SM                | Clevo P17SM            | ???                    |
| P370SM-A             | Clevo P370SM-A         | ???                    |
| P65_67RSRP           | Clevo P65_67RSRP       | ???                    |
| P65xRP               | Clevo P65xRP           | TUXEDO XC1507          |
| P65xHP               | Clevo P65xHP           | TUXEDO XC1507v2        |
| Deimos/Phobos 1x15S  | Clevo P7xxDM(-G)       | TUXEDO XUX506 / XUX706 |
| P7xxDM(-G)           | Clevo P7xxDM(-G)       | TUXEDO XUX506 / XUX706 |
| P7xxDM2(-G)          | Clevo P7xxDM2(-G)      | TUXEDO XUX507 / XUX707 |
| P750ZM               | Clevo P750ZM           | ???                    |
| P5 Pro SE            | Clevo P750ZM           | ???                    |
| P5 Pro               | Clevo P750ZM           | ???                    |
| P775DM3(-G)          | Clevo P775DM3(-G)      | TUXEDO XUX707          |
| N85_N87              | Clevo N850HJ           | TUXEDO DX1507 / DX1707 |
| P870DM               | Clevo P870DM           | ???                    |
| N85_N87,HJ,HJ1,HK1   | Clevo N870HK           | ???                    |
| N85_87HP6            | Clevo N870HP6          | ???                    |
| P95_HP,HR,HQ         | Clevo P950HP6          | ???                    |
| P65_67HSHP           | Clevo P65_67HSHP       | ???                    |

### Building

Dependencies:

* standard compile stuff (c compiler, make, etc)
* linux-headers

Building:
```bash
# For the module
$ cd module && make && sudo make install

# For the utility
See building instruction under ui/README.md
```

### Usage

Adjusting keyboard settings:
```bash
$ sudo clevo-xsm-wmi
```

Restoring state during boot:
```bash
# With the module:
$ modinfo clevo-xsm-wmi
$ sudo tee /etc/modprobe.d/clevo-xsm-wmi.conf <<< options clevo-xsm-wmi kb_color=white,white,white kb_brightness=1

# With systemd:
$ sudo systemctl enable clevo-xsm-wmi
$ sudo systemctl start clevo-xsm-wmi

# With others:
run '/usr/bin/clevo-xsm-wmi -r' at boot to restore state
run '/usr/bin/clevo-xsm-wmi -s' at shutdown/change to save state
```

### Distributions

Arch Linux: [Module](https://aur.archlinux.org/packages/clevo-xsm-wmi/) [DKMS](https://aur.archlinux.org/packages/clevo-xsm-wmi-dkms/) [Utility](https://aur.archlinux.org/packages/clevo-xsm-wmi-util/)

### Common issues

* 'Can't read private key' during 'make install'.

This is a common issue on Ubuntu as the module isn't signed so manual install is required.
```bash
$ sudo install -m644 clevo-xsm-wmi.ko /lib/modules/$(uname -r)/extra
$ sudo depmod
```
* Module installed but not loaded during boot.

Specify that the module should be loaded just in case.
```bash
$ sudo tee /etc/modules-load.d/clevo-xsm-wmi.conf <<< clevo-xsm-wmi
```

* I'm absolutely positive that the module is installed but still nothing happens!
```bash
$ sudo modprobe clevo-xsm-wmi
$ dmesg | grep clevo
```
If it returns no line such as 'clevo_xsm_wmi: Model Clevo P15SM found', then your model isn't supported.
Please open an issue and supply the output of 'uname -a', 'sudo dmidecode' and 'dmesg | grep clevo'.

### Pull Requests
Please create a pull request into the testing branch. After review and testing we are commiting in the master branch.

### License
This program is free software;  you can redistribute it and/or modify
it under the terms of the  GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or (at
your option) any later version.

This program is  distributed in the hope that it  will be useful, but
WITHOUT  ANY   WARRANTY;  without   even  the  implied   warranty  of
MERCHANTABILITY  or FITNESS FOR  A PARTICULAR  PURPOSE.  See  the GNU
General Public License for more details.

You should  have received  a copy of  the GNU General  Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.
