### VLAN Lab Test Checklist

#### 1. Basic Connectivity Tests

- **Ping from Management VLAN to all other VLANs**:
  - `ping 10.10.2.254` (Users VLAN)
  - `ping 10.10.3.254` (Servers VLAN)
  - `ping 10.10.4.254` (Quarantine VLAN)
  - **Expected Result**: All pings should succeed as Management VLAN can reach everything.

- **Ping from Users VLAN to Servers VLAN**:
  - `ping 10.10.3.254`
  - **Expected Result**: Ping should succeed as Users VLAN can reach Servers VLAN on ports 80/443.

- **Ping from Users VLAN to Quarantine VLAN**:
  - `ping 10.10.4.254`
  - **Expected Result**: Ping should fail as Users VLAN cannot reach Quarantine VLAN.

- **Ping from Quarantine VLAN to all other VLANs**:
  - `ping 10.10.1.2` (Management VLAN)
  - `ping 10.10.2.254` (Users VLAN)
  - `ping 10.10.3.254` (Servers VLAN)
  - **Expected Result**: All pings should fail as Quarantine VLAN can reach nothing.

#### 2. VLAN and Trunking Verification

- **Check VLAN configuration on the Cisco switch**:
  - `show vlan brief`
  - **Expected Result**: VLANs 10, 20, 30, and 99 should be listed with their respective names.

- **Check trunking configuration on the Cisco switch**:
  - `show interfaces trunk`
  - **Expected Result**: Port 1 should be a trunk port allowing VLANs 10, 20, 30, and 99.

- **Check VLAN configuration on the Raspberry Pi router**:
  - `ip link show`
  - **Expected Result**: VLAN sub-interfaces eth0.10, eth0.20, eth0.30, and eth0.99 should be listed.

- **Check IP addresses on the Raspberry Pi router**:
  - `ip addr`
  - **Expected Result**: Each VLAN sub-interface should have the correct IP address and subnet mask.

- **Check IP forwarding status on the Raspberry Pi router**:
  - `sysctl -a | grep net.ipv4.ip_forward`
  - **Expected Result**: `net.ipv4.ip_forward = 1` should be displayed, indicating IP forwarding is enabled.

- **Check nftables rules on the Raspberry Pi router**:
  - `sudo nft list ruleset`
  - **Expected Result**: The nftables rules should be listed, showing the allowed traffic between VLANs as per the configuration.

#### 3. Troubleshooting Commands

- **Check routing table on the Raspberry Pi router**:
  - `ip route`
  - **Expected Result**: The routing table should show routes for all VLAN sub-interfaces.

- **Check ARP table on the Raspberry Pi router**:
  - `arp -a`
  - **Expected Result**: The ARP table should show entries for all VLAN sub-interfaces.

- **Check system logs on the Raspberry Pi router**:
  - `journalctl -b`
  - **Expected Result**: System logs should show no critical errors related to networking or routing.

This checklist ensures that all aspects of the VLAN lab configuration are verified and working as expected.
