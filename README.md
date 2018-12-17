# Airthings Wave Sensor Reader

This is a project to provide users an interface (```read_wave.py```) to read current sensor values from the
[Airthings Wave](https://airthings.com/wave/) devices using a Raspberry Pi 3 Model B over Bluetooth Low Energy (BLE).

Airthings Wave is a smart Radon detector, including sensors for temperature and humidity.
Additionally, you can simply wave in front of the device to get a visual indication of your radon levels.

**Table of contents**

* [Requirements](#requirements)
  * [Setup Raspberry Pi](#setup-Raspberry-pi)
  * [Turn on the BLE interface](#turn-on-the-ble-interface)
  * [Installing linux and python packages](#installing-linux-and-python-packages)
  * [Downloading script](#downloading-script)
* [Usage](#usage)
  * [Printing data to the terminal window](#printing-data-to-the-terminal-window)
  * [Piping data to a text-file](#piping-data-to-a-text-file)
* [Sensor data description](#sensor-data-description)
* [Release notes](#release-notes)  

# Requirements

The following tables shows a compact overview of dependencies for this project.

**List of OS dependencies**

| OS | model/version | Comments |
|-------------|-------------|-------------|
| Raspbian | Raspberry Pi 3 Model B | Used in this project.
| Linux    | x86 Debian             | Should work according to [bluepy](https://github.com/IanHarvey/bluepy)

**List of linux/raspberry dependencies**

| package | version | Comments |
|-------------|-------------|-------------|
| python         | 2.7 | Tested with python 2.7.13
| python-pip     |     | pip for python2.7
| git            |     | To download this project
| libglib2.0-dev |     | For bluepy module

**List of Python dependencies**

| module | version | Comments |
|-------------|-------------|-------------|
| bluepy      | 1.2.0 | Newer versions have not been tested.
| tableprint  | 0.8.0 | Newer versions have not been tested.

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

The default password for the “pi” user is “raspberry”.

## Turn on the BLE interface

In the terminal window on your Raspberry Pi:

```
pi@raspberrypi:~$ bluetoothctl
[bluetooth]# power on
[bluetooth]# show
```

After issuing the command ```show```, a list of bluetooth settings will be printed
to the Raspberry Pi terminal window. Look for ```Powered: yes```.

## Installing linux and python packages

> **Note:** The ```read_wave.py``` script is only compatible with Python2.7.

The next step is to install the bluepy Python library for talking to the BLE stack. 
For the current released version for Python 2.7:

```
pi@raspberrypi:~$ sudo apt-get install python-pip libglib2.0-dev
pi@raspberrypi:~$ sudo pip2 install bluepy==1.2.0
```

Make sure your Raspberry Pi has git installed

```
pi@raspberrypi:~$ git --version
```

or install git to be able to clone this repo.

```
pi@raspberrypi:~$ sudo apt-get install git
```

Additionally, the ```read_wave.py``` script depends on the ```tableprint``` module
to print nicely formated sensor data to the Raspberry Pi terminal at run-time.

```
pi@raspberrypi:~$ sudo pip2 install tableprint==0.8.0
```

> **Note:** The ```read_wave.py``` script has been tested with bluepy==1.2.0 and tableprint==0.8.0. You may download the latest versions at your own risk.

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

To read the sensor data from the Airthings Wave using the ```read_wave.py``` script,
you need the 10-digit serial number of the device. This can be found under the magnetic backplate 
of your Airthings Wave.

If your device is paired and connected to e.g. a phone, you may need to turn off bluetooth on
your phone while using this script.

```cd``` into the directory where the ```read_wave.py``` script is located if you cloned the repo.

The general format for calling the ```read_wave.py``` script is as follows:

```
read_wave.py SN SAMPLE-PERIOD [pipe > yourfilename.txt]
```

where the input arguments are:

| input argument | example | Comments |
|-------------|-------------|-------------|
| SN            | 0123456789              | 10-digit number. Can be found under the magnetic backplate of your Airthings Wave Plus.
| SAMPLE-PERIOD | 60                      | Read sensor values every 60 seconds. Must be larger than zero.
| pipe          | pipe > yourfilename.txt | Optional. Since tableprint is incompatible with piping, we use a third optional input argument "pipe".

> **Note on choosing a sample period:** 
Temperature and humidity are updated every time we read the wave.
Radon measurements are updated once every hour.

## Printing data to the terminal window

By default, the ```read_wave.py``` script will print the current sensor values to the Rasberry Pi terminal.
Run the Python script in the following way:

```
pi@raspberrypi:~/wave-reader $ sudo python2 read_waveplus.py SN SAMPLE-PERIOD
```

where you change ```SN``` with the 10-digit serial number, and change ```SAMPLE-PERIOD``` to a numerical value of your choice.

After a short delay, the script will print the current sensor values to the 
Raspberry Pi terminal window. Exit the script using ```Ctrl+C```.

## Piping data to a text-file

If you want to pipe the results to a text-file, you can run the script in the following way:

```
pi@raspberrypi:~/wave-reader $ sudo python2 read_wave.py SN SAMPLE-PERIOD pipe > yourfilename.txt
```

where you change ```SN``` with the 10-digit serial number, and change ```SAMPLE-PERIOD``` to a numerical value of your choice.

Exit the script using ```Ctrl+C```.

# Sensor data description

| sensor | units | Comments |
|-------------|-------------|-------------|
| Datetime                      | YYYY-MM-DD HH:MM:SS | 
| Humidity                      | %rH | 
| Temperature                   | &deg;C | 
| Radon short term average      | Bq/m3 | First measurement available 1 hour after inserting batteries
| Radon long term average       | Bq/m3 | First measurement available 1 hour after inserting batteries

# Contribution

Let us know how it went! If you want contribute, you can do so by posting issues or suggest enhancement
[here](https://github.com/Airthings/wave-reader/issues), or you can open a pull request for review
[here](https://github.com/Airthings/wave-reader/pulls).


# Release notes

Release dated 14-Dec-2018

* Added SAMPLE-PERIOD as an input argument.

Initial release 13-Dec-2018