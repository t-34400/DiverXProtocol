# Diver-X Contact Glove Protocol
Unofficial documentation of the communication protocol between Diver-X's Contact Glove Diving Station and applications.

## Table of Contents
- [Diver-X Contact Glove Protocol](#diver-x-contact-glove-protocol)
  - [Table of Contents](#table-of-contents)
  - [Official Documentation](#official-documentation)
  - [Supported Version](#supported-version)
  - [Protocol](#protocol)
    - [Pairing](#pairing)
      - [Obtaining DivingStation Host Address](#obtaining-divingstation-host-address)
      - [Registering App's Host Address with DivingStation](#registering-apps-host-address-with-divingstation)
    - [Data Exchange](#data-exchange)
      - [Obtaining Bend Values of Each Joint in Right Hand](#obtaining-bend-values-of-each-joint-in-right-hand)
      - [Obtaining Bend Values of Each Joint in Left Hand](#obtaining-bend-values-of-each-joint-in-left-hand)
      - [Controller Input Retrieval](#controller-input-retrieval)
      - [Operation of Vibration Module](#operation-of-vibration-module)
      - [Operation of Haptic Module](#operation-of-haptic-module)


## Official Documentation
[Official Document](https://docs.diver-x.jp/unity-sdk/setup.html)

## Supported Version
- Diving Station: Version 1.4.2

## Protocol
This document describes the communication protocol between Diver-X's Contact Glove Diving Station and the applications utilizing it.

### Pairing
#### Obtaining DivingStation Host Address
1. (App) Broadcast the following string to UDP port 25801:
    - `DivingStationPairing_StartPairing`
2. (DivingStation) If the DivingStation receives this, it broadcasts the following string to UDP port 25801:
    - `DivingStationPairing_DivingStationIP, <DivingStation's Host IP Address>`

#### Registering App's Host Address with DivingStation
1. (App) Send the following string to UDP port 25800 of the DivingStation's host:
    - `DivingStationPairing_ApplicationIP, <App Host's IP Address>`

### Data Exchange
Data exchange with the DivingStation is performed using the [OSC protocol](https://ccrma.stanford.edu/groups/osc/index.html). 
The destination for sending to the DivingStation is UDP port 25790, and data reception from DivingStation is on UDP port 22788.

#### Obtaining Bend Values of Each Joint in Right Hand
- OSC Packet
  - Address:
    `/DivingStation/FingerRotRight`
  - Values:
    - Size: 64 bytes
    - Type: Blob
- Remarks:
  - Contents of OSC Packet values consist of 16 float32 values representing bend values of joints ranging from 0 to 1.
  - Correspondence of each joint to its index is as follows (the 13th value is not used):
    <details><summary>Correspondence</summary>

    | Index | Joint              |
    | ----- | ------------------ |
    | 0     | LittleProximal     |
    | 1     | LittleIntermediate |
    | 2     | LittleDistal       |
    | 3     | RingProximal       |
    | 4     | RingIntermediate   |
    | 5     | RingDistal         |
    | 6     | MiddleProximal     |
    | 7     | MiddleIntermediate |
    | 8     | MiddleDistal       |
    | 9     | IndexProximal      |
    | 10    | IndexIntermediate  |
    | 11    | IndexDistal        |
    | 12    | ThumbProximal      |
    | 14    | ThumbIntermediate  |
    | 15    | ThumbDistal        |
    </details>
  - Conversion factors for bending values to rotation values [deg] are as follows:
    <details><summary>Conversion Factors</summary>

    | Joint              | Factor  |
    | ------------------ | ------- |
    | LittleProximal     | 90      |
    | LittleIntermediate | 100     |
    | LittleDistal       | 90      |
    | RingProximal       | 90      |
    | RingIntermediate   | 100     |
    | RingDistal         | 90      |
    | MiddleProximal     | 90      |
    | MiddleIntermediate | 100     |
    | MiddleDistal       | 90      |
    | IndexProximal      | 90      |
    | IndexIntermediate  | 100     |
    | IndexDistal        | 90      |
    | ThumbProximal      | 30/0.66 |
    | ThumbIntermediate  | 55      |
    | ThumbDistal        | 80      |
    </details>

#### Obtaining Bend Values of Each Joint in Left Hand
- OSC Packet
  - Address:
    `/DivingStation/FingerRotLeft`
  - Values:
    - Size: 64 bytes
    - Type: Blob
- Remarks:
  - Bend values of joints retrieved are similar to the [right hand](#obtaining-bend-values-of-each-joint-in-right-hand).

#### Controller Input Retrieval
- OSC Packet
  - Address:
    `/DivingStation/ControllerInput`
  - Values:
    - Size: 39 bytes
    - Type: Blob
- Remarks:
  - Meaning of values at each index is as follows:
    - 0: Controller hand (0: Left hand, 1: Right hand)
    - 1-5: Boolean values for controller inputs (0: Off, 1: On)
      - 1: A Button
      - 2: B Button
      - 3: Home Button
      - 4: Joystick Button
      - 5: Trackpad Touch
    - 12-31: Float values for controller inputs (32 bits)
      - 12-15: Trigger
      - 16-19: Grip pressure
      - 20-23: Grip force
      - 24-27: Joystick X-axis
      - 28-31: Joystick Y-axis
    - Other values are not used in this version.

#### Operation of Vibration Module
- OSC Packet
  - Address:
    `/DivingStation/HapticVibration`
  - Values:
    - Size: 16 bytes
    - Type: 4 float32s
- Remarks:
  - Contents of OSC Packet values are as follows:
    - Controller hand (0.0: Left hand, 1.0: Right hand)
    - Frequency [Hz]
    - Amplitude (0.0~1.0)
    - Duration [s]

#### Operation of Haptic Module
- OSC Packet
  - Address:
    `/DivingStation/CollisionSignal`
  - Values:
    - Size: 6 bytes
    - Type: Blob
- Remarks:
  - Contents of OSC Packet values are as follows:
    - Haptic data (Left hand): 2.5 bytes
      - 24 boolean values indicating On/Off of each finger's haptic
      - Index meanings are as follows:

      <details><summary>Correspondence</summary>

      | Index | Section | Location    |
      | ----- | ------- | ----------- |
      | 0     | Thumb   | Upper-left  |
      | 1     | Thumb   | Lower-left  |
      | 2     | Thumb   | Upper-right |
      | 3     | Thumb   | Lower-right |
      | 4     | Index   | Upper-left  |
      | 5     | Index   | Lower-left  |
      | 6     | Index   | Upper-right |
      | 7     | Index   | Lower-right |
      | 8     | Middle  | Upper-left  |
      | 9     | Middle  | Lower-left  |
      | 10    | Middle  | Upper-right |
      | 11    | Middle  | Lower-right |
      | 12    | Ring    | Upper-left  |
      | 13    | Ring    | Lower-left  |
      | 14    | Ring    | Upper-right |
      | 15    | Ring    | Lower-right |
      </details>
    - Back haptic type (Left hand): 4 bits
      - 0: Off
    - Intensity (Left hand): 4 bits
      - 0-15
    - Haptic data (Right hand): 2 bytes
      - Same meaning as for the Left hand
    - Back haptic type (Right hand): 4 bits
    - Intensity (Right hand): 4 bits
