# OPlus Extras

*A configurable parts application for OnePlus & Realme devices.*

## About the project:

OPlusExtras is a device specifc parts application, aimed at addressing the challenge of maintaining multiple devices with varying feature sets. One of the key advantages of OPlusExtras is its ability to configure node paths, doze sensor names, and enable/disable features via runtime resource overlay (RRO) packages in the common (OPlusExtraResCommon) and device-specific (OPlusExtrasResTarget) trees. This approach ensures that the application can be run on multiple devices, even if they do not support the same features. With OPlusExtras, users can conveniently enable or disable features on a per-device basis without having to remove code for unsupported features!

## Current features:

| Category | Feature | Description | Reference commits|
| --- | --- | --- | --- |
| **Alert slider** | `Use slider for` | Configure what the Alert slider does (Notification, Flashlight, Brightness, Rotation, Ringer, Notification and ringer, Extra Dim, Night Light, Refresh rate & Colorspace). |N/A |
|  | `Top/Mid/Bottom position` | Selects what each position of the alert slider is used for in accordance with what `Use slider for` is set to. |N/A |
| **CPU** | `Touchboost` | Boost CPU frequency on touchscreen input on qcom devices. |N/A |
| **Display** | `High brightness mode` | Enable peak luminance. | N/A |
|  | `Antiflicker` | Prevent extensive flickering and reduce eye strain in low light conditions. |N/A |
|  | `Kernel color calibration` | Calibrate display via Kcal on qcom devices (red, green, blue, saturation, hue, contrast, value). |N/A |
|  | `Keep PCC` | Keeps panel color correction enabled when the UDFPS circle is pressed. |N/A |
|  | `Ambient display gestures` | Raise2wake, Raise2pulse & pull-from-pocket to pulse. |N/A |
| **Filesystem** | `Fsync` | Force disable filesystem syncing for increased performance at the risk of data loss in the event of a system crash. |N/A |
| **GPU** | `Adrenoboost` | Ramp up GPU clockspeed faster (off, light, medium, aggressive) on qcom devices. |N/A |
| **Power** | `Powershare` | Reverse wireless charging. |N/A |
|  | `Quite mode` | Disable the fan of the OEM wireless charger and limit wireless charging speeds. | N/A |
| **Touchscreen** | `Touch gestures` | Screen-Off touch gestures with various actions (utilizes the lineage touch hidl). |N/A |
|  | `Edge limit` | Disable the touchpanels edge limit. |N/A |
|  | `High touch polling rate` | Increase touchscreen polling rate. |N/A |
| **Ui-Bench** | `Jitter` | Calculate rendering jitter. | N/A|
| **USB** | `USB-OTG` | Force enable USB on-the-go mode. |N/A |
|  | `USB 2.0 fast charge` | Force fast charging of up to 900mA while connected to a USB 2.0 port with mtp enabled. |N/A|
| **Vibrator** | `System strength` | Define the strength of the vibration motor. |N/A|

## Including OPlusExtras during compilation:

Have the application included during compilation by adding the following to device.mk or common.mk
```
# OPlusExtras
PRODUCT_PACKAGES += \   
    OPlusExtras \
    tri-state-key-calibrate (if device supports tri-state alert slider)
```

If you install OPlusExtras as a user-app, it will not be injected into Settings via ia. Instead, it needs to be included as privleged app in /system_ext to appear as a preference in the main activity of the Settings application above the "About phone" and "System" preferences at the bottom.
## How-to configure OPlusExtras features:

By default, the application appears empty except for the "UI Bench" category, unless it is configured. This approach not only reduces the need for overlaying but also prevents accidental activation or configuration of features on unsupported devices from the application's resources.

**Notable files**

- `res/values/supported_features_config.xml`: Allows for features to be shown or hidden by keeping or removing the corresponding preference during onCreatePreferences and controls whether some services start or not. [link](https://raw.githubusercontent.com/chuqol/packages_apps_OPlusExtras/udc/res/values/supported_features_config.xml)
- `res/values/node_paths_config.xml`: Set sysfs/procfs node paths for the corresponding features. [link](https://raw.githubusercontent.com/chuqol/packages_apps_OPlusExtras/udc/res/values/node_paths_config.xml)
- `res/values/doze_sensors_config.xml`: Defines the sensors used for ambient display gestures. [link](https://raw.githubusercontent.com/chuqol/packages_apps_OPlusExtras/udc/res/values/doze_sensors_config.xml)
- `res/values/touch_gestures_config.xml`: Set the default actions for off-screen touch gestures. [link](https://raw.githubusercontent.com/chuqol/packages_apps_OPlusExtras/udc/res/values/touch_gestures_config.xml)
- `res/values/alert_slider_config.xml`: Set the default mode/action order of the alert slider. [link](https://raw.githubusercontent.com/chuqol/packages_apps_OPlusExtras/udc/res/values/alert_slider_config.xml)

**Setting up the RRO overlay packages**

The following examples will be shown as target. You can replace "target" with "common" for the common config.

- The RRO package structure:
```
OPlusExtrasResTarget (Target or Common)
├── Android.bp
├── AndroidManifest.xml
└── res
    ├── drawable
    │   └── easteregg.xml (if applicable)
    └── values
        ├── supported_features_config.xml
        ├── node_paths_config.xml
        ├── doze_sensors_config.xml
        ├── alert_slider_config.xml
        ├── touch_gestures_config.xml
        └── strings.xml (if applicable)
```

- Contents of Android.bp:
```
//
// Copyright (C) 2023 The Evolution X Project
// SPDX-License-Identifier: Apache-2.0
//

runtime_resource_overlay {
    name: "OPlusExtrasResTarget",
    device_specific: true,
}
```

- Contents of AndroidManifest.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<!--
     Copyright (C) 2023 The Evolution X Project
     SPDX-License-Identifier: Apache-2.0
-->
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="android.overlay.OPlusExtras.target">

    <overlay
        android:isStatic="true"
        android:priority="350"
        android:targetPackage="org.evolution.oplus.OPlusExtras" />
</manifest>
```

- Including the RRO package:

Add the RRO package to ```device/manufacture_name/codename/overlay-evolution/```


Have the RRO package included during compilation by adding the following to device.mk or common.mk
```
# Overlays
PRODUCT_PACKAGES += \
    OPlusExtrasResTarget
```
