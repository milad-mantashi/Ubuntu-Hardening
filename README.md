
# Ubuntu-Hardening
# 🔒 Ubuntu 24.04 Hardening Script (CIS Level 1 - Modular)

This project provides an automated Bash script to harden Ubuntu 24.04 LTS systems based on the **CIS Benchmark Level 1 - Server Profile**. The script is modular, logs every section and command, and enforces security best practices from the ground up.

---

## ✅ Features

- Implements **CIS Level 1** server recommendations
- Modular sectioned logging with success/error tracking
- Removes unnecessary packages and services
- Hardens kernel, network, and SSH settings
- Enforces password policies and account protections
- Secures logging and auditing with `auditd`, `rsyslog`, `acct`
- Configures UFW firewall with sane defaults
- Verifies critical mount points and partitions

---

## 📋 Compliance Summary (CIS Ubuntu 24.04 Level 1) 100% (un-comment the password complexity before running to reach 100%)

| **CIS ID**       | **Control**                                          | **Status**     |
|------------------|------------------------------------------------------|----------------|
| 1.1.x            | Disable unused filesystems                           | ✅ Implemented |
| 1.1.1 - 1.1.24   | Check separate partitions (`/home`, `/var`, etc.)    | ✅ Verified    |
| 1.2.x            | Secure bootloader (GRUB permissions)                 | ✅ Hardened    |
| 1.3.x            | AppArmor profiles use complain mode                  | Review profiles |
| 1.4.x            | Kernel security settings (ASLR, core dumps)          | ✅ Applied     |
| 1.5.x            | Software updates and `unattended-upgrades`           | ✅ Enabled     |
| 1.6.x            | Legal banners (`/etc/issue`, `/etc/motd`)            | ✅ Set         |
| 1.7.x            | Remove GUI login (GDM)                               | ✅ Removed     |
| 2.1.x            | Remove unused services (e.g., FTP, RPC, etc.)        | ✅ Purged      |
| 2.2.x            | Remove X Window System                               | ✅ Removed     |
| 2.3.x            | Disable Avahi, Autofs                                | ✅ Disabled    |
| 2.4.x            | NTP with `systemd-timesyncd`                         | ✅ Configured  |
| 2.5.x            | Secure `cron` and `at`                               | ✅ Hardened    |
| 3.x              | Network stack hardening and IPv6 disable             | ✅ Done        |
| 4.x              | UFW Firewall with sane defaults                      | ✅ Enabled     |
| 5.1.x            | SSH configuration hardening                          | ✅ Hardened    |
| 5.2.x            | Secure sudo configuration                            | ✅ Enforced    |
| 5.4.x            | Password policy (age, complexity, reuse, umask)      | ✅ Enforced    |
| 5.5.x            | Account auditing and UID 0 checks                    | ✅ Audited     |
| 6.1.x            | `auditd` logging and audit rules                     | ✅ Comprehensive |
| 6.2.x            | Enable and secure `rsyslog`                          | ✅ Done        |
| 6.3.x            | Log rotation and journald settings                   | ✅ Configured  |
| 6.4.x            | Enable `acct` and process tracking                   | ✅ Enabled     |
| 6.5.x            | Secure `/etc/passwd`, `/etc/shadow`, etc.           | ✅ Permissioned |



## 📋 CIS Benchmark Coverage (Level 2 - Ubuntu 24.04) 90% (the log will indicate the partition information but it is not possible to auto-fix this as this can change for every installation, hence I prefered to be a manual work) and AppArmor recommendations is set on complain to avoid brasking the system, the change for each profile has to be manual depending on the apps installed)

| **Section**      | **Control**                                    | **Status**     |
|------------------|--------------------------------------------------|----------------|
| 1.1.x            | Filesystem: Remove & restrict unused FS         | ✅ Done         |
| 1.1.1–1.1.24     | Mount options + partitions for `/tmp`, `/var`, etc. | ⚠️ Partially Done |
| 1.2.x            | Secure GRUB & permissions                        | ✅ Hardened     |
| 1.3.x            | AppArmor profiles use complain mode              | ⚠️ Review profiles |
| 1.4.x            | Kernel hardening (ASLR, ptrace, dumps)           | ✅ Set          |
| 1.5.x            | Update settings and unattended upgrades          | ✅ Enabled      |
| 1.6.x            | Login banner + permissions                       | ✅ Compliant    |
| 1.7–1.8          | Remove X/GDM                                     | ✅ Removed      |
| 2.x              | Disable unused services                          | ✅ Extensive    |
| 3.x              | Disable uncommon kernel modules, IPv6, redirects | ✅ Done         |
| 4.x              | Enable firewall (UFW)                            | ✅ Enabled      |
| 5.1.x            | Secure SSH server configuration                  | ✅ Hardened     |
| 5.2.x            | Secure sudo policy (logging, timeouts)           | ✅ Compliant    |
| 5.4.x            | Password aging, complexity, reuse                | ✅ Enforced     |
| 5.5.x            | Disable empty or legacy accounts                 | ✅ Done         |
| 6.1.x            | Enable and configure `auditd`                    | ✅ Full ruleset |
| 6.2.x            | Configure `rsyslog`                               | ✅ Enabled      |
| 6.3.x            | Setup `logrotate`, persistent `journald`         | ✅ Set          |
| 6.4.x            | Enable `acct` and process auditing               | ✅ Enabled      |
| 6.5.x            | Secure critical files (`/etc/shadow`, etc.)      | ✅ Permissioned |

---
## Script Changes

The following changes correct errors and reduce risk in `Hardening-Ubuntu-2024.sh`:

- **Log path:** The script now uses `/home/<user>/setup_logs/hardening`. When it runs as root without a `SUDO_USER` value, it uses `/root/setup_logs/hardening`. This gives the log path a directory name and avoids an invalid path when the script runs as root.
- **Command runner:** The runner now starts each command with `bash -c`. This avoids use of `eval` in the current shell.
- **AppArmor message:** The status message now says that profiles use complain mode. The script calls `aa-complain`, so the message now matches its action.
- **UFW network rule:** The allowed subnet is now `192.168.10.0/24`. This expresses the intended network range.
- **SSH configuration:** The script writes its settings to `/etc/ssh/sshd_config.d/00-hardening.conf`. It no longer replaces the main SSH configuration file. It also keeps `chacha20-poly1305@openssh.com` enabled by removing it from the list of ciphers to disable.
- **Repeated settings:** The script checks for existing lines before it adds selected settings for core dumps, NTP, timeouts, umask, auditd, rsyslog, journald, and process audit rules. This reduces duplicate lines when the script runs more than once.
- **Account audit commands:** The GID 0 audit command now has valid shell quoting and writes group names and GIDs to its log. The empty-password command now uses `xargs -r`, so it does not call `passwd` when there are no matching accounts.
- **Account file permissions:** The script uses mode `640` for `/etc/shadow` and `/etc/gshadow`, with owner `root` and group `shadow`. This allows the shadow group to read these files. The section and log labels now use section `7.1` and match the account file controls.

These edits do not confirm CIS compliance. Review the settings for your system before you run the script. The script was not run as part of these edits.

---
⚠️ Disclaimer
This script applies system-level changes. Use with caution in production. Always test in a staging environment first.
This project is provided "as is" and is not affiliated with the Center for Internet Security (CIS).
---

🧪 Tested On
-✅ Ubuntu 24.04 LTS (Server)
-🧪 LXC, KVM, bare-metal and cloud VMs
- 🧰 Compatible with Proxmox, VMware, Hyper-V, Oracle Cloud, and more

## 📂 Directory Structure

```bash
/home/<user>/setup_logs/hardening/
├── main.log                 # High-level section logs
└── section_logs/
    ├── <section_id>/
        ├── success.log
        ├── error.log
        └── details.log



