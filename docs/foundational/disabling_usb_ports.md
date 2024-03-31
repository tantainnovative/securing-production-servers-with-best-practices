# Disabling Unnecessary USB Ports (Onsite)

## Overview

Restricting the usage of USB ports on your Linux servers is a crucial security measure to prevent data leaks and mitigate the risk of malware infections through unauthorized devices. Disabling unnecessary USB ports helps enhance physical security and reduce the attack surface.

## Importance

- **Data Protection:** Prevents unauthorized data transfer and theft via USB devices.
- **Malware Prevention:** Reduces the risk of introducing malware or malicious software through USB devices.

## Steps to Disable USB Ports

1. **Identify USB Controllers:**
   - Use the `lsusb` command to list all USB controllers and devices connected to your system. This will help you identify which USB ports are in use and which can be disabled.

     ```bash
     lsusb
     ```

2. **Disable USB Ports via BIOS/UEFI:**
   - The most secure way to disable USB ports is through the BIOS or UEFI settings. Access your system's BIOS/UEFI menu during boot and look for options to disable individual USB ports or the entire USB controller.

3. **Disable USB Kernel Modules:**
   - You can prevent the USB subsystem from being loaded by the Linux kernel by blacklisting the USB storage module. Edit the `/etc/modprobe.d/blacklist.conf` file and add the following line:

     ```bash
     blacklist usb-storage
     ```

     This will prevent the loading of the USB storage driver, effectively disabling USB storage devices. Note that this will not disable other USB devices like keyboards or mice.

4. **Use udev Rules:**
   - For more granular control, you can create udev rules to disable specific USB devices based on their attributes. For example, to disable all USB storage devices, create a file `/etc/udev/rules.d/usb-storage-disable.rules` with the following content:

     ```bash
     ACTION=="add", SUBSYSTEM=="usb", DRIVER=="usb-storage", RUN+="/bin/sh -c 'echo 0 > /sys$DEVPATH/authorized'"
     ```

5. **Physically Disable or Block Ports:**
   - In highly secure environments, you may consider physically disabling USB ports by removing connectors or using port blocking devices.

## Best Practices

- **Regularly Review USB Usage:** Periodically review the usage of USB ports and devices to ensure that only authorized devices are connected.
- **Implement Device Control Policies:** Establish policies for the use of removable devices and enforce them through technical controls and user training.
- **Monitor for Unauthorized Devices:** Use monitoring tools to detect and alert on unauthorized USB devices connected to the system.

By taking these steps to disable unnecessary USB ports, you can significantly enhance the physical security of your Linux servers and protect against potential data breaches and malware infections.
