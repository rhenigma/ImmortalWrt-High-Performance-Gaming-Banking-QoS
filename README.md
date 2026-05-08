# ImmortalWrt-High-Performance-Gaming-Banking-QoS
Optimized for Unifi (Malaysia) | Hardware: ImmortalWrt v23.xx+
This repository documents a custom firewall and QoS configuration designed to achieve 0.00 Load Average while maintaining ultra-low latency for Gaming, VoIP, and Financial Transactions.
🛠️ Project Goals

    Zero-Delay Banking: Prioritize TLS handshakes and ACKs via mangle table tagging.

    Gaming Priority: Behavioral-based prioritization (packet size/burst) to avoid "bufferbloat."

    Social Media Optimization: Fast-tracking TikTok/FB/YouTube flows via Hardware Offloading.

    System Stability: Clean, modular configuration using nftables.
🏗️ Technical Architecture

The traffic is processed through three specific layers to ensure "VIP" treatment for critical data.
1. The Mangle Layer (Pre-Routing/Post-Routing)

Intercepts packets at the earliest/latest stages to apply DSCP ef (Expedited Forwarding) tags.

    Target: Banking portals (443, 8080, 8443) and small control packets.

2. The Forwarding Layer (QoS Logic)

Sorts traffic based on behavior rather than just IP.

    Mobile Gaming Burst: Identifies UDP packets between 40-512 bytes (typical for game state updates).

    ACK Prioritization: Ensures the "Brain" of the TCP connection isn't throttled.

3. Hardware Flow Offload

Shifts high-bandwidth streams (Streaming/Downloads) directly to the networking silicon.
📜 Configuration Snippet (/etc/firewall.user.d/)

    Note: Critical credentials such as PPPoE User/Pass are omitted for security.

Bash

# Banking Priority Logic
define banking_ports = { 443, 8443, 8080 }
add rule inet fw4 mangle_postrouting tcp dport $banking_ports tcp flags ack ip length < 128 counter ip dscp set ef comment "Unifi: Fast-Track Banking ACKs"

# Gaming & Social Prioritization
add rule inet fw4 forward_wan udp length 40-512 counter accept comment "Unifi: Mobile Gaming Burst"
add rule inet fw4 forward_wan tcp flags ack counter accept comment "Unifi: Prioritize ACKs"
add rule inet fw4 forward_wan ct state established,related flow add @ft comment "Unifi: HW Flow Offload"

📅 Maintenance Changelog
Day 1: Initial Deployment

    Timestamp: 2026-05-06 11:00 MYT

    Status: Initial firewall logic deployed.

    Observation: Basic gaming rules (Handle 107) and Social priority rules implemented.

    Performance: Load average stabilized at 0.00.

Day 2: The "Banking" Breakthrough

    Timestamp: 2026-05-07 14:30 MYT

    Status: Syntax errors in mangle_postrouting resolved.

    Optimization: Added "Fast-Track Banking ACKs" (Handle 127) using ip length < 128.

    Result: Captured 113,000+ packets in first 4 hours; significant improvement in UI snappiness.

Day 3: Long-Term Stability Check

    Timestamp: 2026-05-08 13:49 MYT

    Status: Uptime reached 2 days, 2 hours.

    Stats:

        Handle 127 (Banking): 330,541 packets (Processed).

        Handle 107 (Gaming): 573 packets (Background sync).

        CPU Load: 0.03 (Near idle).

    Verdict: System is rock solid. No packet loss or kernel memory leaks reported.

📈 Live Monitoring Tools

To view your firewall in real-time without external packages:
Bash

while true; do 
    clear; 
    echo "--- LIVE FIREWALL DATA ---";
    nft -a list chain inet fw4 mangle_postrouting | grep "Unifi:";
    nft -a list chain inet fw4 forward_wan | grep "Unifi:";
    sleep 2; 
done

🛡️ Security Redaction Policy

    [REDACTED] PPPoE Username

    [REDACTED] PPPoE Password

    [REDACTED] Local IP Assignments for Private Devices

Next Steps: Planning to perform a live gaming stress test (MLBB/PUBG) to document packet handling under high congestion.
