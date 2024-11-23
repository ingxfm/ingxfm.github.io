+++
title = 'USB converters: do not change your names'
date = 2024-11-22T10:03:39+01:00
draft = false
lang = 'en'
+++

You need to connect 3 USB cables to an Ubuntu 22.04 workstation. These 3 USB cables are 1 UART 232TTL USB converter cable and 2 RS485 USB converter cables.

You have needed to reboot this workstation a few times. Or connect and disconnect the cables and you noticed that the naming (/dev/ttyUSB0, /dev/ttyUSB1, /dev/ttyUSB2) of the cables as serial ports are changing in your Ubuntu OS.

Because you are running a program that is using this names you would like them to stay fixed for each cable. Then, you have come to right place. Let's do just that.

# Assigning names for specific cables

We need to find a way to identify each cable. Each must have some information from the factory that could be an unique identifier. For this, we use udev rules and commands. For getting, some factory info:

```
udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep manufacturer
udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep product
udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep serial
```
From these 3 commands, I get the following output one of my cables:
```
➜  ~ udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep manufacturer
    ATTRS{manufacturer}=="FTDI"
    ATTRS{manufacturer}=="Generic"
    ATTRS{manufacturer}=="Linux 6.5.0-45-generic xhci-hcd"
➜  ~
➜  ~ udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep product    
    ATTRS{product}=="TTL232R-3V3"
    ATTRS{product}=="4-Port USB 2.0 Hub"
    ATTRS{product}=="xHCI Host Controller"
➜  ~
➜  ~ udevadm info --attribute-walk --path=/sys/bus/usb-serial/devices/ttyUSB0 | grep serial
    SUBSYSTEM=="usb-serial"
    ATTRS{serial}=="FTD1BYGI"
    ATTRS{serial}=="0000:00:14.0"
```

Let's repeat these commands for each of the 3 cables. Notice that the differentiator in these data points is the usb-serial number. Let's use this for creating a rule that binds or assigns a name whenever the USB port detect that this cable is connected. Let's create or modify the file /lib/udev/rules.d/10-local.rules.
```
sudo nano /lib/udev/rules.d/10-local.rules
```
Then, let's copy copy the following info. 
```
###########################
# Serial 232TTL cable     #
###########################
SUBSYSTEM=="tty", ATTRS{product}=="TTL232R-3V3", ATTRS{serial}=="FTD1BYGI", SYMLINK+="conv_serial232TTL_control"

####################################
# RS485 USB cable 1                #
####################################
SUBSYSTEM=="tty",ATTRS{serial}=="AV0KDAA6", ATTRS{manufacturer}=="FTDI", SYMLINK+="conv_RS485_furnace"

####################################
# RS485 USB cable 2                #
####################################
SUBSYSTEM=="tty",ATTRS{serial}=="FT54F6WE", ATTRS{manufacturer}=="FTDI", SYMLINK+="conv_RS485_meter"
```

Notice that in the symbolic link SYMLINK we can type any strings you want. For this example, let's write the abbreviation "conv" for converter with underscore and then what the cable communicates to.

Now, do Ctrl+S to Save the changes in the file; and Ctrl+X to close nano.

Then, let's reload our udev settings with:
```
sudo udevadm control --reload
```

And let's deploy the rules with the trigger:
```
sudo udevadm trigger
```
 
Then, check our new names for the USB converters with:
```
ls /dev/conv_*
```

The output is:
```
/dev/conv_serial232TTL_control /dev/conv_RS485_furnace /dev/conv_RS485_meter
```

# Conclusion
There, we go. Now, we can connect and disconnect these USB cables, or reboot the workstation and their naming will persist.

Cheers!
