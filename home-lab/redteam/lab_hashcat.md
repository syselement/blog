# Hashcat Password Cracking

## Windows Host Machine

- The host PC uses the GPU for decrypting the passwords, so it's faster than using the CPU.
- NVIDIA GPUs require "[NVIDIA Driver](https://www.nvidia.com/download/index.aspx)" (440.64 or later) and "[CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)" (9.0 or later)

### Installation

- Download hashcat binaries from [here](https://hashcat.net/hashcat/)
- Unzip the .7z file into `c:\temp` folder.
- Open Windows Powershell

```powershell
# Change diretory to the unzipped folder, in my case:
cd C:\temp\hashcat-6.2.6
# update folder if different hashcat version
```

- Try **hashcat**

**`.\hashcat.exe -I`**

```powershell
.\hashcat.exe -I

hashcat (v6.2.5) starting in backend information mode

CUDA Info:
==========
CUDA.Version.: 11.7
Backend Device ID #1 (Alias: #2)
  Name...........: NVIDIA GeForce GTX 1060 6GB
  Processor(s)...: 10
  Clock..........: 1708
  Memory.Total...: 6143 MB
  Memory.Free....: 5222 MB
  PCI.Addr.BDFe..: 0000:01:00.0

OpenCL Info:
============
OpenCL Platform ID #1
  Vendor..: NVIDIA Corporation
  Name....: NVIDIA CUDA
  Version.: OpenCL 3.0 CUDA 11.7.101
  Backend Device ID #2 (Alias: #1)
    Type...........: GPU
    Vendor.ID......: 32
    Vendor.........: NVIDIA Corporation
    Name...........: NVIDIA GeForce GTX 1060 6GB
    Version........: OpenCL 3.0 CUDA
    Processor(s)...: 10
    Clock..........: 1708
    Memory.Total...: 6143 MB (limited to 1535 MB allocatable in one block)
    Memory.Free....: 5504 MB
    OpenCL.Version.: OpenCL C 1.2
    Driver.Version.: 516.94
    PCI.Addr.BDF...: 01:00.0
```

- Benchmark hashcat

**`.\hashcat.exe -b`**

- GPU is being used

```powershell
.\hashcat.exe -b

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

* Device #1: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
* Device #2: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
CUDA API (CUDA 11.7)
====================
* Device #1: NVIDIA GeForce GTX 1060 6GB, 5222/6143 MB, 10MCU

OpenCL API (OpenCL 3.0 CUDA 11.7.101) - Platform #1 [NVIDIA Corporation]
========================================================================
* Device #2: NVIDIA GeForce GTX 1060 6GB, skipped

Benchmark relevant options:
===========================
* --optimized-kernel-enable

-------------------
* Hash-Mode 0 (MD5)
-------------------

Speed.#1.........: 13040.1 MH/s (51.02ms) @ Accel:128 Loops:1024 Thr:512 Vec:8

----------------------
* Hash-Mode 100 (SHA1)
----------------------

Speed.#1.........:  4398.0 MH/s (75.87ms) @ Accel:64 Loops:1024 Thr:512 Vec:1

---------------------------
* Hash-Mode 1400 (SHA2-256)
---------------------------

Speed.#1.........:  1649.8 MH/s (50.41ms) @ Accel:16 Loops:512 Thr:1024 Vec:1

---------------------------
* Hash-Mode 1700 (SHA2-512)
---------------------------

Speed.#1.........:   525.8 MH/s (79.19ms) @ Accel:64 Loops:256 Thr:256 Vec:1

-------------------------------------------------------------
* Hash-Mode 22000 (WPA-PBKDF2-PMKID+EAPOL) [Iterations: 4095]
-------------------------------------------------------------

Speed.#1.........:   236.5 kH/s (84.29ms) @ Accel:32 Loops:512 Thr:512 Vec:1

-----------------------
* Hash-Mode 1000 (NTLM)
-----------------------

Speed.#1.........: 22494.1 MH/s (58.73ms) @ Accel:512 Loops:512 Thr:512 Vec:8

---------------------
* Hash-Mode 3000 (LM)
---------------------

Speed.#1.........: 10535.1 MH/s (63.13ms) @ Accel:256 Loops:1024 Thr:256 Vec:1

--------------------------------------------
* Hash-Mode 5500 (NetNTLMv1 / NetNTLMv1+ESS)
--------------------------------------------

Speed.#1.........: 12889.7 MH/s (51.50ms) @ Accel:128 Loops:512 Thr:1024 Vec:2

----------------------------
* Hash-Mode 5600 (NetNTLMv2)
----------------------------

Speed.#1.........:   809.4 MH/s (51.50ms) @ Accel:64 Loops:256 Thr:256 Vec:1

--------------------------------------------------------
* Hash-Mode 1500 (descrypt, DES (Unix), Traditional DES)
--------------------------------------------------------

Speed.#1.........:   448.1 MH/s (92.72ms) @ Accel:16 Loops:1024 Thr:256 Vec:1

------------------------------------------------------------------------------
* Hash-Mode 500 (md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5)) [Iterations: 1000]
------------------------------------------------------------------------------

Speed.#1.........:  4442.3 kH/s (58.29ms) @ Accel:32 Loops:1000 Thr:1024 Vec:1

----------------------------------------------------------------
* Hash-Mode 3200 (bcrypt $2*$, Blowfish (Unix)) [Iterations: 32]
----------------------------------------------------------------

Speed.#1.........:    10632 H/s (87.82ms) @ Accel:32 Loops:8 Thr:12 Vec:1

--------------------------------------------------------------------
* Hash-Mode 1800 (sha512crypt $6$, SHA512 (Unix)) [Iterations: 5000]
--------------------------------------------------------------------

Speed.#1.........:    68894 H/s (45.71ms) @ Accel:64 Loops:1024 Thr:256 Vec:1

--------------------------------------------------------
* Hash-Mode 7500 (Kerberos 5, etype 23, AS-REQ Pre-Auth)
--------------------------------------------------------

Speed.#1.........:   168.5 MH/s (61.92ms) @ Accel:32 Loops:1024 Thr:32 Vec:1

-------------------------------------------------
* Hash-Mode 13100 (Kerberos 5, etype 23, TGS-REP)
-------------------------------------------------

Speed.#1.........:   167.9 MH/s (62.29ms) @ Accel:32 Loops:1024 Thr:32 Vec:1

---------------------------------------------------------------------------------
* Hash-Mode 15300 (DPAPI masterkey file v1 (context 1 and 2)) [Iterations: 23999]
---------------------------------------------------------------------------------

Speed.#1.........:    40283 H/s (83.74ms) @ Accel:16 Loops:1024 Thr:512 Vec:1

---------------------------------------------------------------------------------
* Hash-Mode 15900 (DPAPI masterkey file v2 (context 1 and 2)) [Iterations: 12899]
---------------------------------------------------------------------------------

Speed.#1.........:    18647 H/s (83.37ms) @ Accel:8 Loops:512 Thr:512 Vec:1

------------------------------------------------------------------
* Hash-Mode 7100 (macOS v10.8+ (PBKDF2-SHA512)) [Iterations: 1023]
------------------------------------------------------------------

Speed.#1.........:   195.7 kH/s (86.27ms) @ Accel:4 Loops:1023 Thr:512 Vec:1

---------------------------------------------
* Hash-Mode 11600 (7-Zip) [Iterations: 16384]
---------------------------------------------

Speed.#1.........:   155.2 kH/s (57.81ms) @ Accel:32 Loops:4096 Thr:128 Vec:1

------------------------------------------------
* Hash-Mode 12500 (RAR3-hp) [Iterations: 262144]
------------------------------------------------

Speed.#1.........:    27693 H/s (91.03ms) @ Accel:128 Loops:16384 Thr:32 Vec:1

--------------------------------------------
* Hash-Mode 13000 (RAR5) [Iterations: 32799]
--------------------------------------------

Speed.#1.........:    20363 H/s (60.29ms) @ Accel:4 Loops:1024 Thr:1024 Vec:1

--------------------------------------------------------------------------------
* Hash-Mode 6211 (TrueCrypt RIPEMD160 + XTS 512 bit (legacy)) [Iterations: 1999]
--------------------------------------------------------------------------------

Speed.#1.........:   151.0 kH/s (62.62ms) @ Accel:16 Loops:128 Thr:1024 Vec:1

-----------------------------------------------------------------------------------
* Hash-Mode 13400 (KeePass 1 (AES/Twofish) and KeePass 2 (AES)) [Iterations: 24569]
-----------------------------------------------------------------------------------

Speed.#1.........:    17078 H/s (49.28ms) @ Accel:4 Loops:1024 Thr:512 Vec:1

----------------------------------------------------------------
* Hash-Mode 6800 (LastPass + LastPass sniffed) [Iterations: 499]
----------------------------------------------------------------

Speed.#1.........:  1074.3 kH/s (60.68ms) @ Accel:8 Loops:499 Thr:1024 Vec:1

--------------------------------------------------------------------
* Hash-Mode 11300 (Bitcoin/Litecoin wallet.dat) [Iterations: 200459]
--------------------------------------------------------------------

Speed.#1.........:     2518 H/s (82.68ms) @ Accel:4 Loops:1024 Thr:1024 Vec:1

Started: Wed Sep 07 01:58:33 2022
Stopped: Wed Sep 07 02:02:30 2022
```



## Kali Linux Host Machine

### Installation

- https://miloserdov.org/?p=4726
- https://www.kali.org/docs/general-use/install-nvidia-drivers-on-kali-linux/

```bash
sudo apt update && sudo apt full-upgrade -y
sudo reboot

lsmod | grep -i nouveau

echo -e "blacklist nouveau\noptions nouveau modeset=0\nalias nouveau off" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf

sudo update-initramfs -u && reboot

sudo apt install -y ocl-icd-libopencl1 nvidia-driver nvidia-cuda-toolkit

# Check drivers
nvidia-smi
```

- Try **hashcat**

**`.\hashcat.exe -I`**

```bash
hashcat -I                                                                                                                                                         130 ⨯
hashcat (v6.2.5) starting in backend information mode

CUDA Info:
==========
CUDA.Version.: 11.4
Backend Device ID #1 (Alias: #2)
  Name...........: NVIDIA GeForce RTX 2070 Super with Max-Q Design
  Processor(s)...: 40
  Clock..........: 1155
  Memory.Total...: 7979 MB
  Memory.Free....: 7722 MB
  PCI.Addr.BDFe..: 0000:01:00.0

OpenCL Info:
============
OpenCL Platform ID #1
  Vendor..: NVIDIA Corporation
  Name....: NVIDIA CUDA
  Version.: OpenCL 3.0 CUDA 11.4.264
  Backend Device ID #2 (Alias: #1)
    Type...........: GPU
    Vendor.ID......: 32
    Vendor.........: NVIDIA Corporation
    Name...........: NVIDIA GeForce RTX 2070 Super with Max-Q Design
    Version........: OpenCL 3.0 CUDA
    Processor(s)...: 40
    Clock..........: 1155
    Memory.Total...: 7979 MB (limited to 1994 MB allocatable in one block)
    Memory.Free....: 7680 MB
    OpenCL.Version.: OpenCL C 1.2 
    Driver.Version.: 470.141.03
    PCI.Addr.BDF...: 01:00.0

OpenCL Platform ID #2
  Vendor..: The pocl project
  Name....: Portable Computing Language
  Version.: OpenCL 3.0 PoCL 3.0+debian  Linux, None+Asserts, RELOC, LLVM 13.0.1, SLEEF, DISTRO, POCL_DEBUG

  Backend Device ID #3
    Type...........: CPU
    Vendor.ID......: 128
    Vendor.........: GenuineIntel
    Name...........: pthread-Intel(R) Core(TM) i7-10750H CPU @ 2.60GHz
    Version........: OpenCL 1.2 PoCL HSTR: pthread-x86_64-pc-linux-gnu-haswell
    Processor(s)...: 12
    Clock..........: 5000
    Memory.Total...: 29900 MB (limited to 4096 MB allocatable in one block)
    Memory.Free....: 14918 MB
    OpenCL.Version.: OpenCL C 1.2 PoCL
    Driver.Version.: 3.0+debian
```

- Benchmark hashcat

**`.\hashcat.exe -b`**

- GPU is used

```bash
hashcat -b
hashcat (v6.2.5) starting in benchmark mode

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

* Device #1: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
* Device #2: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
nvmlDeviceGetFanSpeed(): Not Supported

CUDA API (CUDA 11.4)
====================
* Device #1: NVIDIA GeForce RTX 2070 Super with Max-Q Design, 7724/7979 MB, 40MCU

OpenCL API (OpenCL 3.0 CUDA 11.4.264) - Platform #1 [NVIDIA Corporation]
========================================================================
* Device #2: NVIDIA GeForce RTX 2070 Super with Max-Q Design, skipped

OpenCL API (OpenCL 3.0 PoCL 3.0+debian  Linux, None+Asserts, RELOC, LLVM 13.0.1, SLEEF, DISTRO, POCL_DEBUG) - Platform #2 [The pocl project]
============================================================================================================================================
* Device #3: pthread-Intel(R) Core(TM) i7-10750H CPU @ 2.60GHz, skipped

Benchmark relevant options:
===========================
* --optimized-kernel-enable

-------------------
* Hash-Mode 0 (MD5)
-------------------

Speed.#1.........: 25009.0 MH/s (52.70ms) @ Accel:128 Loops:1024 Thr:256 Vec:8

----------------------
* Hash-Mode 100 (SHA1)
----------------------

Speed.#1.........:  7992.3 MH/s (83.06ms) @ Accel:64 Loops:1024 Thr:256 Vec:1

---------------------------
* Hash-Mode 1400 (SHA2-256)
---------------------------

Speed.#1.........:  3459.1 MH/s (96.19ms) @ Accel:16 Loops:1024 Thr:512 Vec:1

---------------------------
* Hash-Mode 1700 (SHA2-512)
---------------------------

Speed.#1.........:  1136.3 MH/s (73.10ms) @ Accel:16 Loops:512 Thr:256 Vec:1

-------------------------------------------------------------
* Hash-Mode 22000 (WPA-PBKDF2-PMKID+EAPOL) [Iterations: 4095]
-------------------------------------------------------------

Speed.#1.........:   383.9 kH/s (50.80ms) @ Accel:64 Loops:1024 Thr:32 Vec:1

-----------------------
* Hash-Mode 1000 (NTLM)
-----------------------

Speed.#1.........: 45363.6 MH/s (57.56ms) @ Accel:512 Loops:1024 Thr:128 Vec:8

---------------------
* Hash-Mode 3000 (LM)
---------------------

Speed.#1.........: 23383.3 MH/s (56.34ms) @ Accel:256 Loops:1024 Thr:128 Vec:1

--------------------------------------------
* Hash-Mode 5500 (NetNTLMv1 / NetNTLMv1+ESS)
--------------------------------------------

Speed.#1.........: 24336.4 MH/s (54.06ms) @ Accel:512 Loops:1024 Thr:64 Vec:2

----------------------------
* Hash-Mode 5600 (NetNTLMv2)
----------------------------

Speed.#1.........:  1746.4 MH/s (95.27ms) @ Accel:32 Loops:512 Thr:256 Vec:1

--------------------------------------------------------
* Hash-Mode 1500 (descrypt, DES (Unix), Traditional DES)
--------------------------------------------------------

Speed.#1.........:   952.4 MH/s (87.28ms) @ Accel:64 Loops:1024 Thr:32 Vec:1

------------------------------------------------------------------------------
* Hash-Mode 500 (md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5)) [Iterations: 1000]
------------------------------------------------------------------------------

Speed.#1.........:  8779.6 kH/s (55.18ms) @ Accel:128 Loops:1000 Thr:128 Vec:1

----------------------------------------------------------------
* Hash-Mode 3200 (bcrypt $2*$, Blowfish (Unix)) [Iterations: 32]
----------------------------------------------------------------

Speed.#1.........:    24951 H/s (78.98ms) @ Accel:128 Loops:32 Thr:16 Vec:1

--------------------------------------------------------------------
* Hash-Mode 1800 (sha512crypt $6$, SHA512 (Unix)) [Iterations: 5000]
--------------------------------------------------------------------

Speed.#1.........:   157.9 kH/s (81.25ms) @ Accel:1024 Loops:512 Thr:128 Vec:1

--------------------------------------------------------
* Hash-Mode 7500 (Kerberos 5, etype 23, AS-REQ Pre-Auth)
--------------------------------------------------------

Speed.#1.........:   454.7 MH/s (91.52ms) @ Accel:256 Loops:128 Thr:32 Vec:1

-------------------------------------------------
* Hash-Mode 13100 (Kerberos 5, etype 23, TGS-REP)
-------------------------------------------------

Speed.#1.........:   442.5 MH/s (94.07ms) @ Accel:256 Loops:128 Thr:32 Vec:1

---------------------------------------------------------------
* Hash-Mode 15300 (DPAPI masterkey file v1) [Iterations: 23999]
---------------------------------------------------------------

Speed.#1.........:    65961 H/s (51.03ms) @ Accel:64 Loops:1024 Thr:32 Vec:1

---------------------------------------------------------------
* Hash-Mode 15900 (DPAPI masterkey file v2) [Iterations: 12899]
---------------------------------------------------------------

Speed.#1.........:    37671 H/s (85.34ms) @ Accel:32 Loops:256 Thr:128 Vec:1

------------------------------------------------------------------
* Hash-Mode 7100 (macOS v10.8+ (PBKDF2-SHA512)) [Iterations: 1023]
------------------------------------------------------------------

Speed.#1.........:   452.3 kH/s (55.32ms) @ Accel:16 Loops:511 Thr:128 Vec:1

---------------------------------------------
* Hash-Mode 11600 (7-Zip) [Iterations: 16384]
---------------------------------------------

Speed.#1.........:   363.1 kH/s (51.68ms) @ Accel:64 Loops:4096 Thr:32 Vec:1

------------------------------------------------
* Hash-Mode 12500 (RAR3-hp) [Iterations: 262144]
------------------------------------------------

Speed.#1.........:    50268 H/s (100.43ms) @ Accel:8 Loops:16384 Thr:256 Vec:1

--------------------------------------------
* Hash-Mode 13000 (RAR5) [Iterations: 32799]
--------------------------------------------

Speed.#1.........:    41473 H/s (60.42ms) @ Accel:128 Loops:64 Thr:256 Vec:1

-----------------------------------------------------------------------
* Hash-Mode 6211 (TrueCrypt RIPEMD160 + XTS 512 bit) [Iterations: 1999]
-----------------------------------------------------------------------

Speed.#1.........:   294.8 kH/s (63.92ms) @ Accel:128 Loops:64 Thr:128 Vec:1

-----------------------------------------------------------------------------------
* Hash-Mode 13400 (KeePass 1 (AES/Twofish) and KeePass 2 (AES)) [Iterations: 24569]
-----------------------------------------------------------------------------------

Speed.#1.........:    30741 H/s (54.78ms) @ Accel:8 Loops:1024 Thr:128 Vec:1

----------------------------------------------------------------
* Hash-Mode 6800 (LastPass + LastPass sniffed) [Iterations: 499]
----------------------------------------------------------------

Speed.#1.........:  2500.5 kH/s (46.23ms) @ Accel:32 Loops:124 Thr:512 Vec:1

--------------------------------------------------------------------
* Hash-Mode 11300 (Bitcoin/Litecoin wallet.dat) [Iterations: 200459]
--------------------------------------------------------------------

Speed.#1.........:     4979 H/s (66.71ms) @ Accel:2048 Loops:1024 Thr:32 Vec:1

Started: Wed Sep  7 16:04:07 2022
Stopped: Wed Sep  7 16:10:37 2022
```



## Virtual Machine

**`.\hashcat.exe -I`**

```bash
hashcat -I              
hashcat (v6.2.5) starting in backend information mode

OpenCL Info:
============
OpenCL Platform ID #1
  Vendor..: The pocl project
  Name....: Portable Computing Language
  Version.: OpenCL 3.0 PoCL 3.0+debian  Linux, None+Asserts, RELOC, LLVM 13.0.1, SLEEF, DISTRO, POCL_DEBUG
  Backend Device ID #1
    Type...........: CPU
    Vendor.ID......: 128
    Vendor.........: GenuineIntel
    Name...........: pthread-Intel(R) Core(TM) i7-7700K CPU @ 4.20GHz
    Version........: OpenCL 1.2 PoCL HSTR: pthread-x86_64-pc-linux-gnu-haswell
    Processor(s)...: 4
    Clock..........: 4200
    Memory.Total...: 5872 MB (limited to 1024 MB allocatable in one block)
    Memory.Free....: 2904 MB
    OpenCL.Version.: OpenCL C 1.2 PoCL
    Driver.Version.: 3.0+debian

```

**`.\hashcat.exe -b`**

- CPU is used

```bash
.\hashcat.exe -b

hashcat (v6.2.5) starting in benchmark mode

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

OpenCL API (OpenCL 3.0 PoCL 3.0+debian  Linux, None+Asserts, RELOC, LLVM 13.0.1, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: pthread-Intel(R) Core(TM) i7-7700K CPU @ 4.20GHz, 2904/5872 MB (1024 MB allocatable), 4MCU

Benchmark relevant options:
===========================
* --optimized-kernel-enable

-------------------
* Hash-Mode 0 (MD5)
-------------------

Speed.#1.........:   411.7 MH/s (4.78ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

----------------------
* Hash-Mode 100 (SHA1)
----------------------

Speed.#1.........:   266.0 MH/s (7.26ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

---------------------------
* Hash-Mode 1400 (SHA2-256)
---------------------------

Speed.#1.........:   102.5 MH/s (20.15ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

---------------------------
* Hash-Mode 1700 (SHA2-512)
---------------------------

Speed.#1.........: 33849.6 kH/s (61.53ms) @ Accel:512 Loops:1024 Thr:1 Vec:4

-------------------------------------------------------------
* Hash-Mode 22000 (WPA-PBKDF2-PMKID+EAPOL) [Iterations: 4095]
-------------------------------------------------------------

Speed.#1.........:     9721 H/s (44.56ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

-----------------------
* Hash-Mode 1000 (NTLM)
-----------------------

Speed.#1.........:   641.0 MH/s (3.04ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

---------------------
* Hash-Mode 3000 (LM)
---------------------

Speed.#1.........:   102.1 MH/s (19.77ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

--------------------------------------------
* Hash-Mode 5500 (NetNTLMv1 / NetNTLMv1+ESS)
--------------------------------------------

Speed.#1.........:   456.2 MH/s (4.34ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

----------------------------
* Hash-Mode 5600 (NetNTLMv2)
----------------------------

Speed.#1.........: 34218.4 kH/s (60.95ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

--------------------------------------------------------
* Hash-Mode 1500 (descrypt, DES (Unix), Traditional DES)
--------------------------------------------------------

Speed.#1.........:  3077.6 kH/s (80.90ms) @ Accel:64 Loops:1024 Thr:1 Vec:8

------------------------------------------------------------------------------
* Hash-Mode 500 (md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5)) [Iterations: 1000]
------------------------------------------------------------------------------

Speed.#1.........:    31354 H/s (63.42ms) @ Accel:512 Loops:1000 Thr:1 Vec:8

----------------------------------------------------------------
* Hash-Mode 3200 (bcrypt $2*$, Blowfish (Unix)) [Iterations: 32]
----------------------------------------------------------------

Speed.#1.........:       29 H/s (1.53ms) @ Accel:4 Loops:32 Thr:1 Vec:1

--------------------------------------------------------------------
* Hash-Mode 1800 (sha512crypt $6$, SHA512 (Unix)) [Iterations: 5000]
--------------------------------------------------------------------

Speed.#1.........:     1736 H/s (58.54ms) @ Accel:512 Loops:1024 Thr:1 Vec:4

--------------------------------------------------------
* Hash-Mode 7500 (Kerberos 5, etype 23, AS-REQ Pre-Auth)
--------------------------------------------------------

Speed.#1.........:  2836.5 kH/s (92.14ms) @ Accel:256 Loops:256 Thr:1 Vec:8

-------------------------------------------------
* Hash-Mode 13100 (Kerberos 5, etype 23, TGS-REP)
-------------------------------------------------

Speed.#1.........:  2835.8 kH/s (92.11ms) @ Accel:256 Loops:256 Thr:1 Vec:8

---------------------------------------------------------------
* Hash-Mode 15300 (DPAPI masterkey file v1) [Iterations: 23999]
---------------------------------------------------------------

Speed.#1.........:     1881 H/s (45.09ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

---------------------------------------------------------------
* Hash-Mode 15900 (DPAPI masterkey file v2) [Iterations: 12899]
---------------------------------------------------------------

Speed.#1.........:     1086 H/s (72.22ms) @ Accel:512 Loops:512 Thr:1 Vec:4

------------------------------------------------------------------
* Hash-Mode 7100 (macOS v10.8+ (PBKDF2-SHA512)) [Iterations: 1023]
------------------------------------------------------------------

Speed.#1.........:    17049 H/s (39.39ms) @ Accel:512 Loops:511 Thr:1 Vec:4

---------------------------------------------
* Hash-Mode 11600 (7-Zip) [Iterations: 16384]
---------------------------------------------

Speed.#1.........:     1936 H/s (131.61ms) @ Accel:256 Loops:4096 Thr:1 Vec:8

------------------------------------------------
* Hash-Mode 12500 (RAR3-hp) [Iterations: 262144]
------------------------------------------------

Speed.#1.........:      379 H/s (84.16ms) @ Accel:128 Loops:16384 Thr:1 Vec:8

--------------------------------------------
* Hash-Mode 13000 (RAR5) [Iterations: 32799]
--------------------------------------------

Speed.#1.........:     1212 H/s (51.06ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

-----------------------------------------------------------------------
* Hash-Mode 6211 (TrueCrypt RIPEMD160 + XTS 512 bit) [Iterations: 1999]
-----------------------------------------------------------------------

Speed.#1.........:     7628 H/s (66.04ms) @ Accel:512 Loops:512 Thr:1 Vec:8

-----------------------------------------------------------------------------------
* Hash-Mode 13400 (KeePass 1 (AES/Twofish) and KeePass 2 (AES)) [Iterations: 24569]
-----------------------------------------------------------------------------------

Speed.#1.........:      962 H/s (88.54ms) @ Accel:512 Loops:1024 Thr:1 Vec:8

----------------------------------------------------------------
* Hash-Mode 6800 (LastPass + LastPass sniffed) [Iterations: 499]
----------------------------------------------------------------

Speed.#1.........:    82716 H/s (22.50ms) @ Accel:512 Loops:499 Thr:1 Vec:8

--------------------------------------------------------------------
* Hash-Mode 11300 (Bitcoin/Litecoin wallet.dat) [Iterations: 200459]
--------------------------------------------------------------------

Speed.#1.........:      165 H/s (14.93ms) @ Accel:512 Loops:1024 Thr:1 Vec:4

Started: Wed Sep  7 01:44:24 2022
Stopped: Wed Sep  7 01:52:30 2022

```

------

