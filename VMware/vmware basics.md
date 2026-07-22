### Hypervisor

VSphere  -
- vSphere is the commercial brand name for VMware's entire suite of virtualization products. You don't "install" vSphere on a server; instead, you build a vSphere environment using its core components.
- it's a bundle of ESXi, vCenter, and license features.
- Manages and automates resource pools across multiple physical servers.
    Analogy: The Entire Car (The Suite)

1.  Type-1 ESXi -   Baremetal HyperV 
       - It intercepts hardware resources (CPU, RAM, Storage, Network) and carves them up so you can run multiple isolated Virtual Machines (VMs) on a single physical box.
    - Analogy: If your virtual environment were a car, ESXi is the engine.

---

This cheat sheet is an incredibly helpful map of the entire VMware vCenter ecosystem!
It is divided into logical, bite-sized topics.

<img width="800" height="1200" alt="vmware" src="https://github.com/user-attachments/assets/7a11cf98-05f4-44f0-8cb2-13d5ad0ef310" />

* **ESXi** $\rightarrow$ Bare-Metal Hypervisor
* **vCenter** $\rightarrow$ Management console / Central Brain
* **Datastore** $\rightarrow$ Storage Bucket
* **VMware Tools** $\rightarrow$ Driver Pack
* **DRS** $\rightarrow$ Distributed Resource Scheduler / Automatic Load Balancer
* **HA** $\rightarrow$ High Availability / Crash Protection
* **FT** $\rightarrow$ Fault Tolerance / Zero Downtime
* **vMotion** $\rightarrow$ Live Compute Migration
* **Storage vMotion** $\rightarrow$ Live Storage Migration
* **Thin Provisioning** $\rightarrow$ On-Demand Storage
* **Thick Provisioning** $\rightarrow$ Pre-Allocated Storage
* **Snapshot** $\rightarrow$ Short-Term Save State
* **Template** $\rightarrow$ Master Cookie-Cutter
* **vSS** $\rightarrow$ Local Switch
* **vDS** $\rightarrow$ Global Switch
* **Maintenance Mode** $\rightarrow$ Evacuate & Service
* **Resource Pool** $\rightarrow$ Resource Slicer
---

## Part 1: The Core Infrastructure & Basics

### 1. VMware Infrastructure

Think of this as the family tree of your virtual world:

* **Datacenter:** The highest boundary (like a physical building or location).
* **Cluster:** A group of physical servers pooled together to share their processing power.
* **ESXi Hosts:** The actual physical servers running the virtualization engine.
* **Virtual Machines (VMs):** The virtual computers running inside those physical servers.

### 2. Login & Daily Health Check

How to check if your environment is healthy. You log in via a web browser (`https://vcenter-fqdn/ui`). You look for green checkmarks on **vCenter status, host status, storage usage, and backup status** to ensure nothing is failing.

### 3. ESXi Host Health

How to check the vitals of a single physical server (the Host). You monitor its:

* **Hardware Sensors:** Are the fans spinning? Is the power supply unit (PSU) working? Is it too hot?
* **Resource Usage:** How much CPU, Memory, and Storage is it currently using?

### 4. VM Health

How to check if a specific virtual computer is running well. Key terms to know:

* **CPU Ready:** The amount of time a VM is waiting for physical CPU power. High "CPU Ready" means your host server is overloaded.
* **Memory Ballooning:** A trick where VMware reclaims unused memory from one VM to give it to another that needs it urgently.

### 5. Snapshot Management

A **Snapshot** is like a "save game" state. You take one right before doing something risky (like updating software).

* **Rule of Thumb:** Never keep snapshots for more than 72 hours, and never use them as permanent backups, because they slow down your system over time.

---

## Part 2: VM Lifecycle (Clones, Templates, and Migrations)

### 6. Clone VM

Making an exact copy of an existing Virtual Machine.

* **Thin Provision:** The copy only takes up as much disk space as it is actually using (e.g., a 40GB drive with 10GB of files only takes up 10GB of physical disk).
* **Thick Provision:** The copy immediately locks down and claims the entire allocated space (e.g., it immediately takes up 40GB of physical disk).

### 7. Template

A "master copy" of a configured OS (like Windows Server or Ubuntu) used to stamp out dozens of identical new virtual machines quickly.

### 8. VM Migration

Moving virtual machines around:

* **Cold Migration:** Moving a VM while it is turned off.
* **vMotion:** Moving a *running* VM from one physical server to another with **zero downtime**.
* **Storage vMotion:** Moving a VM's virtual hard drive from one physical storage disk to another while it is still running.
* **Enhanced vMotion:** Moving both the host and the storage at the same time.

---

## Part 3: Advanced Clustering Features (The Magic of VMware)

### 9. DRS (Distributed Resource Scheduler)

An automatic load balancer. If Server A gets too busy and Server B is idle, DRS automatically uses **vMotion** to slide some VMs over to Server B so everything runs smoothly.

### 10. HA (High Availability)

The safety net. If a physical server suddenly dies (unplugs or breaks), HA detects the crash and automatically reboots those lost VMs on a different, healthy physical server in the cluster.

### 11. FT (Fault Tolerance)

For ultra-critical systems that can never go down, even for a second. FT runs a "shadow" secondary VM in lockstep on a different server. If the primary VM fails, the secondary takes over instantly with **zero loss of connection**.

### 12. Resource Pool

A way to slice up your CPU and RAM. For example, you can create a "Production" pool with guaranteed high-performance resources and a "Testing" pool with limited, low-priority resources.

### 13. Datastore

A storage container where your VM files (like their virtual hard drives) live. These can be local drives, or shared over a network (using protocols like NFS, iSCSI, or vSAN).

---

## Part 4: Networking & Daily Operations

### 14. Storage Policies

Allows you to categorize your storage into tiers (e.g., **Gold** for fast SSDs, **Bronze** for slower hard drives) and assign VMs to them automatically based on priority.

### 15. Networking

Connecting your VMs to the outside world.

* **Standard Switch (vSS):** A virtual switch configured on a single host.
* **Distributed Switch (vDS):** A single virtual switch that stretches across all your hosts, making management much simpler.

### 16. VM Network Troubleshooting

Standard tools to use if a VM cannot connect to the internet or local network:

* Check if the virtual cable is "Connected."
* Ensure it's on the correct **VLAN** or **Port Group**.
* Run classic command line tools like `ping`, `ipconfig` (Windows), or `ifconfig` (Linux).

### 17. ESXi Maintenance Mode

Used when you need to physically repair or update a host server. Entering Maintenance Mode safely evacuates (migrates) all running VMs to other servers in the cluster first so you don't disrupt users.

### 18 & 19. Add / Remove Host

How to scale your infrastructure. You can add more physical servers to your vCenter by pointing to their IP address and entering the root password, or safely remove old ones.

### 20. VM Provisiong

### 21. VMware Tools

A tiny, highly critical package of drivers you install *inside* the Guest OS. It optimizes mouse movements, screen resolution, clock synchronization, and network performance.

### 22. Host Profiles
---

## Part 5: Administration, Backups, & Troubleshooting

### 23. Permissions

Assigning roles to users (e.g., an **Administrator** can change everything, a **VM User** can only turn virtual machines on and off).

### 25. Backup vCenter

vCenter is the brains of your system. You back it up using its management page (known as the VAMI at port `:5480`) and send those backups to secure network storage (like SFTP or SMB).

### 26. Certificates

### 27. Logs

Where to look when things break:

* **ESXi Logs:** Stored locally on the host `/var/log/` (e.g., `vmkernel.log` for kernel errors).
* **vCenter Logs:** Stored in `/var/log/vmware/` (e.g., `vpxd.log` for vCenter connection errors).

### 28. Useful ESXi Commands

Commands you type directly into the ESXi command-line interface:

* `vmware -v`: Check your current ESXi version.
* `esxcli storage filesystem list`: Check your connected hard drives and storage.

### 29. Common Troubleshooting Guide

A quick cheat sheet of what to check when common errors occur (like "Host Not Responding" or a "vMotion Failed").

### 30. Best Practices

The "golden rules" for keeping your infrastructure running smoothly:

* Keep VMware Tools updated.
* Monitor datastore usage (always keep at least 20% free space!).
* Follow the 3-2-1 backup strategy (3 copies, 2 different media types, 1 offsite).
