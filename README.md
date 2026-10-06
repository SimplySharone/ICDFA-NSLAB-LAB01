# ICDFA-NSLAB-LAB01
# Laboratory 1: Network Infrastructure and Firewall Configuration

## Part A & B: VirtualBox & Firewall Initialization
### Evidence E1: Firewall Network Configuration
*Description: VirtualBox network properties for the firewall appliance showing Adapter 1 on NAT and Adapter 2 attached to the Internal Network named 'ICDFA-LAN'.*
![Evidence E1](path/to/your/e1_screenshot.png)

### Evidence E2: Client Network Configuration
*Description: VirtualBox network properties for the Ubuntu workstation showing Adapter 1 attached to the Internal Network named 'ICDFA-LAN'.*
![Evidence E2](path/to/your/e2_screenshot.png)

### Evidence E3: OPNsense Console Status
*Description: Active OPNsense text console interface mapping showing LAN mapped to em0 (10.10.10.1/24) and WAN mapped to em1.*
![Evidence E3](path/to/your/e3_screenshot.png)

---

## Part C: Workstation Interface Verification
### Evidence E4: Ubuntu Terminal Network Settings
*Description: Terminal execution showing the output of 'ip -4 -br address' and 'ip route' proving successful lease acquisition.*
![Evidence E4](path/to/your/e4_screenshot.png)

---

## Part D & E: Connectivity Testing & Traffic Analysis
### Evidence E5: End-to-End Connectivity Suites
*Description: Execution block proving successful reachability to the local gateway, external loopbacks, and protocol verification.*
![Evidence E5](path/to/your/e5_screenshot.png)

### Evidence E6: Wireshark Packet Captures
#### ARP Sequence
![ARP Packet](path/to/your/e6_arp.png)

#### ICMP Sequence
![ICMP Packet](path/to/your/e6_icmp.png)

#### DNS Sequence
![DNS Packet](path/to/your/e6_dns.png)

---

## Part F: Analytical Review & Completion Questions

### Evidence E7: Default Gateway Analysis
The Ubuntu workstation (`icdfa-nslab-client-v1`) uses `10.10.10.1` as its default gateway because the OPNsense firewall acts as the intermediate boundary router between the isolated local network (`ICDFA-LAN`) and external networks. Any network traffic destined for IP addresses outside the local `10.10.10.0/24` subnet (such as public internet addresses) cannot be delivered directly, so the client forwards these packets to the firewall's LAN interface to be appropriately routed and translated via Outbound NAT.

### Completion Questions

**1. What is the difference between the OPNsense WAN and LAN interfaces?**
The WAN (Wide Area Network) interface is the untrusted boundary side facing the external public network or hypervisor NAT engine, configured dynamically via DHCP to route traffic outbound to the internet. The LAN (Local Area Network) interface is the private, trusted side facing the internal protected host network, using a static IP configuration (`10.10.10.1`) to serve as the default gateway and local DHCP server for internal clients.

**2. Why must both internal adapters use the same VirtualBox network name?**
Both adapters must utilize the exact same case-sensitive identifier (`ICDFA-LAN`) because VirtualBox leverages this unique label to dynamically provision a shared software-defined virtual switch backplane. Any naming mismatch isolates the virtual adapters onto entirely different virtual broadcast domains, breaking connectivity at the physical layer and blocking packet transit.

**3. What information does the default route provide to Ubuntu?**
The default route serves as the fallback structural routing directive (gateway of last resort) telling the host operating system exactly where to forward network traffic when the destination IP address does not match any explicit subnet paths available inside its local routing table (such as traffic destined for external public web services).

**4. Which packet exchange allows Ubuntu to learn the firewall MAC address?**
The Address Resolution Protocol (ARP) packet exchange enables this discovery. The client broadcasts an ARP Request query ("Who has 10.10.10.1?"), and the firewall responds with a unicast ARP Reply defining its physical hardware MAC address, allowing the client to encapsulate frames correctly.

**5. Why does a successful ping to 1.1.1.1 not automatically prove that DNS is working?**
A ping to `1.1.1.1` strictly validates Layer 3 network routing, ICMP translation, and outbound NAT traversal using a direct numerical destination address. It completely bypasses the Layer 7 Domain Name System (DNS) parsing architecture responsible for resolving alphabetic domain records (like `opnsense.org`) into usable machine-readable IP addresses.
