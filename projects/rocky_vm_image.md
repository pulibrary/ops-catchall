### Create a Rocky Linux Virtual Machine

**Don't Panic!**

#### Prerequisites to Install Ubuntu on VMware

  * You will need an Intel amd64 based hardware for the rest of this document.
  * Install [Ubuntu Desktop](https://ubuntu.com/download/desktop)

#### Download qemu and the Ubuntu ISO File

  * Get and install QEMU and virt-manager
  ```bash
  sudo apt -y install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
  ```
  * Download the [Ubuntu LTS](https://ubuntu.com/download/server) iso file (Use Jammy (22.04))



