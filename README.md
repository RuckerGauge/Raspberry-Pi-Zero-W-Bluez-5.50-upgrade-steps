# Raspberry Pi Zero W Bluez v5.50 upgrade steps
12/17/2018
These are the steps I follow for upgrading the bluetooth version 5.43 included in Raspbian Stretch Lite (kernel version 4.14 as of 11/13/2018) to the latest version 5.50.  The reason for the upgrade is to setup the Raspberry Pi Zero W as a Bluetooth Low Energy (BLE) Peripheral.  I disable a great deal of the default bluetooth services since I don’t need them to be a BLE Peripheral.  So do not follow these instructions if your trying to setup your Raspberry Pi as a generic desktop device.

Start with a fresh install from [RaspberryPi.org](https://www.raspberrypi.org/downloads/raspbian/) on your SD card.  Connect a keyboard and monitor to your raspberry Pi and start at step 1.
1.	From your keyboard login with user = pi and password = raspberry
2.	Type **sudo raspi-config** and set the following:
    * Set a new password
	* Set new network name
	* Join local WiFi
	* Enable SSH
	* Set graphics memory to minimum 16kb
	* Set local time zone
	* Expand os to use all of SD card
3.	Type **sudo reboot**
4.	After it boots back up log back into your pi with your new password.


