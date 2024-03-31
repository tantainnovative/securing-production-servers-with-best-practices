# Enable SELinux

## Overview

Security-Enhanced Linux (SELinux) is a security architecture for Linux systems that provides a mechanism for enforcing access control policies. Enabling and properly configuring SELinux can significantly enhance the security of your server.

## Importance

- **Mandatory Access Control:** SELinux provides granular control over system resources and processes, enhancing security beyond traditional discretionary access control.
- **Process Isolation:** Helps isolate and contain processes, limiting the impact of potential exploits.
- **Policy Enforcement:** Ensures that only allowed operations are permitted, even for privileged processes.

## Steps to Enable and Configure SELinux

1. **Check SELinux Status:**
   - Use the `sestatus` command to check the current status of SELinux:

     ```bash
     sestatus
     ```

2. **Enable SELinux:**
   - If SELinux is disabled, you'll need to enable it by editing the `/etc/selinux/config` file:

     ```bash
     SELINUX=enforcing
     ```

3. **Reboot the System:**
   - For the changes to take effect, reboot your server:

     ```bash
     sudo reboot
     ```

4. **Set SELinux Policies:**
   - Determine the appropriate SELinux policies for your server based on your specific needs. Common policies include `targeted` (for general-purpose servers) and `mls` (for multi-level security).

5. **Manage SELinux Booleans:**
   - Use the `getsebool` and `setsebool` commands to manage SELinux boolean settings, which allow you to toggle certain permissions on and off.

     ```bash
     getsebool -a
     setsebool httpd_can_network_connect on
     ```

6. **Troubleshoot SELinux Issues:**
   - If you encounter issues related to SELinux, use tools like `sealert` to analyze SELinux logs and generate human-readable reports.

     ```bash
     sealert -a /var/log/audit/audit.log
     ```

## Best Practices

- **Regularly Review SELinux Policies:** Periodically review and audit your SELinux policies to ensure they align with your security requirements and operational needs.
- **Use Permissive Mode for Troubleshooting:** If you encounter issues, you can temporarily set SELinux to `permissive` mode to log violations without enforcing them, aiding in troubleshooting.
- **Stay Informed:** Keep up-to-date with the latest developments in SELinux policies and best practices.

By enabling and properly configuring SELinux, you can leverage its powerful access control capabilities to enhance the security of your Linux server.
