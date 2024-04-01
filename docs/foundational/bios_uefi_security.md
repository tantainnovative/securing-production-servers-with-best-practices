# BIOS/UEFI Security (physical servers)

## Overview

The BIOS (Basic Input/Output System) and UEFI (Unified Extensible Firmware Interface) are firmware interfaces that serve
as the intermediary between the server's firmware and its operating system. Securing the BIOS/UEFI is paramount in
server hardening as it represents the first line of defense against a swath of pre-boot attacks and unauthorized system
modifications.

## Importance

- **Pre-boot Security Barrier:** Establishing stringent BIOS/UEFI security measures blocks attackers from compromising
  the server before the operating system's protective mechanisms are loaded.
- **Root of Trust Assurance:** A secure BIOS/UEFI ensures that the server's boot process begins in a trusted state,
  thwarting potential threat vectors from the lowest system level.
- **Bootkit and Rootkit Guard:** By securing the BIOS/UEFI, you protect against some of the most pernicious forms of
  malware that operate beneath the OS level.


## Key Steps for Enhanced Security

### Password Protection:

- **Step-by-Step Instructions:**
    - Restart your server and press the key indicated on the startup screen to enter BIOS/UEFI (
      commonly `Del`, `F2`, `F10`, `F12`).
    - Navigate to the Security tab using the arrow keys.
    - Locate the Password or Administrator Password option.
    - Enter a strong password that includes a mix of uppercase, lowercase, numbers, and special characters.
    - Save and exit the BIOS/UEFI setup (often by pressing `F10`).
- **Example:**
   ```
   Entering Setup: Press F2
   Security Tab > Set Administrator Password: [********]
   Confirm Password: [********]
   Save & Exit Setup: Press F10 > Yes
   ```

### Control Over Boot Sequence:

- **Step-by-Step Instructions:**
    - Enter BIOS/UEFI settings as outlined above.
    - Find the Boot or Startup tab.
    - Adjust the boot order to prioritize internal hard drives over external devices.
    - Disable any options related to booting from USB, CD/DVD, or network unless needed.
    - Save the new boot sequence and exit.
- **Example:**
   ```
   Boot Tab > Boot Device Priority:
   1st Boot Device [HDD0]
   2nd Boot Device [Disabled]
   3rd Boot Device [Disabled]
   Save & Exit Setup: Press F10 > Yes
   ```

### Secure Boot Activation:

- **Step-by-Step Instructions:**
    - Access your BIOS/UEFI settings.
    - Navigate to the Boot, Security, or Authentication tab.
    - Locate the Secure Boot option and change its status to Enabled.
    - Confirm any additional prompts to enable Secure Boot.
    - Save the changes and reboot the server.
- **Example:**
   ```
   Security Tab > Secure Boot > Secure Boot Enable: [Enabled]
   Save & Exit: Press F10 > Yes
   ```

### Firmware Vigilance:

- **Step-by-Step Instructions:**
    - Visit your server hardware manufacturer’s website to check for firmware updates.
    - Download the appropriate update for your server model.
    - Follow the manufacturer's instructions to apply the firmware update, which may include creating a bootable USB or
      running the updater from within the operating system.
- **Example:**
   ```
   Visit: [Manufacturer's Support Page]
   Download: BIOS Update Utility for Model XYZ
   Follow On-Screen Instructions to Update Firmware
   ```

### Access Restriction:

- **Practical Measures:**
    - Keep server rooms locked and implement access control measures such as key cards or biometric scanners.
    - Use cable locks or other physical security devices to secure servers to their racks.
    - Implement surveillance systems to monitor physical access to the servers.
- **Example:**
   ```
   Data Center Access Log:
   09:30 AM - John Doe accessed Server Room A
   09:45 AM - John Doe exited Server Room A
   Security Notice: All server access is logged and monitored.
   ```


## Best Practices

- **Continuous Firmware Updates:** Implement a schedule for checking and applying firmware updates as they become
  available from your hardware provider.
- **Security-Optimized Configuration:** Scrutinize and configure all BIOS/UEFI settings to adhere to security best
  practices, such as disabling superfluous ports or interfaces.
- **Configuration Backup:** Utilize the BIOS/UEFI backup capabilities to save current settings, facilitating recovery if
  configurations become corrupted or are erroneously altered.
- **Change Monitoring:** Deploy monitoring solutions capable of logging and alerting on unauthorized changes to
  BIOS/UEFI settings.

## Secure Boot Deep Dive

- **Understanding Secure Boot:**
    - Dive into the workings of Secure Boot, the role of platform keys (PK), key enrollment keys (KEK), and how
      signature databases enforce boot security.
- **Key Management:**
    - Offer guidance on managing Secure Boot keys and certificates to ensure that only signed and approved software can
      execute during the boot process.

## Troubleshooting

- **Common Issues:**
    - Address typical BIOS/UEFI problems, such as lost passwords, boot order issues, and update failures.
- **Recovery Procedures:**
    - Provide step-by-step instructions for recovering from misconfigurations or restoring BIOS/UEFI to a known good
      state.

## FAQs

- **What if I forget my BIOS/UEFI password?**
    - Detail methods for password recovery or reset procedures provided by the hardware manufacturer.
- **How often should firmware be updated?**
    - Recommend a firmware review interval and the best practices for applying updates without disrupting server
      availability.

By fortifying BIOS/UEFI settings, you can significantly elevate the pre-boot security posture of your Linux servers,
thereby establishing a robust foundation for comprehensive system protection. This is a vital step in constructing a
fortified server environment resistant to advanced threats.