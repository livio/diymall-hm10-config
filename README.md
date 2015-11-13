# Configuring DIYMall HM-10 BLE 4.0 iBeacon

This README will guide you through the process of configuring the Diyall HM-10 iBeacon. This document was created because the beacon's [official documentation](https://www.adrive.com/public/WHAGyN/FZ1095-iBeacon_en.zip ) is difficult to understand and this document will serve as a summation of the official doc.

## Notes

The base station cannot be set UART, which means you cannot use the basestation's serial communication (RX,TX) to communicate with HM-10. You must use an application that can communicate with BLE devices.

## Applications

One of the following applications will be required to send commands to the device

* OS X [LightBlue] (https://itunes.apple.com/us/app/lightblue/id639944780?mt=12)
* iOS TODO
* Android TODO
* Windows TODO
* Linux Flavors TODO

### Using OS X LightBlue to Send Commands to the Beacon

**Note**: This guide assumes the device is configured with its default values

1. Install [LightBlue] (https://itunes.apple.com/us/app/lightblue/id639944780?mt=12) on an OS X machine that supports BLE 4.0
2. Open LighBlue. Under *peripherals* there will be a device name `HMSensor`
3. Select `HMSensor`, his will reveal the services that are available on the device
4. Under *services* select `FFE0`, this will reveal the characteristics are available for this service
5. Under *characteristics* select `FFE1`, this will reveal details about the characteristic and allow you to read, subscribe, write hex and write ASCII to the device
6. Under *details* click the `Subscribe` button
7. In the *Write ASCII* text box input the test command: `AT` and hit `enter`, this will send the command to the device
8. Under *details*, the ASCII field should have been updated with a `OK` response. If you do not see a response make sure that you are subscribed

## Commands

The Diymall Beacon supports a variety of commands. Each of these commands should be sent as ASCII to the device. 

### Test Command

| Send | Receive               |
|------|-----------------------|
| AT   | OK 				   		 |

### Get MAC Address

Gets the BLE Modules MAC address

| Send     | Receive               | 
|----------|-----------------------|
| AT+ADDR? | OK+ADDR:`MAC Address` |

### Get/Set Advertising Interval

Gets or sets the advertising interval of the beacon. The intervals are mapped to a specific hex value:

| Value | Interval  |
|-------|-----------|
| 0     | 100 ms    |
| 1     | 152.5 ms  |
| 2     | 211.25 ms |
| 3     | 318.75 ms |
| 4     | 417.5 ms  |
| 5     | 546.26 ms |
| 6     | 760 ms    |
| 7     | 852.5 ms  |
| 8     | 1022.5 ms |
| 9     | 1285 ms   |
| A     | 2000 ms   |
| B     | 3000 ms   |
| C     | 4000 ms   |
| D     | 5000 ms   |
| E     | 6000 ms   |
| F     | 7000 ms   |

**Default** 9 (1285 ms)

| Send           | Receive         | 
|----------------|-----------------|
| At+ADVI?       | OK+Get:`value` |
| AT+ADVI`value` | OK+Set:`value` |

#### Special Notes

* Available in firmware v517 or greater
* F value was added in firmware v522

### Get/Set battery monitor switch

TODO what does this do

| Value | Description  |
|-------|--------------|
| 0     | Off   	   |
| 1     | On 		   |

**Default** 0 (Off)

| Send           | Receive        |
|----------------|----------------|
| AT+BATC?       | OK+Get:`value` |
| AT+BATC`value` | OK+Set:`value` |

#### Special Notes

* Available in firmware v520 or greater

### Get Battery Information

Gets the battery percentage.

value: 000 to 100

| Send     | Receive        |
|----------|----------------|
| AT+BATT? | OK+Get:`value` |

### Set iBeacon into service mode

Places the iBeacon into service until the device's power is reset. iBeacon mode needs to be enabled first.

| Send           | Receive              |
|----------------|----------------------|
| AT+BUSHU       | OK+BUSHU 			|

#### Special Notes

* Added in firmware v520 and Removed in v521

### Get/Set Characteristic

value: 0x0001 to 0xFFFE

**Default** 0x0FFE1

| Send           | Receive         |
|----------------|-----------------|
| AT+CHAR?       | OK+Get:`value`  |
| AT+CHAR`value` | OK+Set:`value`  |

### Get/Set iBeacon deployment mode

| Value | Description                      |
|-------|----------------------------------|
| 1     | Allows broadcasting and scanning |
| 2     | Only allow broadcasting          |

| Send           | Receive        |
|----------------|----------------|
| AT+DELO`value` | OK+DELO`value` |

#### Special Notes

* After receiving OK+DEL0`value` the device will reset after 500 ms
* This command will put the device into a non-connectable state until next restart

### System Help Information

TODO wat

| Send           | Receive         |
|----------------|-----------------|
| AT+HELP?       | Help Info       |

### Get/Set iBeacon switch

 Command allows the iBeacon mode to be turned on or off.
 
| Value | Description           |
|-------|-----------------------|
| 0     | Turn off iBeacon mode |
| 1     | Turn on iBeacon mode  |

**Default** 0 (off)

| Send           | Receive        |
|----------------|----------------|
| AT+IBEA?       | OK+Get:`value` |
| AT+IBEA`value` | OK+Set:`value` |

#### Special Notes

* Available in firmware v517 or greater

### Get/Set iBeacon UUID 

Allows the iBeacon's UUID to be changed. The iBeacon's UUID requires four commands, each command sets a four byte portion of the UUID. The location defines which portion of the bytes are changed.

| Location | UUID Portion | 
|----------|--------------|
| 0 | `74278BDA`-B644-4520-8F0C-720EAF059935 |
| 1 | 74278BDA-`B644-4520`-8F0C-720EAF059935 |
| 2 | 74278BDA-B644-4520-`8F0C-720E`AF059935 |
| 3 | 74278BDA-B644-4520-8F0C-720E`AF059935` |

Value: 00000001 to FFFFFFFE

| Send                    | Receive        |
|-------------------------|----------------|
| AT+IBE[location]        | OK+Get:`value` |
| AT+IBE[location]`value` | OK+Set:`value` |

#### Example commands to set UUID to 36996276-CB00-40A2-A81E-D7F95858CA7C

```
AT+IBE036996276 // Sets bytes 0-3 to 36996276
AT+IBE1CB0040A2 // Sets bytes 4-7 to CB0040A2
AT+IBE2A81ED7F9 // Sets bytes 8-11 to A81ED7F9
AT+IBE35858CA7C // Sets bytes 12-15 to 5858CA7C
```

#### Special Notes

* Available in firmware v520 or greater

### Get/Set iBeacon Major value

Gets or sets the iBeacon's major value

values 0x0001 to 0xFFFE

**Default** 0xFFE0

| Send           | Receive         |
|----------------|-----------------|
| AT+MARJ?       | OK+Get:`value`  |
| AT+MARJ`value` | OK+Set:`value`  |

#### Special Notes

* Available in firmware v517 and greater

### Get/Set iBeacon Minor value

Gets or sets the iBeacon's minor value

values 0x0001 to 0xFFFE

**Default** 0xFFE1

| Send           | Receive         |
|----------------|-----------------|
| AT+MINO?       | OK+Get:`value`  |
| AT+MINO`value` | OK+Set:`value`  |

#### Special Notes

* Available in firmware v517 and greater

### Get/Set iBeacon Measured power

Measured power is a calibrated value of the iBeacons average RSSI value at one meter.

**Default** 0xFFE1

| Send           | Receive         |
|----------------|-----------------|
| AT+MEAS?       | OK+Get:`value`  |
| AT+MEAS`value` | OK+Set:`value`  |

#### Special Notes

* Available in firmware v519 and greater

### Get/Set device name

Sets the name of the device, you will see this when scanning for the beacon.

value string with max length of 12

**Default** HMSoft

| Send           | Receive         |
|----------------|-----------------|
| AT+NAME?       | OK+NAME:`value` |
| AT+NAME`value` | OK+Set:`value`  |

#### Example of setting name to hello_world

AT+NAMEhello_world


### Get/Set Pin Code

value 000000 to 999999

| Send          | Receive        |
|---------------|----------------|
| AT+PASS?      | OK+Get:`value` |
| AT+PIN`value` | OK+Set:`value` |

**Default** 000000

### Get/Set device antenna power

Gets or sets the antenna's power. Each power setting is mapped to a specific hex value:

| Value | Power  	|
|-------|-----------|
| 0     | -23dbm    |
| 1     | -6dbm  	|
| 2     | 0dbm 		|
| 3     | 6dbm 		|

**Default** 2 (0dbm)

| Send           | Receive         |
|----------------|-----------------|
| AT+POWE?       | OK+Get:`value`  |
| AT+POWE`value` | OK+Set:`value`  |

### Get/Set device's sleep type

| Value | Description      |
|-------|------------------|
| 0     | Auto sleep       |
| 1     | Don't auto sleep |

**Default** 1 (Don't auto sleep)

| Send           | Receive         |
|----------------|-----------------|
| AT+PWRM?       | OK+Get:`value`  |
| AT+PWRM`value` | OK+Set:`value`  |

#### Special Note

* Sleep is only supported when the beacon is in the peripheral role

### Restore to factory defaults

| Send           | Receive         |
|----------------|-----------------|
| AT+RENEW?       | OK+RENEW  |

### Restart device

| Send      | Receive  |
|-----------|----------|
| AT+RESET? | OK+RESET |


### Get RSSI Value

| Send     | Receive         |
|----------|-----------------|
| AT+RSSI? | OK+RSSI:`value` |

#### Special Notes

* Requires AT+MODE value > 0

### Get last connect device address

Gets the MAC address of the last co

| Send           | Receive         |
|----------------|-----------------|
| AT+RADD?       | OK+RADD:`MAC Address` |

### Get/Set Device's Authentication Mode

| Value | Description               |
|-------|---------------------------|
| 0     | No PIN Code               |
| 1     | Bonding does not need PIN |
| 2     | Bonding requires PIN      |


**Default** 0 (No PIN code required)

| Send           | Receive         |
|----------------|-----------------|
| AT+TYPE?       | OK+Get:`value`  |
| AT+TYPE`value` | OK+Set:`value`  |

#### Special Notes

* If firmware is less than v515 do not used this command

### Get/Set Service UUID

value 0x0001 to 0xFFFE

**Default** 0xFFE0

| Send           | Receive         |
|----------------|-----------------|
| AT+UUID?       | OK+Get:`value`  |
| AT+UUID`value` | OK+Set:`value`  |

### Get firmware version

| Send           | Receive      |
|----------------|--------------|
| AT+VERR?       | Version info |
| AT+VERS`value` | Version info |
