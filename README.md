# 🖥️ VMware Workstation Pro Setup Guide (Arch Linux)

## ✅ What is VMware Workstation Pro?

**VMware Workstation Pro** is a desktop virtualization platform that allows you to create and run virtual machines on Linux.

You can use VMware Workstation Pro to run operating systems such as:

* 🪟 **Windows 10 / 11**
* 🐧 **Ubuntu / Debian / Fedora / Arch Linux**
* 🐧 **Kali Linux**
* 🖥️ Other Linux distributions
* 💿 Other operating systems supported by VMware

VMware Workstation Pro supports networking, USB passthrough, shared folders, snapshots, virtual disks, and multiple virtual machines.

It works well with both full desktop environments and lightweight window managers such as **KDE Plasma, GNOME, XFCE, LXQt, Openbox, and i3wm**.

---

# 🐧 Installation

## 🟢 Arch Linux

VMware Workstation Pro can be installed on Arch Linux using the **AUR**.

### Step 1: Update System

First, update your Arch Linux system:

```bash
sudo pacman -Syu
```

### Step 2: Install Git & Build Tools

Install the tools required to build AUR packages:

```bash
sudo pacman -S git base-devel
```

### Step 3: Clone VMware Workstation from AUR

Create a Downloads directory:

```bash
mkdir -p ~/Downloads
cd ~/Downloads
```

Clone the VMware Workstation AUR package:

```bash
git clone https://aur.archlinux.org/vmware-workstation.git
```

### Step 4: Enter the Directory

```bash
cd vmware-workstation
```

### Step 5: Build & Install

Build and install the package:

```bash
makepkg -si
```

> 💡 `makepkg -si` automatically builds the package and installs the resulting package together with required dependencies.

---

# 🧩 Install VMware Kernel Modules

VMware requires kernel modules to work correctly.

For the standard Arch Linux kernel, install the corresponding headers:

```bash
sudo pacman -S linux-headers
```

If you use another kernel, install the matching headers.

### For linux-lts:

```bash
sudo pacman -S linux-lts linux-lts-headers
```

### For linux-zen:

```bash
sudo pacman -S linux-zen linux-zen-headers
```

> ⚠️ The kernel headers must match the kernel you are currently using.

Check your kernel:

```bash
uname -r
```

Check installed kernels:

```bash
pacman -Q | grep '^linux'
```

---

# ⚙️ Enable VMware Services

VMware uses several background services.

Enable and start the required services:

```bash
sudo systemctl enable --now vmware-networks.service
```

```bash
sudo systemctl enable --now vmware-usbarbitrator.service
```

You can also start the VMware host service:

```bash
sudo systemctl enable --now vmware-hostd.service
```

Check the services:

```bash
systemctl status vmware-networks.service
```

```bash
systemctl status vmware-usbarbitrator.service
```

```bash
systemctl status vmware-hostd.service
```

> 💡 Some VMware services are only required for specific features. If a service does not exist on your installation, check the installed package and VMware version.

---

# 🔌 VMware Network Configuration

VMware normally creates virtual network interfaces such as:

```text
vmnet0
vmnet1
vmnet8
```

Common configurations:

```text
vmnet0 → Bridged networking
vmnet1 → Host-only networking
vmnet8 → NAT networking
```

Check VMware network interfaces:

```bash
ip addr
```

Or:

```bash
ip link
```

You may see interfaces such as:

```text
vmnet1
vmnet8
```

---

# 🌐 NAT Networking

**NAT** allows a virtual machine to access the Internet through the host computer.

The virtual machine normally receives an IP address from VMware's DHCP service.

Check:

```bash
ip addr show vmnet8
```

You may see something similar to:

```text
vmnet8:
    inet 192.168.x.1/24
```

Inside the virtual machine, check its IP address:

```bash
ip addr
```

Test connectivity:

```bash
ping 8.8.8.8
```

Test DNS:

```bash
ping google.com
```

---

# 🌉 Bridged Networking

**Bridged networking** connects the virtual machine directly to the physical network.

The VM can receive an IP address from the same DHCP server as the host.

For example:

```text
Router
   │
   ├── Arch Linux Host
   │
   └── VMware VM
```

The VM appears as another device on the physical network.

Check the host's network interfaces:

```bash
ip addr
```

---

# 🖥️ Launch VMware Workstation

After installation, start VMware Workstation with:

```bash
vmware
```

You can also launch it from your desktop application menu.

Check the executable:

```bash
which vmware
```

---

# 🔍 Check VMware Installation

Check the installed package:

```bash
pacman -Qi vmware-workstation
```

Check VMware executable:

```bash
which vmware
```

Check VMware version:

```bash
vmware --version
```

Example:

```text
VMware Workstation ...
```

---

# 🧩 Check VMware Kernel Modules

Check whether VMware modules are loaded:

```bash
lsmod | grep vmw
```

You may see modules such as:

```text
vmmon
vmnet
```

You can also check:

```bash
modinfo vmmon
```

```bash
modinfo vmnet
```

If the modules are missing after a kernel update, rebuild/reinstall the VMware AUR package:

```bash
cd ~/Downloads/vmware-workstation
```

```bash
makepkg -si
```

Then reboot:

```bash
sudo reboot
```

---

# 🔄 Updating VMware

Navigate to the AUR repository:

```bash
cd ~/Downloads/vmware-workstation
```

Update the repository:

```bash
git pull
```

Build and install the new version:

```bash
makepkg -si
```

After updating the kernel or VMware, reboot if necessary:

```bash
sudo reboot
```

---

# 🖥️ VMware with i3wm

If you use **i3wm**, VMware Workstation can be launched using an i3 keyboard shortcut.

Open your i3 configuration:

```bash
nano ~/.config/i3/config
```

Add:

```i3
bindsym $mod+v exec --no-startup-id vmware
```

Reload i3:

```text
$mod + Shift + R
```

Launch VMware:

```text
$mod + V
```

---

# 🚀 Autostart VMware Services

VMware services should normally be started through `systemd`.

For example:

```bash
sudo systemctl enable --now vmware-networks.service
sudo systemctl enable --now vmware-usbarbitrator.service
```

You normally **do not need to start the VMware GUI automatically**.

If you want VMware Workstation itself to start with i3:

```i3
exec --no-startup-id vmware
```

> 💡 Autostarting VMware Workstation is optional and can significantly increase startup time and memory usage.

---

# 🔌 USB Passthrough

VMware can connect USB devices directly to a virtual machine.

Make sure the USB arbitrator service is running:

```bash
systemctl status vmware-usbarbitrator.service
```

Start it if necessary:

```bash
sudo systemctl enable --now vmware-usbarbitrator.service
```

Then:

1. Connect the USB device.
2. Start the virtual machine.
3. Open **VM → Removable Devices**.
4. Select the USB device.
5. Choose **Connect to the virtual machine**.

> 💡 A USB device connected to the VM may no longer be available to the Arch Linux host until it is disconnected from the VM.

---

# 💾 VMware Virtual Machines

VMware virtual machines are commonly stored in your home directory.

For example:

```text
~/vmware/
```

Create a directory:

```bash
mkdir -p ~/vmware
```

You can organize virtual machines like:

```text
~/vmware/
├── Windows-10/
├── Windows-11/
├── Ubuntu/
├── Debian/
├── Kali/
└── MikroTik-CHR/
```

---

# 💿 Create a New Virtual Machine

Start VMware:

```bash
vmware
```

Then:

1. Select **Create a New Virtual Machine**.
2. Select the installation media.
3. Choose the guest operating system.
4. Set the VM name.
5. Select the VM storage location.
6. Configure CPU and RAM.
7. Create the virtual disk.
8. Finish the setup.
9. Start the VM.

---

# 🧠 Recommended VM Resources

The required resources depend on the guest operating system.

### Windows 10

A reasonable starting configuration:

```text
CPU: 2–4 cores
RAM: 4–8 GB
Disk: 64+ GB
```

### Windows 11

Recommended starting configuration:

```text
CPU: 4 cores
RAM: 8 GB+
Disk: 64+ GB
```

### Linux Desktop

A typical configuration:

```text
CPU: 2–4 cores
RAM: 4–8 GB
Disk: 30+ GB
```

> 💡 Avoid allocating all host RAM or CPU cores to a virtual machine. Leave enough resources for Arch Linux and your desktop environment.

---

# 🛠️ VMware Tools

For better integration between the host and guest operating system, install **VMware Tools** or the appropriate open-source guest tools supported by your guest OS.

Features can include:

* 🖱️ Improved mouse integration
* 🖥️ Better display integration
* 📋 Clipboard integration
* 📁 Shared folders
* ⏱️ Time synchronization
* 🚀 Better guest performance

For many modern Linux distributions, the open-source **open-vm-tools** package is recommended.

### Arch Linux Guest

Inside an Arch Linux VM:

```bash
sudo pacman -S open-vm-tools
```

Enable the service:

```bash
sudo systemctl enable --now vmtoolsd.service
```

For desktop integration, additional packages may be useful depending on the desktop environment.

---

# 📁 Shared Folders

VMware Shared Folders allow files to be shared between the Arch Linux host and the guest OS.

After configuring a shared folder in VMware, Linux guests may access it through VMware Tools/open-vm-tools.

Check:

```bash
vmware-hgfsclient
```

If available, list shared folders:

```bash
vmware-hgfsclient
```

Mounting and exact configuration can vary depending on the guest distribution and VMware version.

---

# 🔧 VMware Network Troubleshooting

If the VM has no network connection, check:

```bash
ip addr
```

Check VMware network services:

```bash
systemctl status vmware-networks.service
```

Restart the service:

```bash
sudo systemctl restart vmware-networks.service
```

Check VMnet interfaces:

```bash
ip link | grep vmnet
```

Check:

```bash
vmnet8
```

for NAT networking.

---

# 🌐 Test VM Internet Connection

Inside the virtual machine:

```bash
ip addr
```

Check the default route:

```bash
ip route
```

Test the VMware NAT gateway:

```bash
ping 192.168.x.1
```

Test external connectivity:

```bash
ping 8.8.8.8
```

Test DNS:

```bash
ping google.com
```

If `8.8.8.8` works but `google.com` does not, the problem is most likely related to DNS.

---

# 🧰 Troubleshooting VMware Modules

If VMware reports errors related to `vmmon` or `vmnet`, check your kernel:

```bash
uname -r
```

Check installed kernel headers:

```bash
pacman -Q | grep headers
```

Install headers for the standard kernel:

```bash
sudo pacman -S linux-headers
```

Rebuild VMware:

```bash
cd ~/Downloads/vmware-workstation
makepkg -si
```

Then reboot:

```bash
sudo reboot
```

Check modules again:

```bash
lsmod | grep -E 'vmmon|vmnet'
```

---

# 🧹 Restart VMware Services

If VMware networking or USB functionality stops working:

```bash
sudo systemctl restart vmware-networks.service
```

```bash
sudo systemctl restart vmware-usbarbitrator.service
```

Check status:

```bash
systemctl --failed
```

---

# 🗑️ Uninstall VMware Workstation

Remove VMware Workstation:

```bash
sudo pacman -Rns vmware-workstation
```

Remove the AUR source directory:

```bash
rm -rf ~/Downloads/vmware-workstation
```

> ⚠️ Removing VMware Workstation does not necessarily remove your existing virtual machine files. If you want to keep your VMs, back up your `~/vmware/` directory before deleting anything.

---

# 🧹 Remove VMware Virtual Machines

If you no longer need your virtual machines:

```bash
rm -rf ~/vmware
```

> ⚠️ This permanently deletes the virtual machines and their virtual disks. Make sure you have backups before running this command.

---

# 📋 Quick Reference

| Task                   | Command                                                      |
| ---------------------- | ------------------------------------------------------------ |
| Update Arch            | `sudo pacman -Syu`                                           |
| Install build tools    | `sudo pacman -S git base-devel`                              |
| Install kernel headers | `sudo pacman -S linux-headers`                               |
| Clone AUR              | `git clone https://aur.archlinux.org/vmware-workstation.git` |
| Build VMware           | `makepkg -si`                                                |
| Launch VMware          | `vmware`                                                     |
| Check version          | `vmware --version`                                           |
| Check package          | `pacman -Qi vmware-workstation`                              |
| Check modules          | `lsmod \| grep vmw`                                          |
| Check VMnet            | `ip link \| grep vmnet`                                      |
| Restart networking     | `sudo systemctl restart vmware-networks.service`             |
| USB service            | `sudo systemctl enable --now vmware-usbarbitrator.service`   |
| Remove VMware          | `sudo pacman -Rns vmware-workstation`                        |

---

# ⭐ Recommended Installation

For a standard Arch Linux installation:

### 1. Update System

```bash
sudo pacman -Syu
```

### 2. Install Git & Build Tools

```bash
sudo pacman -S git base-devel
```

### 3. Install Kernel Headers

```bash
sudo pacman -S linux-headers
```

### 4. Download VMware from AUR

```bash
mkdir -p ~/Downloads
cd ~/Downloads

git clone https://aur.archlinux.org/vmware-workstation.git
```

### 5. Build & Install

```bash
cd vmware-workstation
makepkg -si
```

### 6. Enable VMware Networking

```bash
sudo systemctl enable --now vmware-networks.service
```

### 7. Enable USB Arbitrator

```bash
sudo systemctl enable --now vmware-usbarbitrator.service
```

### 8. Launch VMware

```bash
vmware
```

### 9. Verify Installation

```bash
vmware --version
```

Check VMware modules:

```bash
lsmod | grep -E 'vmmon|vmnet'
```

---

# 🧪 Useful Commands

Check VMware processes:

```bash
ps aux | grep vmware
```

Check VMware services:

```bash
systemctl list-units --type=service | grep vmware
```

Check failed services:

```bash
systemctl --failed
```

Check VMnet interfaces:

```bash
ip addr | grep -A5 vmnet
```

Check routing:

```bash
ip route
```

Check network connectivity:

```bash
ping 8.8.8.8
```

Check DNS:

```bash
ping google.com
```

---

# 📚 Additional Resources

* 🌐 **VMware Workstation Pro — Official Website**
* 📦 **Arch User Repository — VMware Workstation**
* 🐧 **Arch Linux — Official Website**
* 📚 **Arch Linux Wiki — VMware**
* 📚 **Arch Linux Wiki — Kernel Modules**
* 📚 **open-vm-tools Documentation**

---

# 👨‍💻 Author

**Created by Sasha Zimin**

🌐 https://zimin.dev

⭐ If this guide helped you, consider giving the repository a **Star** on GitHub!
