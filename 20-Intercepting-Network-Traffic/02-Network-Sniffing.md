# Module 20: Intercepting Network Traffic - Network Sniffing

## What is Network Sniffing?

Network sniffing means capturing packets on a network without being the intended recipient. On Wi-Fi networks, a sniffer can see all traffic.

## Passive vs Active Sniffing

### Passive Sniffing

Listening to network traffic without interfering.

**How it works:**
The sniffer listens to all packets on the network. In a switched network, they only see their own traffic unless the switch is configured to mirror ports.

### Active Sniffing

Interfering with the network to redirect traffic.

**Techniques:**
- ARP spoofing
- DNS spoofing
- Rogue access points

## ARP Spoofing

ARP spoofing tricks devices into sending traffic to the attacker's machine instead of the intended destination.

**How it works:**
```
Victim thinks:    Gateway is at MAC: AA:BB:CC:DD:EE:FF
Attacker sends:   Gateway is at MAC: FF:EE:DD:CC:BB:AA
Victim now sends traffic to attacker instead of real gateway
```

**Tools:** Ettercap, Bettercap, arpspoof

**On Kali:**
```
ettercap -T -M arp:remote /192.168.1.100// /192.168.1.1//
```

## Sniffing on Mobile Networks

**Testing environment:**
1. Connect mobile device to a controlled Wi-Fi
2. Kali Linux is on the same network
3. Use ARP spoofing if needed
4. Capture all traffic

## Detection and Prevention

| Protection | How It Works |
|------------|--------------|
| HTTPS | Encrypts traffic, sniffing reveals only encrypted data |
| SSL Pinning | Prevents MITM even on sniffed networks |
| VPN | Encrypts all traffic before it leaves the device |
| Certificate validation | Ensures connection is to the real server |

## Summary

Network sniffing captures traffic on the network. Active sniffing (ARP spoofing) redirects traffic through the attacker. HTTPS encryption protects against sniffing. Use controlled test environments for mobile testing.