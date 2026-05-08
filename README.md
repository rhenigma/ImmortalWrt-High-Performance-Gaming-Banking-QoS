# ImmortalWrt-High-Performance-Gaming-Banking-QoS
Optimized for Unifi (Malaysia) | Hardware: ImmortalWrt v23.xx+
This repository documents a custom firewall and QoS configuration designed to achieve 0.00 Load Average while maintaining ultra-low latency for Gaming, VoIP, and Financial Transactions.
🛠️ Project Goals

    Zero-Delay Banking: Prioritize TLS handshakes and ACKs via mangle table tagging.

    Gaming Priority: Behavioral-based prioritization (packet size/burst) to avoid "bufferbloat."

    Social Media Optimization: Fast-tracking TikTok/FB/YouTube flows via Hardware Offloading.

    System Stability: Clean, modular configuration using nftables.
