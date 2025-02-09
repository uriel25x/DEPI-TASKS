# **Virtual Machine Ubuntu Server Setup**

**This guide provides step-by-step instructions for setting up a virtual machine with Ubuntu Server, connecting it via SSH, and configuring disk management using LVM2.**

## **Table of Contents**

- [**Prerequisites**](#prerequisites)
- [**Virtual Machine Setup**](#virtual-machine-setup)
- [**SSH Connection**](#ssh-connection)
- [**Installing LVM2**](#installing-lvm2)
- [**Disk Partitioning and Mounting**](#disk-partitioning-and-mounting)
- [**Creating and Enabling Swap**](#creating-and-enabling-swap)

## **Prerequisites**

- **A host machine running Ubuntu**
- **Virtualization software such as VirtualBox or VMware**
- **Ubuntu Server ISO image**

## **Virtual Machine Setup**

1. **Create a new virtual machine:**
   - **Allocate at least 2GB of RAM and 20GB of disk space.**
   - **Attach the Ubuntu Server ISO.**
2. **Start the VM and follow the Ubuntu Server installation steps.**
3. **Configure network settings to allow SSH connections (enable NAT or Bridged Adapter).**
4. **Complete the installation and reboot the VM.**

## **SSH Connection**

1. **On the VM, install the OpenSSH server:**

   ```bash
   sudo apt update
   sudo apt install openssh-server
   ```

2. **Check the SSH service status:**

   ```bash
   sudo systemctl status ssh
   ```

3. **Find the VM's IP address:**

   ```bash
   ip a
   ```

4. **From the host machine, connect via SSH:**

   ```bash
   ssh username@vm-ip-address
   ```

## **Installing LVM2**

1. **Update package lists and install LVM2:**

   ```bash
   sudo apt update
   sudo apt install lvm2
   ```

2. **Verify installation:**

   ```bash
   sudo lvmdiskscan
   ```

## **Disk Partitioning and Mounting**

1. **Identify available disks:**

   ```bash
   sudo fdisk -l
   ```

2. **Create a new partition:**

   ```bash
   sudo fdisk /dev/sdX
   ```

   **Follow prompts to create a primary partition.**

3. **Format the partition:**

   ```bash
   sudo mkfs.ext4 /dev/sdX1
   ```

4. **Create a mount point:**

   ```bash
   sudo mkdir /mnt/newdisk
   ```

5. **Mount the partition:**

   ```bash
   sudo mount /dev/sdX1 /mnt/newdisk
   ```

6. **Verify the mount:**

   ```bash
   df -h
   ```

## **Creating and Enabling Swap**

1. **Create a swap file (2GB example):**

   ```bash
   sudo fallocate -l 2G /swapfile
   ```

2. **Secure the swap file:**

   ```bash
   sudo chmod 600 /swapfile
   ```

3. **Set up the swap area:**

   ```bash
   sudo mkswap /swapfile
   ```

4. **Enable the swap file:**

   ```bash
   sudo swapon /swapfile
   ```

5. **Verify swap status:**

   ```bash
   sudo swapon --show
   ```

6. **Make the swap permanent by adding it to **``**:**

   ```bash
   echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
   ```

**This completes the task of setting up a**n Ubuntu Server VM, configuring SSH, and managing disks with LVM2.

