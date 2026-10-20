# IT Home Lab (Project 0: Lab Setup)

## What I Built

I built a home lab on my Windows PC with VirtualBox: two Linux servers (Rocky Linux 9 and Ubuntu Server 26.04) that I use as clean templates for every later project. I control the servers from my PC's PowerShell using SSH keys instead of passwords, and this repo is the index for all my lab projects.

## Tools Used

* VirtualBox 7.2 (virtual machines)
* Rocky Linux 9.8 Minimal (Red Hat family, `dnf`)
* Ubuntu Server 26.04 LTS (Debian family, `apt`)
* OpenSSH with ed25519 keys
* Windows PowerShell
* GitHub

## How It Works

```
Windows PC (PowerShell)
   │  ssh -p 2222 dfulton@127.0.0.1   (key login, no password)
   ▼
VirtualBox NAT port forward  127.0.0.1:2222  ──►  VM port 22
   ▼
TPL-Ubuntu (10.0.2.15)        TPL-Rocky9 (10.0.2.15, separate NAT)
```

* Each VM sits behind VirtualBox NAT, so it can reach the internet but nothing outside can reach it.
* A port forward on my PC's own address (127.0.0.1) lets only my PC open SSH to the VM.
* The VMs are templates. Each later project gets its own clones, so projects never share machines.

## Steps

1. Installed VirtualBox and downloaded the Rocky 9 Minimal and Ubuntu Server ISOs.
2. Created TPL-Rocky9 (2 CPU, 4 GB RAM, 40 GB disk) and installed Rocky Linux 9.8.
3. Created TPL-Ubuntu (2 CPU, 4 GB RAM, 30 GB disk, LVM) and installed Ubuntu Server with OpenSSH.
4. Updated both servers (`sudo dnf update -y`, `sudo apt update \&\& sudo apt upgrade -y`).
5. Practiced the basic commands: `pwd`, `ls`, `cd`, `mkdir`, `touch`, `cat`, `nano`, `sudo`, `ip a`, `ping`.
6. Added a VirtualBox port forward (127.0.0.1:2222 to VM port 22) and logged in with SSH from PowerShell.
7. Created an ed25519 key pair with `ssh-keygen`, copied the public key into `\~/.ssh/authorized\_keys` on the VM, and confirmed login with no password.
8. Created this GitHub repo.

## Screenshots

### P0-01 — VirtualBox With Both VMs

!\[VirtualBox With Both VMs](screenshots/P0-01-virtualbox-vms.png)
VirtualBox Manager shows both template VMs, TPL-Rocky9 and TPL-Ubuntu, built and powered off.

### P0-02 — Rocky Linux First Login

!\[Rocky Linux First Login](screenshots/P0-02-rocky-first-login.png)
Logged into Rocky Linux; `cat /etc/os-release` confirms Rocky Linux 9.8.

### P0-03 — Ubuntu First Login

!\[Ubuntu First Login](screenshots/P0-03-ubuntu-first-login.png)
Logged into Ubuntu Server; `cat /etc/os-release` confirms Ubuntu 26.04 LTS.

### P0-04 — SSH Into the VM From My Computer

!\[SSH Into the VM From My Computer](screenshots/P0-04-ssh-from-host.png)
PowerShell on my PC logs into the Ubuntu VM with an SSH key and no password prompt.

### P0-05 — First GitHub Repo

!\[First GitHub Repo](screenshots/P0-05-first-github-repo.png)
My first public GitHub repo, it-home-lab, created with a README.

## Troubleshooting Tickets

**1. "dfulton is not in the sudoers file" (Rocky)**

* What broke: `sudo dnf update` was refused.
* How I found it: the error named the problem; `groups` showed I wasn't in `wheel`.
* Cause: the "Make this user administrator" box was unticked during install.
* Fix: switched to root with `su -`, ran `usermod -aG wheel dfulton`, logged out and back in.

**2. "No bootable medium found"**

* What broke: the new VM wouldn't start the installer.
* Cause: the ISO wasn't attached to the VM's virtual DVD drive.
* Fix: Devices → Optical Drives → chose the ISO, then Machine → Reset.

**3. "WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!"**

* What broke: SSH from PowerShell refused to connect.
* How I found it: I compared the fingerprint in the warning with the VM's real one (`ssh-keygen -lf /etc/ssh/ssh\_host\_ed25519\_key.pub`). They matched.
* Cause: an older VM had used the same address (127.0.0.1:2222), so my PC remembered a different server's key.
* Fix: removed the old entry with `ssh-keygen -R "\[127.0.0.1]:2222"` and reconnected.

## What I Learned

* How to build and update Linux servers from an ISO in VirtualBox.
* The difference between Red Hat-family (`dnf`, `wheel`) and Debian-family (`apt`, `sudo` group) Linux.
* How NAT and port forwarding let my PC reach a VM.
* Why SSH keys beat passwords, and that the private key never leaves my PC.
* To verify a host fingerprint before trusting it, not just delete the warning.

