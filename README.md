# NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP
# 🔐 Cybersecurity Lab Setup — VirtualBox + Kali Linux

This repo documents how I set up my own isolated cybersecurity lab using VirtualBox and Kali Linux, as part of Week 1 of the Networkwalks Cybersecurity program.

Nothing fancy here — just VirtualBox, a NAT Network, and a Kali VM sitting on it. But I ran into a couple of annoying issues along the way (mainly around VirtualBox hiding tools and BIOS virtualization errors), so I wanted to write those down properly in case anyone else in the batch hits the same wall.

**Author:** Narugopal Sing | **Batch:** B083, Networkwalks

---

## 🤔 Why I built this

Before jumping into actual pentesting tools and techniques, we needed a safe place to practice — somewhere isolated from my real home network where I could scan, poke around, and eventually break things without worrying about actually breaking anything important.

So the goal was simple: get Kali Linux running inside VirtualBox, on its own private network, with an IP I control. Later on, I'll probably add more VMs to this same network to use as targets.

*A quick note before anything else — this lab is strictly for learning and for testing systems I own or have permission to test. Not pointing any of this at external systems.*

---

## 💻 My Hardware & Lab Architecture

Running multiple VMs requires solid hardware, so I built this lab directly on my main daily driver. 

* **Host Machine:** HP Victus 15
* **CPU:** Intel Core i5-14450HX (Provides plenty of cores to split between Windows and my virtual machines).
* **RAM:** 24GB DDR5 (This is a lifesaver. It allows me to easily dedicate 4GB+ to my Kali machine without my Windows 11 host lagging at all).
* **Storage:** 512GB NVMe SSD (Keeps boot times fast).
* **Hypervisor:** Oracle VirtualBox 7.2
* **Security OS:** Kali Linux 2026.2

### 🌐 The Virtual Network Configuration
I needed a network where my future "Attacker" and "Target" machines could talk to each other, but my home router couldn't reach inside. 

| Component | Configuration |
| :--- | :--- |
| **Network Type** | NAT Network (Not standard NAT!) |
| **Network Name** | `NatNetwork` |
| **Subnet** | `10.0.0.0/24` |
| **Kali's IP** | `10.0.0.2/24` (Static) |
| **Gateway** | `10.0.0.1` |
| **DNS** | `8.8.8.8` |

> **Note:** I am intentionally leaving IPs `10.0.0.3` through `10.0.0.99` free. As the bootcamp progresses, I will deploy vulnerable target VMs in this range.

---

## 🪜 Step-by-Step Setup Process

Here is exactly how I built the lab from the ground up:

### Step 1: Installed 7-Zip (Step Zero)
Kali Linux virtual machine files often come compressed as .7z archives. Step zero was simply installing 7-Zip so I could actually extract the downloaded VM package.

### Step 2: Installing VirtualBox
I went with VirtualBox 7.2 as my hypervisor. The installation was straightforward on Windows 11.

### Step 3: Created the NAT Network
Went with:
* **Network Name:** `NatNetwork`
* **IPv4 Prefix:** `10.0.0.0/24`
* **DHCP:** Enabled

### Step 4: Imported Kali Linux
Downloaded the Kali VM from the official site and imported it into VirtualBox. I allocated **4096 MB (4GB) of RAM** to keep things smooth, and set the network adapter like this:
* **Attached to:** NAT Network
* **Network:** `NatNetwork`

### Step 5: Set a Static IP on Kali
By default, Kali just grabs whatever DHCP hands it. I didn't want to keep typing `ip a` every time I booted the machine just to find my IP, so I set it manually using Kali's Network Manager:
* **Address:** `10.0.0.2`
* **Netmask:** `24`
* **Gateway:** `10.0.0.1`

### Step 6: Took a "Clean" Snapshot
Once everything looked good, I took a VirtualBox snapshot called **"Clean Kali - Network Setup"**. If I mess something up in later labs (which is highly likely), I can just roll back to this instead of reinstalling the whole OS. Cheap insurance!

---

## 🔎 Verification & Testing

To prove the lab actually works, I ran a series of pre-flight checks from my Kali terminal:

| Check | Command Ran | What I Expected (and Got) |
| :--- | :--- | :--- |
| **IP is set correctly** | `ip a` | Showed `10.0.0.2/24` |
| **Gateway reachable** | `ping -c 4 10.0.0.1` | Successful replies |
| **Internet works** | `ping -c 4 8.8.8.8` | Successful replies |
| **DNS resolves** | `nslookup networkwalks.com` | Domain resolved fine |
| **Nmap installed** | `nmap --version` | Showed the Nmap version |

---

## 🐞 Problems I Ran Into (And How I Fixed Them)

No IT project goes perfectly on the first try. Here are the walls I hit:

### Problem 1: VirtualBox wouldn't show me the Network tool
Right after installing VirtualBox, I went looking for the Network option under Tools to set up my NAT Network — and it wasn't there! 
* **The Fix:** I realized VirtualBox defaulted to **Basic Mode**. I went into Preferences, switched the Experience Mode to **Expert**, and restarted. Boom, the Network tool appeared in the sidebar.

### Problem 2: Internet dropped after setting my Static IP
After I manually configured my static IP (`10.0.0.2`), I completely lost outbound internet connectivity. It turns out this can happen due to how Kali's NetworkManager handles Duplicate Address Detection (DAD).
* **The Fix:** I opened the terminal and ran: 
  `sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0`
  After restarting the network connection, my internet came right back.

### Problem 3: The VT-x BIOS Error
When I first tried to boot Kali, it crashed with a hardware virtualization error.
* **The Fix:** My HP Victus had Intel VT-x disabled by default. I restarted the laptop, mashed the F10 key to enter the BIOS, enabled virtualization, saved, and rebooted. Smooth sailing after that.

---

## 💡 What I Learned
Writing all this down actually helped me understand what I was clicking. 
1. **Basic vs Expert Mode matters:** That one popup during install quietly decides what GUI tools you can even see.
2. **NAT Network ≠ regular NAT:** I now understand how to route multiple VMs together on a private virtual switch.
3. **Static IPs make life easier:** No more guessing my IP address.
4. **Documentation isn't busywork:** Documenting the `nmcli` fix means I won't have to spend an hour Googling it if it happens again.

---

## 🔗 Tools used:
* **7-Zip:** https://7-zip.org/
* **VirtualBox:** https://virtualbox.org/wiki/Downloads
* **Kali Linux:** https://kali.org/get-kali

---

## 👤 Author

**Narugopal Sing**

**LinkedIn:** 

---

## 📌 Project Information

**Program Name:** Cybersecurity at Networkwalks | **Week:** 01 | **Project:** Cybersecurity & Pentesting Lab Setup |**Repository:** GitHub
