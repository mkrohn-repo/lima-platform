# Networks in Lima VMs

When you run Lima with the `vz` virtualization type on macOS, networking works a little differently than with `qemu`.

______________________________________________________________________

## Local `vz` Virtualization

- Lima uses Apple’s [Virtualization.framework](https://developer.apple.com/documentation/virtualization) (`vmType: vz`) to run the VM.
- With `vz`, the VM’s network interface is automatically bridged into a **local DHCP service** provided by Lima.
- This means the guest VM does not have a static IP baked in — it requests one via DHCP at boot.

______________________________________________________________________

## DHCP and MAC Addresses

- Each VM must have a **stable MAC address** so that DHCP will always hand back the **same IP**.
- In our setup, the MAC addresses in the YAML (`de:ad:be:ef:00:01`, etc.) line up with the entries in `/etc/bootptab`.
- This ensures that:
  - The VM always comes up with the same IP address.
  - Other nodes (and your host) can reliably connect to it by that IP or by the hostname we assign.

______________________________________________________________________

## Why `qemu-guest-agent` is needed

- The package [**`qemu-guest-agent`**](https://www.qemu.org/docs/master/interop/qemu-ga.html) is installed in provisioning.
- Even though we are using `vz`, Lima relies on the guest agent to retrieve runtime details like the **assigned IP address**.
- Without it, Lima cannot easily report the VM’s IP back to the host, which breaks convenience features like `limactl shell` or quick SSH access.

______________________________________________________________________

## Summary

- `vz` VMs get their IP via DHCP from Lima’s internal network service.
- MAC addresses are fixed in the config so DHCP gives predictable IPs, matching `/etc/bootptab`.
- `qemu-guest-agent` is required so Lima can query and return the current IP of the VM to the host.
