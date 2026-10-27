### Raspberry Pi 5 Router-on-a-Stick Configuration

#### 1. VLAN Sub-Interfaces

Create VLAN sub-interfaces on the Raspberry Pi 5 to handle traffic for each VLAN:

```bash
sudo ip link add link eth0 name eth0.10 type vlan id 10
sudo ip link add link eth0 name eth0.20 type vlan id 20
sudo ip link add link eth0 name eth0.30 type vlan id 30
sudo ip link add link eth0 name eth0.99 type vlan id 99

sudo ip addr add 10.10.1.2/24 dev eth0.10
sudo ip addr add 10.10.2.254/24 dev eth0.20
sudo ip addr add 10.10.3.254/24 dev eth0.30
sudo ip addr add 10.10.4.254/24 dev eth0.99

sudo ip link set dev eth0.10 up
sudo ip link set dev eth0.20 up
sudo ip link set dev eth0.30 up
sudo ip link set dev eth0.99 up
```

This configures the Raspberry Pi 5 to act as a router-on-a-stick, handling traffic for each VLAN with its own sub-interface.

#### 2. Enable IP Forwarding

Enable IP forwarding to allow the Raspberry Pi 5 to route traffic between VLANs:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

This command enables IP forwarding, allowing the Raspberry Pi 5 to act as a router.

#### 3. nftables Rules

Configure nftables rules to control traffic between VLANs:

```bash
sudo nft add table ip4 filter
sudo nft add chain ip4 filter input {
    type filter hook input priority 0;
    policy accept;
}

# Allow management VLAN to reach everything
sudo nft add rule ip4 filter input ip saddr 10.10.1.0/24 counter accept

# Allow users VLAN to reach servers on ports 80/443
sudo nft add rule ip4 filter input ip saddr 10.10.2.0/24 counter accept tcp dport 80
sudo nft add rule ip4 filter input ip saddr 10.10.2.0/24 counter accept tcp dport 443

# Allow quarantine VLAN to reach nothing
sudo nft add rule ip4 filter input ip saddr 10.10.4.0/24 counter drop

# Allow all VLANs to reach management VLAN
sudo nft add rule ip4 filter input ip daddr 10.10.1.0/24 counter accept
```

These rules ensure that:
- The management VLAN can reach everything.
- The users VLAN can only reach servers on ports 80/443.
- The quarantine VLAN can reach nothing.
- All VLANs can reach the management VLAN.

This configuration provides a secure and controlled network environment for the VLANs.
