
## 1. CPU-Based Cracking

While GPU-based cracking is typically faster due to its ability to handle parallel operations at scale, CPU-based cracking still plays a critical role, especially for hash types that are slow to compute or involve complex operations. CPUs excel at handling such instructions due to their more advanced control logic and instruction pipelines. We can instruct Hashcat to use the `CPU` for cracking by specifying the `-D 1` argument, as shown in the example below.


```
hashcat -m 22000 hash /opt/wordlist.txt -D 1
```

We can further bind hashcat to specific CPU cores using the `--cpu-affinity` flag. This allows finer control over which cores handle the cracking task.

```
hashcat -m 22000 hash /opt/wordlist.txt -D 1 --cpu-affinity=1,2,3,4
```

To control the number of threads hashcat uses, we can use the `--hook-threads` option.

```
hashcat -m 22000 hash /opt/wordlist.txt -D 1 --cpu-affinity=1,2,3,4 --hook-threads=8
```

- `--cpu-affinity`: Bind specific CPU cores for optimized performance
- `--hook-threads`: Specify the number of CPU threads Hashcat can hook into

## 2. GPU-Based Cracking

By default, Hashcat utilizes the `GPU` for cracking operations. However, to explicitly ensure GPU usage, we can include the `-D 2` flag. For maximum efficiency during the cracking process, it is highly recommended to enable optimized kernels using the `-O` or `--optimized-kernel-enable` option. Additionally, combining this with the `-w 4` flag sets the workload profile to `Nightmare`, which pushes the GPU to its highest power and computational capacity. This combination is ideal when speed and performance are critical, and sufficient hardware resources are available.

As detailed in the blog post [Unlocking Hashcat’s Full Potential](https://cyberdigi.medium.com/unlocking-hashcats-full-potential-how-gpu-optimization-reduced-cracking-time-by-85-a-wordlist-17565a7832c5), enabling `--optimized-kernel-enable (-O)` and setting the workload to `-w 4` ("Nightmare" mode) can dramatically reduce cracking time. In one case, these optimizations cut cracking time by `85%`, highlighting the performance gains of GPU tuning.

```
hashcat -m 22000 hash /opt/wordlist.txt --optimized-kernel-enable -w 4
```

## 3. Using CPU and GPU Simultaneously

To maximize cracking speed, Hashcat can leverage both CPU and GPU in a single session. This allows better workload distribution, especially for large or complex attacks.

We can control which devices are used by specifying the device types with `-D`, and selecting individual device IDs with `-d`:

```
hashcat -m 22000 hash /opt/wordlist.txt -D 1,2 -d 1,2 --cpu-affinity=1,2,3,4 --hook-threads=8
```

- `-D 1,2`: Use both CPU (1) and GPU (2) device types
- `-d 1,2`: Use specific backend device IDs (e.g., CPU ID 1 and GPU ID 2)


