# High Availability Setup with Keepalived and Vagrant

This repository contains a `Vagrantfile` that sets up a High Availability (HA) environment using Keepalived. The setup includes two virtual machines (VMs): one serving as the primary (MASTER) and the other as the backup (BACKUP). A floating IP address is configured, which will automatically failover to the backup VM in case of a failure in the primary VM.

## Prerequisites

- [Vagrant](https://www.vagrantup.com/downloads.html) installed on your machine.
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or any other compatible virtualization platform.

## Getting Started

1. Clone this repository to your local machine.
   ```bash
   git clone https://github.com/wozniakpl/ha-keepalived-vagrant
   cd ha-keepalived-vagrant
   ```

2. Start the Vagrant environment.
   ```bash
   vagrant up
   ```

3. Once the VMs are up and provisioned, you can SSH into each VM using:
   ```bash
   vagrant ssh master
   vagrant ssh backup
   ```

## Testing the Setup

1. Verify that the floating IP is reachable.
   ```bash
   ping 192.168.56.200
   ```

2. Test failover by stopping Keepalived on the master VM, and verify the backup VM takes over the floating IP.
   ```bash
   vagrant ssh master
   sudo tcpdump -i enp0s8 icmp # observe the traffic
   sudo systemctl stop keepalived
   sudo tcpdump -i enp0s8 icmp # no traffic should be observed
   ```

3. Check the traffic on the backup VM for messages indicating it has taken over the MASTER role.
   ```bash
   vagrant ssh backup
   sudo tcpdump -i enp0s8 icmp # observe the traffic
   ```

4. Restart Keepalived on the master VM to test failback.
   ```bash
   vagrant ssh master
   sudo tcpdump -i enp0s8 icmp # no traffic should be observed
   sudo systemctl start keepalived
   sudo tcpdump -i enp0s8 icmp # observe the traffic
   ```

5. Check the traffic on the backup VM for messages indicating it has taken over the BACKUP role.
   ```bash
   vagrant ssh backup
   sudo tcpdump -i enp0s8 icmp # no traffic should be observed
   ```

## Cleanup

- You can shut down the VMs with:
  ```bash
  vagrant halt
  ```

- Or destroy them with:
  ```bash
  vagrant destroy
  ```
