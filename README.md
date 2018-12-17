# Raspberry Pi Zero W Bluez v5.50 upgrade steps
**Posted on 12/17/2018** <br>
These are the steps I follow for upgrading the bluetooth version 5.43 included in Raspbian Stretch Lite (kernel version 4.14 dated 11/13/2018) to the latest version of Bluez 5.50.  The reason for the upgrade is to setup the Raspberry Pi Zero W as a Bluetooth Low Energy (BLE) Peripheral.  I disable a great deal of the default bluetooth services since I don’t need them to be a BLE Peripheral.  So do not follow these instructions if your trying to setup your Raspberry Pi as a generic desktop device.

Start with a fresh install of Raspbian Stretch Lite from [RaspberryPi.org](https://www.raspberrypi.org/downloads/raspbian/) on your SD card.  Connect a keyboard and monitor to your raspberry Pi and start at step 1.
# Install Bluez 5.50
1.	From your physically attached keyboard login with user = pi and password = raspberry
2.	Type `sudo raspi-config` and set the following:
    * Set a new password
	* Set new host name
	* Join local WiFi
	* Enable SSH
	* Set graphics memory to minimum 16kb
	* Set local time zone
	* Expand os to use all of SD card
3.	Type `sudo reboot`	After it boots back up log back into your pi with your new password.
4.	Type `sudo apt-get install libdbus-1-dev libglib2.0-dev libudev-dev libical-dev libreadline-dev -y`
5.	Type `wget www.kernel.org/pub/linux/bluetooth/bluez-5.50.tar.xz`
6.	Type `tar xvf bluez-5.50.tar.xz && cd bluez-5.50`
7.	Type `./configure --prefix=/usr --mandir=/usr/share/man --sysconfdir=/etc --localstatedir=/var --disable-cups --disable-a2dp --disable-avrcp --disable-network --disable-hid --disable-hog`
8.	Type `make -j4`
9.	Type `sudo make install`
10.	Type `sudo reboot` reboot and log back in
11.	Type `sudo cp /usr/libexec/bluetooth/bluetoothd /usr/lib/bluetooth`
12.	Type `bluetoothctl -v` should say 5.50
13.	Type `bluetoothd --version` should say 5.50
14. Type `sudo apt-get update && sudo apt-get upgrade`
15. Type `rm bluez-5.50.tar.xz && rm -r -f bluez-5.50` to remove install files.
## Configure Bluez
1.  Type `sudo nano /lib/systemd/system/bluetooth.service` and change the ExecStart line to match the following:
    * ExecStart=/usr/libexec/bluetooth/bluetoothd --noplugin=wiimote,battery,deviceinfo,hostname,scanparam,autopair
    * save and exit
2.  Type `sudo nano /etc/bluetooth/main.conf` 
    * remove the pound-sign from in front of Name = and set a name for your peripheral.  In my case the **Name = rGauge Transmitter**
    * remove the pound-sign from in front of ControllerMode and set it = le.  **ControllerMode = le**.
    * save and exit.
3.  Type `sudo reboot` log back in and check versions.  You can also type `sudo systemctl status bluetooth` to check service status.
## Notes
* Bluez 5.50 requires applications to use their D-Bus API to communicate with it. Documentation for this API can be found here: https://kernel.googlesource.com/pub/scm/bluetooth/bluez/+/5.50/doc.  Look at the gatt-api.txt, it forms the heart of a BLE Peripheral.  
* As an FYI I have written a node.js class [blePeripheral]( https://github.com/RuckerGauge/blePeripheral) that sets up a sample Bluetooth LE Peripheral over this Bluez D-Bus interface.  
* If you would like to query the bluez dbus interface you can type `sudo gdbus introspect --system --dest com.netConfig --object-path / --recurse`
* If you would like to install and run my [blePeripheral]( https://github.com/RuckerGauge/blePeripheral) you will need to install node.js and github onto your Raspberry Pi zero.  I have provided those steps below:
* The above steps were created with input from these posts: 
    * https://scribles.net/updating-bluez-on-raspberry-pi-5-43-to-5-48/
    * https://www.raspberrypi.org/forums/viewtopic.php?t=181566#p1156312
    * https://www.instructables.com/id/Control-Bluetooth-LE-Devices-From-A-Raspberry-Pi/
    * http://www.linuxfromscratch.org/blfs/view/8.3-systemd/general/bluez.html

# Install Node.js and GitHub
I think it is important to be on the latest stable version of node.js and I do my best to make sure my apps support it.  Here is how I find the latest version for the Raspberry Pi Zero W. These steps are based on information from https://www.instructables.com/id/Install-Nodejs-and-Npm-on-Raspberry-Pi/
## Node and NPM install

* Type `uname -m` You will see the Raspberry Pi Zero returns armv61
* Now go to this website https://nodejs.org/en/download/ to find the latest version of node based on your hardware.  You should see an option to click on ARMv6 in the Linux Binaries row of the table.  The link behind that button is what you want.  Today 12/17/2018 the link is https://nodejs.org/dist/v10.14.2/node-v10.14.2-linux-armv6l.tar.xz. Download that on your Pi by typing `wget https://nodejs.org/dist/v10.14.2/node-v10.14.2-linux-armv6l.tar.xz`
* Type `tar -xf node-v10.14.2-linux-armv6l.tar.xz` to expand the tarball into a subdirectory.
* Type `cd node-v10.14.2-linux-armv6l`
* Type `sudo cp -R * /usr/local/` 
* Type `node -v` to see the node version.  In this case it should come back with v10.14.2
* You can now remove the install files. Type `cd` then type `rm node-v10.14.2-linux-armv6l.tar.xz && rm -r -f node-v10.14.2-linux-armv6l`
## Git Hub install
* Type `sudo apt-get install git` 
* As one last step (probably not necessary) I check for any updates. Type `sudo apt-get update && sudo apt-get upgrade`
## Next Step
Head on over to the install section of the [blePeripheral]( https://github.com/RuckerGauge/blePeripheral) to setup a sample D-Bus based bluetooth LE Peripheral and securely connect to it with you iPhone! 


