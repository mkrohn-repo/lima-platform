# Lima Platform (Apple Silicon VM Automation)

Lima configs for running Linux VMs on macOS (Apple Silicon), serving as a base for Platform/SRE workflows and future Kubernetes clusters.

## Why Lima?
- **Vms without the bloat** on macOS, without full hypervisor UX
- **Scriptable + reproducible** VM creation
- **K8s-ready** foundation (multi-VM, custom networks, cloud-init)

## Building you lima environment

> Requires: macOS 14+, Apple Silicon, Homebrew, Lima ≥ 1.2.x, and socket_vmnet

> **Tested on:** Apple M3 Pro (12 cores, 36 GB RAM)

## Usage 

Clone:

```bash
git clone https://github.com/mkrohn-repo/lima-platform.git
#or
git clone git@github.com:mkrohn-repo/lima-platform.git
```

```bash
brew install lima
limactl -v  # verify
```

### Installing socket_vmnet for network management (what worked for me).
```bash
git clone https://github.com/lima-vm/socket_vmnet
cd socket_vmnet
make
sudo make PREFIX=/opt/socket_vmnet install.bin
limactl sudoers > etc_sudoers.d_lima
sudo install -o root etc_sudoers.d_lima /etc/sudoers.d/lima
rm etc_sudoers.d_lima
```

## Spin up a VM
``` bash
#run from repo root

limactl create --name vm-dhcp-bootp ./lima-platform/poc/vm-dhcp-bootp.yml -y
# remove -y if you want to edit the configuration interactively
limactl start vm-dhcp-bootp
limactl shell vm-dhcp-bootp
```
## stop or delete a vm
``` bash
limactl stop vm-dhcp-bootp
limactl delete vm-dhcp-bootp
limactl delete vm-dhcp-bootp --force
```
## Roadmap
- Automate creation of a 3-node cluster (kubeadm-ready) using Ansible + Make to tie it together

## References
- [Lima documentation](https://github.com/lima-vm/lima)  
- [socket_vmnet (network helper)](https://github.com/lima-vm/socket_vmnet)
- [Ubuntu Cloud Images (ARM64)](https://cloud-images.ubuntu.com/)