---
title: "Installing Debian 12 (Bookworm) on Raspberry Pi 5"
description: "While Debian 12 (codenamed Bookworm) is not yet officially released for the Raspberry Pi 5, you can install by following this approach. This guide will walk you through the steps to install Debian 12 on your Raspberry Pi 5."
date: 2024-10-07T13:29:58+10:00
draft: false
image: images/icons/motherboard-icon-red.png
enableToc: true
enableTocContent: true
tocPosition: inner

---
# Installing Debian 12 Bookworm on Raspberry Pi 5: A Step-by-Step Guide Using the FlightRadar24 Image

Sometimes, you just want plain Debian 12 and you want it on the latest Raspberry Pi 5. This guide will walk you through the process of setting up Debian 12 on your Raspberry Pi 5 using a ready-made FlightRadar24 image, which is based on Debian Bookworm, and then optimizing the system by removing unnecessary software like FlightRadar24 and dump1090-mutability. We'll also troubleshoot potential firmware issues, update the login message, and customize the system.

{{< notice info >}}
We are using this approach because, as of this writing, the Debian project does not officially support or provide images specifically for the Raspberry Pi 5.
{{< /notice >}}

## Step 0: Assemble your Raspberry Pi 5

Watch the below video if you wish to see the assembly of the Pi with Active Cooler and case

{{< youtube 5zxoCf9V3PE >}}

## Step 1: Download the FlightRadar24 Image

Before we begin, we'll use the FlightRadar24 image, which is already built on **Debian 12 Bookworm**. This image is designed for those who want to track aircraft and run a PiAware setup, but even if that's not your goal, it's a convenient starting point since it’s based on the latest version of Debian.

1. **Download the FlightRadar24 Image** from [FlightRadar24 Build Your Own](https://www.flightradar24.com/build-your-own).
2. Unzip the downloaded file to prepare it for flashing onto your SD card.

## Step 2: Flash the Image Using Raspberry Pi Imager

The **Raspberry Pi Imager** simplifies the process of flashing operating systems to SD cards. It also allows you to configure essential settings like hostname, Wi-Fi, and user credentials without manually editing files afterward.

1. **Download Raspberry Pi Imager** from [Raspberry Pi Software](https://www.raspberrypi.com/software/).
2. Insert your SD card into your computer.
3. Open **Raspberry Pi Imager**
4. Select **CHOOSE OS** and select **Use custom**
5. Select the FlightRadar24 image you just downloaded in Step 1
6. Select the Storage and select the empty SD Card that you have attached to your computer
4. Click **Edit Settings** when prompted to apply OS customisation settings. Here, you can:
   - Set a **hostname** for your Raspberry Pi.
   - Set up the **username and password** for the system.
   - Configure the **Wi-Fi network** by adding your SSID and password.
   - Enable **SSH** under Services tab, if you want remote access right from the start.
5. Click **Write** to flash the image to your SD card and apply the settings.
6. Once the process is complete, safely eject the SD card.

## Step 3: Boot Your Raspberry Pi

1. Insert the SD card into your Raspberry Pi 5 and power it on.
2. The Pi might have reboot automatically more than once to initialise the username/passwords, connecting to WiFi etc.
3. It will boot into the FlightRadar24 setup screen or a web-based GUI. If you're not interested in using FlightRadar24, follow the next steps to remove this software and related services.

## Step 4: Remove FlightRadar24 and Dump1090-mutability

By default, the image includes **FR24Feed** and **dump1090-mutability**, which are used for aircraft tracking. If you do not need them, you can uninstall them to free up resources.

1. **Stop and Disable FR24Feed**:
   ```bash
   sudo systemctl stop fr24feed
   sudo systemctl disable fr24feed
   sudo apt-get remove --purge fr24feed

2. **Stop and Disable Dump1090-mutability:**:
   ```bash
    sudo systemctl stop dump1090-mutability
    sudo systemctl disable dump1090-mutability
    sudo apt-get remove --purge dump1090-mutability

3. **Remove Lighttpd**:
   ```bash
   sudo systemctl stop lighttpd
   sudo systemctl disable lighttpd
   sudo apt-get remove --purge lighttpd

3. **Remove fr24gui**:
   ```bash
   sudo systemctl stop fr24gui
   sudo systemctl disable fr24gui.service
   sudo apt-get remove --purge fr24gui

3. **Validate if there are any services that might be running related to fr24**:
   ```bash
   sudo systemctl list-unit-files --type=service

3. **Clean up remaining packages:**:
   ```bash
    sudo apt-get autoremove --purge
    sudo apt-get clean

## Step 5: Fixing Firmware and Boot Issues

If you encounter errors like `cannot read file system information for /boot/firmware`, follow these steps to resolve the issue:

1. Unmount and recreate the /boot/firmware directory:
   ```bash
    sudo umount /boot
    sudo mkdir /boot/firmware

2. Modify /etc/fstab to reference /boot/firmware:

   ```bash
    sudo sed -i "s:boot:boot/firmware:" /etc/fstab // Note that the character after s is a colon followed by boot s(colon)boot(colon)boot/firmware:)
    sudo systemctl daemon-reload
    sudo mount /boot/firmware
    sudo  apt -f install

3. Reload the system daemon and install any pending packages:

   ```bash
    sudo systemctl daemon-reload
    sudo mount /boot/firmware
    sudo apt -f install

## Step 6: Customise the message of the day (motd) [Optional]

1. Create a dynamic MOTD that displays useful system information such as:

* Number of running processes.
* System uptime.
* IP addresses of connected devices.
* IP address of the Raspberry Pi.

2. Add the dynamic script:

   ```bash
    sudo nano /etc/update-motd.d/01-custom

Add the following content to the file:
   ```bash
    #!/bin/bash
    echo "System Information as of: $(date)"
    echo "---------------------------------------------------"
    echo "Hostname: $(hostname)"
    echo "Uptime: $(uptime -p)"
    echo "Processes: $(ps aux | wc -l)"
    echo "Current IP Address: $(hostname -I)"
    echo "Connected IPs:" ss -tuna | grep ESTAB | awk '{print $5}'
    echo "---------------------------------------------------"
  ```    

## Check the Hardware and software properties

   ```bash
    cat /proc/device-tree/model
    lsb_release -a
    cat /etc/os-release
  ```  

### Screenshot:

{{< img src="/images/techblog/rpi5-debian-k8s-hw-os-properties.png" title="Validate the properties of the hardware and OS" caption="Commands to check the Model and OS versions" alt="image alt" >}}

## Conclusion

There are times when we prefer plain vanilla Debian over Ubuntu or Raspbian on a Raspberry Pi. While an official Debian image for the Raspberry Pi 5 is still in the works, this guide provides a reliable method to install Debian 12. If you have a specific reason for choosing Debian on your Raspberry Pi, feel free to share your use case in the comments below!

All the best!

### Note about Installing Piaware on Raspberry Pi 5
If you want to install Flightaware's piaware on Raspberry Pi 5, you can now do that on this image because Piaware can be installed on this Raspberry Pi 5

You wouldn't want to uninstall dump1090-mutability and others like we did in the earlier steps.