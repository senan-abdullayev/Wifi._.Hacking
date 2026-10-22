# 📡 WiFi Hacking — Penetration Testing Notes

A personal knowledge base of Wi-Fi (802.11) penetration-testing notes, techniques, and tool references — maintained as an [Obsidian](https://obsidian.md) vault, so internal `[[wiki-links]]` resolve automatically inside Obsidian and render as plain text elsewhere.

> ⚠️ **Authorized use only.** These notes exist for learning and for authorized security work — your own lab, a CTF, or an engagement you have **written permission** to test. Attacking networks you don't own or lack explicit authorization to test is illegal in most jurisdictions. You are responsible for how you use this material.

---

## 📂 Contents

### Fundamentals

Core 802.11 theory needed before any practical attack.

|Note|Topic|
|---|---|
|Wi-Fi Penetration Testing Basics Overview|High-level methodology and workflow|
|802.11 Frames and Types|MAC frame structure; management / control / data frames and sub-types|
|Wi-Fi Interfaces|Wireless adapters and interface naming|
|Interface Modes|Managed vs. monitor mode and how to switch|
|Authentication Methods|Open, WEP, WPA/WPA2/WPA3 authentication|
|WEP IV|WEP initialization vectors and why WEP is broken|

### Aircrack-ng Suite

Reference notes for each tool in the [Aircrack-ng](https://www.aircrack-ng.org/) suite.

|Tool|Purpose|
|---|---|
|Introduction to Aircrack-ng|Overview of the suite|
|Airmon-ng|Enable/disable monitor mode|
|Airodump-ng|Capture packets and discover networks/clients|
|Aireplay-ng|Inject frames (deauth, replay, etc.)|
|Aircrack-ng|Crack WEP/WPA keys from captures|
|Airdecap-ng|Decrypt captured traffic|
|Airgraph-ng|Visualize client/AP relationships|

### Techniques

Hands-on attack and recon write-ups — practice these in a lab only.

|Note|Technique|
|---|---|
|Finding Hidden SSIDs|Reveal hidden networks via passive monitoring and deauth|
|Bypassing MAC Filtering|Spoof an allowed MAC to defeat MAC-based access control|

---

## 🧰 Typical Toolkit

|Tool|Role|
|---|---|
|[Aircrack-ng suite](https://www.aircrack-ng.org/)|Monitor mode, capture, injection, cracking|
|Monitor-mode-capable Wi-Fi adapter|Chipset with `nl80211` / injection support|
|[Kali Linux](https://www.kali.org/)|Distro with the wireless stack preinstalled (or any distro with it added)|
|`iw`|Low-level wireless configuration|
|`macchanger`|MAC address spoofing|
|`wireshark`|Packet capture analysis|
|`hashcat`|Offline hash/key cracking|

## 🚀 Getting Started

1. Open this folder as a vault in **Obsidian** to browse notes with working links and graph view — or read any `.md` file directly in a text editor or on GitHub.
2. Start with **Wi-Fi Penetration Testing Basics Overview**, then work through the rest of _Fundamentals_, before moving to _Techniques_.

## 🗺️ Suggested Learning Path

```
Fundamentals (theory)
  └─ 802.11 Frames → Interfaces → Interface Modes → Authentication Methods → WEP IV
        │
        ▼
Aircrack-ng Suite (tooling)
  └─ Airmon → Airodump → Aireplay → Aircrack → Airdecap / Airgraph
        │
        ▼
Techniques (practice, in a lab)
  └─ Finding Hidden SSIDs → Bypassing MAC Filtering
```

## 🤝 Contributing

This is a living set of personal study notes — expect it to grow and change. If you fork or reuse it, keep the authorized-use principle above intact.

## 📄 License

Personal study notes. No warranty. Use at your own risk and within the law.