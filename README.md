# Reusing Fresco Logic FL2000DX USB-to-HDMI Driver From an Old Windows Installation

This document explains how to **export** the working Fresco Logic FL2000DX driver from an old Windows 10/11 installation and **install** it on a new Windows 10/11 installation on the same PC.

The same idea can be used for other devices as well.

> ⚠️ All commands must be run in an **elevated** Command Prompt or PowerShell  
> (Right-click → **Run as administrator**).

---

## 0. Optional: Download prepared driver pack

If you already have a prepared driver pack (for example zipped as `FrescoFL200DXDrivers`), you can download it directly instead of exporting it yourself:

- **Download:** [FrescoFL200DX-USB-HDMI-Drivers-Win11-2025](https://drive.google.com/file/d/1ckIP9apI6sO5W6ZxuOjmGGhzyZo2s4Te/view?usp=sharing)

After downloading, extract it so that you end up with a folder like:

```text
C:\FrescoFL200DXDrivers
├─ fl2000.inf_amd64
│  ├─ fl2000.inf
│  ├─ fl2000.cat
│  └─ x64\fl2000.sys, flvga_tray.exe, WdfCoInstaller01011.dll
└─ fresco_iddcx.inf_amd64
   ├─ fresco_iddcx.inf
   ├─ fresco_iddcx.cat
   └─ x64\fresco_iddcx.dll
```

Then skip directly to **Section 3 – Move the driver to the new Windows installation** and **Section 4 – Install the exported driver**.

If you want to build this folder from your own working system, follow the full instructions below.

---

## 1. Export drivers from the old Windows installation

Boot into the **old Windows** where the FL2000DX adapter is working correctly.

### 1.1 Create a backup folder

```bat
mkdir C:\FrescoDriverBackup
```

### 1.2 Export all third-party drivers

```bat
dism /online /export-driver /destination:C:\FrescoDriverBackup
```

This copies all third-party drivers from the current Windows installation into  
`C:\FrescoDriverBackup`, including the Fresco Logic driver.

---

## 2. Extract only the Fresco Logic FL2000DX driver

From `C:\FrescoDriverBackup`, locate the folders related to the Fresco Logic FL2000DX.
In one typical case they are named similar to:

- `fl2000.inf_amd64…`
- `fresco_iddcx.inf_amd64…`

Copy these two folders into a new, clean directory (still on the old Windows, or directly on a USB drive).  
In this guide we use:

```text
C:\FrescoFL200DXDrivers
├─ fl2000.inf_amd64
│  ├─ fl2000.inf
│  ├─ fl2000.cat
│  └─ x64\fl2000.sys, flvga_tray.exe, WdfCoInstaller01011.dll
└─ fresco_iddcx.inf_amd64
   ├─ fresco_iddcx.inf
   ├─ fresco_iddcx.cat
   └─ x64\fresco_iddcx.dll
```

You can now copy the whole `C:\FrescoFL200DXDrivers` folder to a **USB stick** or to a shared data partition.

---

## 3. Move the driver to the new Windows installation

1. Boot into the **new Windows** installation where the adapter is not working properly.
2. Copy the `FrescoFL200DXDrivers` folder from the USB / data partition to, for example:

```text
C:\FrescoFL200DXDrivers
```

Make sure the structure is exactly as shown above.

---

## 4. Install the exported driver on the new Windows

Open an elevated Command Prompt / PowerShell (**Run as administrator**) and run:

```bat
pnputil /add-driver "C:\FrescoFL200DXDrivers\fl2000.inf_amd64\fl2000.inf" /install
pnputil /add-driver "C:\FrescoFL200DXDrivers\fresco_iddcx.inf_amd64\fresco_iddcx.inf" /install
```

What these commands do:

- `/add-driver` imports the driver package(s) into the **DriverStore** of the new Windows.
- `/install` immediately associates the driver with any matching hardware (in this case, the FL2000DX USB display adapter).

---

## 5. Re-connect the adapter and verify

1. Unplug the FL2000DX adapter, wait a few seconds, and plug it back in.
2. Open **Device Manager**:
   - Previously it may have appeared under **Portable Devices** as a mass-storage device (e.g. `FL2000DX` with a drive letter).
   - After installing the driver, it should appear under **Display adapters** as a Fresco Logic / FL2000 device.
3. Open **Settings → System → Display** and check if the external monitor is detected and working.

If Windows still picks a generic driver, you can force it:

- Device Manager → right-click the problematic device → **Update driver**  
- **Browse my computer for drivers** → select `C:\FrescoFL200DXDrivers` → make sure **Include subfolders** is checked → **Next**.

Windows should then use the exported FL2000DX driver.

---

## 6. Notes & Tips

- This method is particularly useful when:
  - The vendor no longer provides updated drivers.
  - Newer Windows builds mis-detect the device (e.g. as a USB storage device instead of a display adapter).
- You can adapt this process for other hardware by:
  - Exporting drivers with `dism /online /export-driver`
  - Identifying the correct `.inf` folder for your device
  - Installing it on another Windows installation with `pnputil /add-driver`.
