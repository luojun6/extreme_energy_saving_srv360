# Extreme Energy Saving Strategy for FICM - SRV360

## 0 Legend

| Version | Description     | Date       | Author |
| ------- | --------------- | ---------- | ------ |
| 1.0     | First released. | 2024-02-28 | 罗均   |

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

## 2 Scope

### 2.1 Functional Scope

This specification is **ONLY** applicable for the "extreme" energy saving strategy of _FICM_ SRV360 function.

### 2.2 Vehicle Program Scope

This specification is applicable for **ALL** SAIC vehicle programs installed with latest FICM platforms included:

- FICM MT2712 integrated with HD digital SRV360 cameras
- FICM MT8666 integrated with HD digital SRV360 cameras

Note: ICM SA8155P might has differrent solutions, which is still in developing.

### 2.3 Hardware Block Diagram

![Hardware Block Diagram](./images/hardware_block_diagram.png)

- Power of SRV360 cameras are controlled by FICM-MCU's enbale wire
- Once the system chooses to close the power of SRV360 cameras, the power of de-serializer keeps the same.

### 2.4 Regulation

- R158

Assessment: no impact.

## 3 Functional Requirements

### 3.1 REQ_EES_SRV360_000

#### 3.1.1 Description

Power of SRV360 cameras should be **power-off** while **ALL** of the **Pre-conditions** satisfied as below for energy saving purpose.

#### 3.1.2 Pre-conditions

- [CAN Signal: SysPwrMd](#syspwrmd) == RUN
<!-- - [CAN Signal: VehSpdAvgDrvn](#VehSpdAvgDrvn) > [Internal Signal: SRV360AppAvailableSpeed](#SRV360AppAvailableSpeed) -->
- [CAN Signal: VehSpdAvgDrvn](#VehSpdAvgDrvn) > [Internal Signal: EES_SRV360CameraPowerOffSpeed](#EES_SRV360CameraPowerOffSpeed)
- [Internal Signal: SDCardPluginStatus](#SDCardPluginStatus) == SD_CARD_NOT_PLUG_IN (if applicable)

#### 3.1.3 Use Cases

- [UC_EES_SRV360_000: System sets SRV360 Cameras Power OFF](#uc_ees_srv360_000-system-sets-srv360-cameras-power-off)

#### 3.1.4 Post-conditions

- [Internal Signal: SRV360CameraPowerStatus](#SRV360CameraPowerStatus) == OFF

#### 3.1.5 Dependecies

N/A

### 3.2 REQ_EES_SRV360_001

#### 3.2.1 Description

Power of SRV360 cameras should be **power-on** while **ANY** of the **Pre-conditions** satisfied as below for SRV360 functionality available.

#### 3.2.2 Pre-conditions

<!-- - [CAN Signal: SysPwrMd](#syspwrmd) == RUN -->

- [CAN Signal: VehSpdAvgDrvn](#VehSpdAvgDrvn) < ([Internal Signal: EES_SRV360CameraPowerOffSpeed](#EES_SRV360CameraPowerOffSpeed) + [Internal Signal: EES_SRV360CamperPowerOnSpeedOffset](#EES_SRV360CamperPowerOnSpeedOffset))
- [Internal Signal: SDCardPluginStatus](#SDCardPluginStatus) == SD_CARD_PLUG_IN (if applicable)

#### 3.2.3 Use Cases

- [UC_EES_SRV360_001: System sets SRV360 Cameras Power ON](#uc_ees_srv360_001-system-sets-srv360-cameras-power-on)

#### 3.2.4 Post-conditions

- [Internal Signal: SRV360CameraPowerStatus](#SRV360CameraPowerStatus) == ON

#### 3.2.5 Dependecies

N/A

## 4 Entities

### SysPwrMd

### VehSpdAvgDrvn

### SDCardPluginStatus

### SRV360CameraPowerStatus

### SRV360AppAvailableSpeed

### EES_SRV360CamperPowerOnSpeedOffset

### EES_SRV360CameraPowerOffSpeed

## 5 Use Cases

### UC_EES_SRV360_000: System sets SRV360 Cameras Power OFF

### UC_EES_SRV360_001: System sets SRV360 Cameras Power ON
