# Airthings Wave Sensor Reader

Airthings Wave is a smart Radon detector, including sensors for temperature and humidity measurements.
Additionally, you can simply wave in front of the device to get a visual indication of your radon levels.

This is a project to provide users a starting point (```read_wave.py``` and ```read_wave2.py```) to read current sensor
values from the 1st and 2nd generation [Airthings Wave](https://airthings.com/wave/) devices using a Raspberry Pi 3
Model B over Bluetooth Low Energy (BLE).

**Table of contents**

- [Airthings Wave Sensor Reader](#airthings-wave-sensor-reader)
- [Requirements](#requirements)
  - [Setup Raspberry Pi](#setup-raspberry-pi)
  - [Turn on the BLE interface](#turn-on-the-ble-interface)
  - [Installing linux and python packages](#installing-linux-and-python-packages)
  - [Downloading script](#downloading-script)
- [Usage](#usage)
- [Sensor data description](#sensor-data-description)
- [Contribution](#contribution)
- [Release notes](#release-notes)

# Requirements

The following tables shows a compact overview of dependencies for this project.

**List of OS dependencies**

| OS          | model/version          | Comments              |
|-------------|------------------------|-----------------------|
| Raspbian    | Raspberry Pi 3 Model B | Used in this project. |
| Linux       | x86 Debian             | Should work according to [bluepy](https://github.com/IanHarvey/bluepy) |


**List of linux/raspberry dependencies**

| package        | version     | Comments                            |
|----------------|-------------|-------------------------------------|
| python         | 2.7 or 3    | Tested with python 2.7.13 and 3.7.3 |
| python-pip     |             | pip for python2.7                   |
| python3-pip    |             | pip3 for python3                    |
| git            |             | To download this project            |
| libglib2.0-dev |             | For bluepy module                   |

**List of third-party Python dependencies**

| module      | version     |
|-------------|-------------|
| bluepy      | 1.3.0       |


## Setup Raspberry Pi

The first step is to setup the Raspberry Pi with Raspbian. An installation guide for 
Raspbian can be found on the [Raspberry Pi website](https://www.raspberrypi.org/downloads/raspbian/).
In short: download the Raspbian image and write it to a micro SD card.

To continue, you need access to the Raspberry Pi using either a monitor and keyboard, or 
by connecting through WiFi or ethernet from another computer. The latter option does not 
require an external screen or keyboard and is called “headless” setup. To access a headless 
setup, you must first activate SSH on the Pi. This can be done by creating a file named ssh 
in the boot partition of the SD card. Connect to the Pi using SSH from a command line 
interface (terminal):

```
$ ssh pi@raspberrypi.local
```

The default password for the ```pi``` user is ```raspberry```.

## Turn on the BLE interface

In the terminal window on your Raspberry Pi:

```
pi@raspberrypi:~$ bluetoothctl
[bluetooth]# power on
[bluetooth]# show
[bluetooth]# exit
```

After issuing the command ```show```, a list of bluetooth settings will be printed
to the Raspberry Pi terminal window. Look for ```Powered: yes```.

## Installing linux and python packages

Raspbian images usually comes with Python (2 and/or 3) pre-installed.

```
pi@raspberrypi:~$ python2 --version
pi@raspberrypi:~$ python3 --version
```

Install dependencies:

```bash
pi@raspberrypi:~$ sudo apt-get update && sudo apt-get install libglib2.0-dev git
# For Python 2
pi@raspberrypi:~$ sudo apt-get install python-pip 
pi@raspberrypi:~$ sudo pip2 install bluepy==1.3.0
# For python 3
pi@raspberrypi:~$ sudo apt-get install python3-pip
pi@raspberrypi:~$ sudo pip3 install bluepy==1.3.0
```

## Downloading script

Downloading using git:

```
pi@raspberrypi:~$ sudo git clone https://github.com/Airthings/wave-reader.git
```

Downloading using wget:

```
pi@raspberrypi:~$ wget https://raw.githubusercontent.com/Airthings/wave-reader/master/read_wave.py
```

# Usage

The general format for calling the scripts is as follows:

```bash
# For 1st Gen Wave
sudo python read_wave.py SERIAL_NUMBER SAMPLE_PERIOD [> somefile.txt]
# For 2nd Gen Wave
sudo python read_wave2.py SERIAL_NUMBER SAMPLE_PERIOD [> somefile.txt]
```

After a short delay, the script will print the current sensor values to the 
Raspberry Pi terminal window. Optionally, you may pipe the readings to a
text-file using ```> somefile.txt```. Exit the script using ```Ctrl+C```.


| Positional input arguments | Type | Description  |
|----------------------------|------|--------------|
| SERIAL_NUMBER | Integer | 10-digit number found under the magnetic backplate of your Airthings product.
| SAMPLE_PERIOD | Integer | Time in seconds between reading the current sensor values (excluding the overhead of connecting to target).

| Device          | Serial number   | Script to use         |
|-----------------|-----------------|-----------------------|
| Wave 1st Gen    | 2900xxxxxx      | read_wave.py          |
| Wave 2nd Gen    | 2950xxxxxx      | read_wave2.py         |


Example output of a 1st Gen Wave device:
```
Timestamp: 2020-02-10 10:14:32, Humidity: 21.5 %rH, Temperature: 22.8 *C, Radon STA: 36 Bq/m3, Radon LTA: 27 Bq/m3
Timestamp: 2020-02-10 10:20:48, Humidity: 21.5 %rH, Temperature: 22.8 *C, Radon STA: 36 Bq/m3, Radon LTA: 27 Bq/m3
Timestamp: 2020-02-10 10:25:58, Humidity: 21.5 %rH, Temperature: 22.9 *C, Radon STA: 36 Bq/m3, Radon LTA: 27 Bq/m3
Timestamp: 2020-02-10 10:30:10, Humidity: 21.5 %rH, Temperature: 22.9 *C, Radon STA: 36 Bq/m3, Radon LTA: 27 Bq/m3
Timestamp: 2020-02-10 10:35:20, Humidity: 21.5 %rH, Temperature: 22.8 *C, Radon STA: 36 Bq/m3, Radon LTA: 27 Bq/m3
```

> **Note**: The scripts require that your device is advertising. If your device is paired/connected to e.g. a phone, you need to turn off bluetooth on your phone while using the scripts.

> **Note on choosing a sample period:** 
On 1st Gen Wave, temperature and humidity are updated every time we read the wave.
On 2nd Gen Wave, temperature and humidity are updated every 5 minutes.
On both devices, radon measurements are updated once every hour.


# Sensor data description

| sensor                        | units               | Comments |
|-------------------------------|---------------------|----------|
| Datetime                      | YYYY-MM-DD HH:MM:SS | Only available on 1st Gen Wave
| Humidity                      | %rH                 | 
| Temperature                   | &deg;C              | 
| Radon short term average      | Bq/m3               | First measurement available 1 hour after inserting batteries
| Radon long term average       | Bq/m3               | First measurement available 1 hour after inserting batteries

# Contribution

Let us know how it went! If you want contribute, you can do so by posting issues or suggest enhancement
[here](https://github.com/Airthings/wave-reader/issues).


# Release notes

Release dated 10-Feb-2020

* Added read_wave2.py to support 2nd Gen wave devices
* Added py3 compatibility
* Removed tableprint dependency so all py3 versions can be used
* Removed redundant positional argument ```pipe``` (since tableprint is removed)
* Updated bluepy version dependency from 1.2.0 to 1.3.0

Release dated 14-Dec-2018

* Added SAMPLE-PERIOD as an input argument.

Initial release 13-Dec-2018