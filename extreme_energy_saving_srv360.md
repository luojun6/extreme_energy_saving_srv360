# Extreme Energy Saving Strategy for FICM - SRV360

## 0 Legend

| Version | Description                                       | Date       | Author |
| ------- | ------------------------------------------------- | ---------- | ------ |
| 1.0     | First released.                                   | 2024-04-12 | 罗均   |
| 1.1     | Revised [Interoperability](#79-interoperability). | 2024-04-15 | 罗均   |

## 1 Overview

### 1.1 Background

From the eletrical-battery-power-train vehicle perspective, any consumption of low-voltage-system eletrical components might produce the non-essentional waste of battery power, which could has impact to vehicle users' real capable mileage.

Therefore, this solution is trying to save low-voltage-system eletrical components consumption while users are not using. In addition, the applied energy saving solution would not cause any obvious unhappy exerperience from user standpoint.

### 1.2 Requirements Brief

Regarding to the external SRV360(SurRounding View 360 degrees) four cameras which have approximately 4W power consumption (about 1W per camera).

If an eletrical vehicle's power efficiency is 9km/1kwh, saving 4W power consumption could contribute approximately extra 4 multiplies 9 equals 36m of moving range per hour.

Therefore it is valuable to design and apply an "extreme" energy saving strategy for this extra 54m moving range from user usage perspective.

### 1.3 Acronyms

- FICM: Front Infortainment Control Module
- ICM: Infortainment Control Module
- SRV360: SurRounding View 360 degrees
- PMIC: Power Management Integrated Circuit
- SoC: System on Chip
- MCU: Micro Controller Unit
- DRV: Driving Recording Video

## 2 Scope

### 2.1 Functional Scope

This specification is **ONLY** applicable for the "extreme" energy saving strategy of _FICM_ SRV360 function.

### 2.2 Vehicle Program Scope

This specification is applicable for **ALL** SAIC vehicle programs installed with latest FICM platforms included:

- FICM JFK8015 integrated with HD digital SRV360 cameras
- FICM MT2712 integrated with HD digital SRV360 cameras
- FICM MT8666 integrated with HD digital SRV360 cameras
- ICM SA8155P integrated with HD digital SRV360 cameras

Note: ICM SA8155P might has differrent solutions, which is still in developing.

### 2.3 Hardware Block Diagram

![Hardware Block Diagram](./images/hardware_block_diagram.png)

- Power of SRV360 cameras are controlled by FICM-MCU's **enbale** wire.
- Once the system chooses to close the power of SRV360 cameras, the power of de-serializer keeps the same.

### 2.4 Regulation

- R158

Assessment: no impact.

## 3 Functional Requirements

### 3.1 REQ_EES_SRV360_000

#### 3.1.1 Description

Power of SRV360 cameras should be **power-off** while **ALL** of the **Pre-conditions** satisfied as below for energy saving purpose.

#### 3.1.2 Pre-conditions

<!-- - [CAN Signal: SysPwrMd](#syspwrmd) == RUN -->
<!-- - [CAN Signal: VehSpdAvgDrvn](#VehSpdAvgDrvn) > [Internal Signal: SRV360AppAvailableSpeedSetting](#SRV360AppAvailableSpeedSetting) -->

- [SystemMode: HeadunitPowerMode](#headunitpowermode) == _`running`_
- [CAN Signal: VehSpdAvgDrvn](#VehSpdAvgDrvn) > [Internal Signal: EES_SRV360CameraPowerOffSpeed](#EES_SRV360CameraPowerOffSpeed)
- [Internal Signal: DvrSDCardPluginStatus](#DvrSDCardPluginStatus) == SD_CARD_NOT_PLUG_IN (if applicable)

#### 3.1.3 Use Cases

- [UC_EES_SRV360_000: Headunit sets SRV360 Cameras Power OFF](#uc_ees_srv360_000-system-sets-srv360-cameras-power-off)

#### 3.1.4 Post-conditions

- [Internal Signal: SRV360CameraPowerStatus](#SRV360CameraPowerStatus) == OFF

#### 3.1.5 Dependecies

N/A

### 3.2 REQ_EES_SRV360_001

#### 3.2.1 Description

Power of SRV360 cameras should be **power-on** while **ANY** of the **Pre-conditions** satisfied as below for SRV360 functionality available.

#### 3.2.2 Pre-conditions

- [SystemMode: HeadunitPowerMode](#headunitpowermode) == _`running`_
- [CAN Signal: VehSpdAvgDrvn](#VehSpdAvgDrvn) > ([Internal Signal: EES_SRV360CameraPowerOffSpeed](#EES_SRV360CameraPowerOffSpeed) + [Internal Signal: EES_SRV360CameraPowerOnSpeedOffset](#EES_SRV360CameraPowerOnSpeedOffset))
- [Internal Signal: DvrSDCardPluginStatus](#DvrSDCardPluginStatus) == SD_CARD_NOT_PLUG_IN (if applicable)

#### 3.2.3 Use Cases

- [UC_EES_SRV360_001: Headunit sets SRV360 Cameras Power ON](#uc_ees_srv360_001-system-sets-srv360-cameras-power-on)

#### 3.2.4 Post-conditions

- [Internal Signal: SRV360CameraPowerStatus](#SRV360CameraPowerStatus) == ON

#### 3.2.5 Dependecies

N/A

### 3.3 REQ_EES_SRV360_003

#### 3.3.1 Description

For the purpose of intenral EMC performance and camera protection, power of SRV360 cameras should **NOT** be **power-on** and **power-off** with high frequency.

#### 3.3.2 Pre-conditions

As same as [REQ_EES_SRV360_000](#31-req_ees_srv360_000)

- Plus [Internal Signal: EES_SRV360CameraPowerOffTimerStatus](#EES_SRV360CameraPowerOffTimerStatus)

#### 3.3.3 Use Cases

- [Internal Signal: EES_SRV360CameraPowerOffTimerStatus](#EES_SRV360CameraPowerOffTimerStatus) == standby -> [UC_EES_SRV360_001: Headunit sets SRV360 Cameras Power ON](#uc_ees_srv360_001-system-sets-srv360-cameras-power-on)

#### 3.3.4 Post-conditions

- [Internal Signal: EES_SRV360CameraPowerOffTimerStatus](#EES_SRV360CameraPowerOffTimerStatus) == standby -> [Internal Signal: SRV360CameraPowerStatus](#SRV360CameraPowerStatus) == OFF

#### 3.3.5 Dependecies

N/A

### 3.4 REQ_EES_SRV360_004

#### 3.4.1 Description

For the "camera-plex" solution of DRV(Driving Recording Video) feature, system should forbit cameras powering off, which means to disable [REQ_EES_SRV360_000](#31-req_ees_srv360_000).

#### 3.4.2 Pre-conditions

[Internal Signal: DvrSDCardPluginStatus](#DvrSDCardPluginStatus)

#### 3.4.3 Use Cases

[UC_EES_SRV360_001: Headunit sets SRV360 Cameras Power ON](#uc_ees_srv360_001-system-sets-srv360-cameras-power-on)

#### 3.4.4 Post-conditions

[Internal Signal: SRV360CameraPowerStatus](#SRV360CameraPowerStatus) == ON

#### 3.4.5 Dependecies

N/A

## 4 Entities

### HeadunitPowerMode

```json
{
  "EntityID": "REQ_EES_ENT_SRV000",
  "EntityName": "HeadunitPowerMode",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "shutdown": 0, "STR": 1, "running": 2 },
  "InitialValue": "shutdown",
  "ExceptionValue": "shutdown",
  "Comments": "STR means SuspendToRAM."
}
```

### VehSpdAvgDrvn

```json
{
  "EntityID": "REQ_EES_ENT_SRV100",
  "EntityName": "VehSpdAvgDrvn",
  "EntityType": "",
  "DataType": "float",
  "ValueRange": "(0 - 32767 * 0.015625) km/h",
  "InitialValue": 0,
  "ExceptionValue": 0,
  "Comments": "Vehicle average speed received from CAN bus."
}
```

### DvrSDCardPluginStatus

```json
{
  "EntityID": "REQ_EES_ENT_SRV200",
  "EntityName": "DvrSDCardPluginStatus",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "not-plugin": 0, "plugin": 1 },
  "InitialValue": "not-plugin",
  "ExceptionValue": "plugin",
  "Comments": "SD Card just means the memory medium for DVR video storage, it can be TF card or USB stick."
}
```

### SRV360CameraPowerStatus

```json
{
  "EntityID": "REQ_EES_ENT_SRV201",
  "EntityName": "SRV360CameraPowerStatus",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "OFF": 0, "ON": 1 },
  "InitialValue": "OFF",
  "ExceptionValue": "OFF",
  "Comments": ""
}
```

### SRV360AppAvailableSpeedSetting

```json
{
  "EntityID": "REQ_EES_ENT_SRV202",
  "EntityName": "SRV360AppAvailableSpeedSetting",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "15km/h": 0, "25km/h": 1, "35km/h": 2 },
  "InitialValue": "15km/h",
  "ExceptionValue": "15km/h",
  "Comments": ""
}
```

### EES_SRV360CameraPowerOnSpeedOffset

```json
{
  "EntityID": "REQ_EES_ENT_SRV203",
  "EntityName": "EES_SRV360CameraPowerOnSpeedOffset",
  "EntityType": "",
  "DataType": "int",
  "ValueRange": "(0 - 10) km/h",
  "InitialValue": 4,
  "ExceptionValue": 10,
  "Comments": "Internal value hard-coded in application."
}
```

### EES_SRV360CameraPowerOffSpeed

```json
{
  "EntityID": "REQ_EES_ENT_SRV204",
  "EntityName": "EES_SRV360CameraPowerOffSpeed",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "50km/h": "15km/h", "60km/h": "25km/h", "70km/h": "35km/h" },
  "InitialValue": "50km/h",
  "ExceptionValue": "50km/h",
  "Comments": "Configurable value in application."
}
```

### EES_SRV360CameraPowerOffTimerStatus

```json
{
  "EntityID": "REQ_EES_ENT_SRV205",
  "EntityName": "EES_SRV360CameraPowerOffTimerStatus",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "standby": 0, "activated": 1 },
  "InitialValue": "standby",
  "ExceptionValue": "activated",
  "Comments": ""
}
```

### EES_SRV360CameraPowerOffTimerSetting

```json
{
  "EntityID": "REQ_EES_ENT_SRV206",
  "EntityName": "EES_SRV360CameraPowerOffTimerSetting",
  "EntityType": "",
  "DataType": "int",
  "ValueRange": "-1(0xFFFF) ~ 32767(0x7FFF)",
  "InitialValue": "0",
  "ExceptionValue": "-1(0xFFFF)",
  "Comments": "unit - seconds"
}
```

### EES_SRV360EanbledStatus

```json
{
  "EntityID": "REQ_EES_ENT_SRV207",
  "EntityName": "EES_SRV360EanbledStatus",
  "EntityType": "",
  "DataType": "Enumeration",
  "ValueRange": { "disabled": 0, "enabled": 1 },
  "InitialValue": "enabled",
  "ExceptionValue": "disabled",
  "Comments": ""
}
```

## 5 Use Cases

### 5.1 Use Case Models

#### UC_EES_SRV360_000: Headunit sets SRV360 Cameras Power OFF

![UC_EES_SRV360_000](./images/UC_EES_SRV360_000.png)

#### UC_EES_SRV360_001: Headunit sets SRV360 Cameras Power ON

![UC_EES_SRV360_001](./images/UC_EES_SRV360_001.png)

### 5.2 Business Rules

#### Arbitriation Table - 1

- **Inputs:**
  - [HeadunitPowerMode](#headunitpowermode)
  - [VehSpdAvgDrvn](#vehspdavgdrvn)
  - [DvrSDCardPluginStatus](#dvrsdcardpluginstatus)
- **Outputs:**
  - [SRV360CameraPowerStatus](#srv360camerapowerstatus)
  - [SRV360CameraPowerOffTimerStatus](#ees_srv360camerapowerofftimerstatus)

| No. | [Headunit<br>PowerMode](#headunitpowermode) |                                                                                            [VehSpdAvgDrvn](#vehspdavgdrvn)                                                                                             | [DvrSDCard<br>PluginStatus](#dvrsdcardpluginstatus) | [SRV360<br>Camera<br>Power<br>Status](#srv360camerapowerstatus) | [SRV360<br>Camera<br>Power<br>OffTimer<br>Status](#ees_srv360camerapowerofftimerstatus) |
| :-- | :-----------------------------------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------: | :-------------------------------------------------------------: | :-------------------------------------------------------------------------------------: |
| A   |                  shutdown                   |                                                                                                           X                                                                                                            |                          X                          |                             **OFF**                             |                                         standby                                         |
| B   |                     STR                     |                                                                                                           X                                                                                                            |                          X                          |                             **OFF**                             |                                         standby                                         |
| C   |                   running                   | <= [EES_SRV360CameraPowerOffSpeed](#ees_srv360camerapoweroffspeed) && <br>> ([SRV360AppAvailableSpeedSetting](#srv360appavailablespeedsetting) + [EES_SRV360CameraPowerOffSpeed](#ees_srv360camerapoweronspeedoffset)) |                          X                          |                             **ON**                              |                                         standby                                         |
| D   |                   running                   |                                                                           > [EES_SRV360CameraPowerOffSpeed](#ees_srv360camerapoweroffspeed)                                                                            |                     not-plugin                      |                             **OFF**                             |                             standby <br> -> <br> activated                              |
| E   |                   running                   |                                                                           > [EES_SRV360CameraPowerOffSpeed](#ees_srv360camerapoweroffspeed)                                                                            |                       plugin                        |                             **ON**                              |                                         standby                                         |
| F   |                   running                   |                                     <= ([SRV360AppAvailableSpeedSetting](#srv360appavailablespeedsetting) + [EES_SRV360CameraPowerOffSpeed](#ees_srv360camerapoweronspeedoffset))                                      |                          X                          |                             **ON**                              |                                            X                                            |
| G   |                   running                   |                                                                           > [EES_SRV360CameraPowerOffSpeed](#ees_srv360camerapoweroffspeed)                                                                            |                          X                          |                                X                                |                                        activated                                        |

**Requirements Correlation:**

| No. | Requirements                                 | Realization Use Cases                                                                                                                                                                                                                                                     |
| --- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A   | General system power management              | N/A                                                                                                                                                                                                                                                                       |
| B   | General system power management              | N/A                                                                                                                                                                                                                                                                       |
| C   | [REQ_EES_SRV360_000](#31-req_ees_srv360_000) | [UC_EES_SRV360_000_RE000_READY_TO_POWER_OFF_CAMERAS](#uc_ees_srv360_000_re000_ready_to_power_off_cameras)                                                                                                                                                                 |
| D   | [REQ_EES_SRV360_000](#31-req_ees_srv360_000) | [UC_EES_SRV360_000_RE001_POWER_OFF_CAMERAS_GRACEFULLY](#uc_ees_srv360_000_re001_power_off_cameras_gracefully)                                                                                                                                                             |
| E   | [REQ_EES_SRV360_004](#34-req_ees_srv360_004) | [UC_EES_SRV360_001_RE001_POWER_ON_WHILE_SD_CARD_PLUGIN](#uc_ees_srv360_001_re001_power_on_while_sd_card_plugin)<br> [UC_EES_SRV360_000_RE003_POWER_OFF_CAMERAS_FORBIDDEN_WHILE_SD_CARD_PLUGIN](#uc_ees_srv360_000_re003_power_off_cameras_forbidden_while_sd_card_plugin) |
| F   | [REQ_EES_SRV360_001](#32-req_ees_srv360_001) | [UC_EES_SRV360_001_RE000_POWER_ON_CAMERAS_IN_LOW_SPEED](#uc_ees_srv360_001_re000_power_on_cameras_in_low_speed)                                                                                                                                                           |
| G   | [REQ_EES_SRV360_003](#33-req_ees_srv360_003) | [UC_EES_SRV360_000_RE002_POWER_OFF_CAMERAS_FORBIDDEN_WHILE_TIMER_ACTIVATED](#uc_ees_srv360_000_re002_power_off_cameras_forbidden_while_timer_activated)                                                                                                                   |

#### Arbitriation Table - 2

- **Inputs:**
  - [EES_SRV360EanbledStatus](#ees_srv360eanbledstatus)
- **Outputs:**
  - [UC_EES_SRV360_000](#uc_ees_srv360_000-headunit-sets-srv360-cameras-power-off)
  - [UC_EES_SRV360_001](#uc_ees_srv360_001-headunit-sets-srv360-cameras-power-on)

| No. | [EES_SRV360EanbledStatus](#EES_SRV360EanbledStatus) | [UC_EES_SRV360_000](#uc_ees_srv360_000-headunit-sets-srv360-cameras-power-off) | [UC_EES_SRV360_001](#uc_ees_srv360_001-headunit-sets-srv360-cameras-power-on) |
| --- | --------------------------------------------------- | ------------------------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| A   | disabled                                            | disabled                                                                       | disabled                                                                      |
| B   | enabled                                             | enabled                                                                        | enabled                                                                       |

Tha above rule has no neccessary to implement during run-time.

### 5.3 Prototype Demos

## 6 System Realization

### 6.1 Analysis Models

#### 6.1.1 [UC_EES_SRV360_000](#uc_ees_srv360_000-headunit-sets-srv360-cameras-power-off)

![analysis_model_power_off_cameras](./images/UC_EES_SRV360_000_analysis.png)

#### 6.1.2 [UC_EES_SRV360_001](#uc_ees_srv360_001-headunit-sets-srv360-cameras-power-on)

![analysis_model_power_on_cameras](./images/UC_EES_SRV360_001_analysis.png)

### 6.2 Realization Use Cases

- **[UC_EES_SRV360_000: Headunit sets SRV360 Cameras Power OFF](#uc_ees_srv360_000-headunit-sets-srv360-cameras-power-off):**

  - #### UC_EES_SRV360_000_RE000_READY_TO_POWER_OFF_CAMERAS

  - #### UC_EES_SRV360_000_RE001_POWER_OFF_CAMERAS_GRACEFULLY

  - #### UC_EES_SRV360_000_RE002_POWER_OFF_CAMERAS_FORBIDDEN_WHILE_TIMER_ACTIVATED

  - #### UC_EES_SRV360_000_RE003_POWER_OFF_CAMERAS_FORBIDDEN_WHILE_SD_CARD_PLUGIN

![UC_EES_SRV360_000_sequence](./images/UC_EES_SRV360_000_sequence.png)

- **[UC_EES_SRV360_001: Headunit sets SRV360 Cameras Power ON](#uc_ees_srv360_001-headunit-sets-srv360-cameras-power-on)**

  - #### UC_EES_SRV360_001_RE000_POWER_ON_CAMERAS_IN_LOW_SPEED

  - #### UC_EES_SRV360_001_RE001_POWER_ON_WHILE_SD_CARD_PLUGIN

  - #### UC_EES_SRV360_001_RE002_READT_TO_POWER_ON_CAMERAS_IN_LOW_SPEED

![UC_EES_SRV360_001_sequence](./images/UC_EES_SRV360_001_sequence.png)

### 6.3 Dynamic Analysis

N/A

### 6.4 Component Deployment Strategy

![deploy_model](./images/deploy_model.png)

## 7 Non-Functional Requirements

### 7.1 Physical Requirements

N/A

### 7.2 Electricity & Voltage Requirements

TBD

### 7.3 Thermal Constraint

N/A

### 7.4 Comfortabbleness

The final implementation **MUST NOT** have any impact to SRV360 function from user perspective.

### 7.5 Drivability

N/A

### 7.6 Duration

| HW_ID | SW_ID | Component | Validtion Method                           | Duration Cylces |
| ----- | ----- | --------- | ------------------------------------------ | :-------------: |
|       |       | FICM      | Power-on <-> Power-off with actual cameras |     300,000     |
|       |       | Cameras   | Power-on <-> Power-off with power supply   |     300,000     |

### 7.7 Performance

TBD

### 7.8 Availability

#### REQ_EES_SRV360_AVA_000

The camera malfunctional DTC detection should be masked while [SRV360CameraPowerStatus](#SRV360CameraPowerStatus) is `OFF`.

### 7.9 Interoperability

#### 7.9.1 User Interface Interoperability

N/A

#### 7.9.2 Engineering Interface Interoperability

**Suggested Diagnostic Identification Entities:**

| No. | Entity                                                                        | Typical Configuration | Corresponding Scenarios                                 |
| --- | ----------------------------------------------------------------------------- | --------------------- | ------------------------------------------------------- |
| A   | [EES_SRV360EanbledStatus](#ees_srv360eanbledstatus)                           | disabled              | Power-off <-> Power-on functionality disabled           |
| B   | [EES_SRV360EanbledStatus](#ees_srv360eanbledstatus)                           | enabled               | Power-off <-> Power-on functionality enabled            |
| C   | [EES_SRV360CameraPowerOffTimerSetting](#ees_srv360camerapowerofftimersetting) | 0 (seconds)           | Disabled [REQ_EES_SRV360_003](#31-req_ees_srv360_000)   |
| D   | [EES_SRV360CameraPowerOffTimerSetting](#ees_srv360camerapowerofftimersetting) | 900 (seconds)         | 15 minutes protection period for camera                 |
| E   | [EES_SRV360CameraPowerOffTimerSetting](#ees_srv360camerapowerofftimersetting) | -1 (seconds)          | Only allow one time of Power-off during one power cycle |

### 7.10 Maintenance

N/A

### 7.11 Testability

All functional requirements are testable by use cases.

All non-functional requirements are testable by requirement descriptions.

### 7.12 Usability

N/A

### 7.13 Cybersecurity

N/A

### 7.14 Legal and Standard Requirements

N/A
