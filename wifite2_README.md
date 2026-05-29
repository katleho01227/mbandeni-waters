Wifite
======

This repo is a complete re-write of [`wifite`](https://github.com/derv82/wifite)
, a Python script for auditing wireless networks.

Wifite runs existing wireless-auditing tools for you. Stop memorizing command ar
guments & switches!

Wifite is designed to use all known methods for retrieving the password of a wir
eless access point (router).  These methods include:
1. WPS: The [Offline Pixie-Dust attack](https://en.wikipedia.org/wiki/Wi-Fi_Prot
ected_Setup#Offline_brute-force_attack)
1. WPS: The [Online Brute-Force PIN attack](https://en.wikipedia.org/wiki/Wi-Fi_
Protected_Setup#Online_brute-force_attack)
2. WPA: The [WPA Handshake Capture](https://hashcat.net/forum/thread-7717.html) 
+ offline crack.
3. WPA: The [PMKID Hash Capture](https://hashcat.net/forum/thread-7717.html) + o
ffline crack.
4. WEP: Various known attacks against WEP, including *fragmentation*, *chop-chop
*, *aireplay*, etc.

Run wifite, select your targets, and Wifite will automatically start trying to c
apture or crack the password.

Supported Operating Systems
---------------------------
Wifite is designed specifically for the latest version of [**Kali** Linux](https
://www.kali.org/). [ParrotSec](https://www.parrotsec.org/) is also supported.

Other pen-testing distributions (such as BackBox or Ubuntu) have outdated versio
ns of the tools used by Wifite. Do not expect support unless you are using the l
atest versions of the *Required Tools*, and also [patched wireless drivers that 
support injection]().

Required Tools
--------------
First and foremost, you will need a wireless card capable of "Monitor Mode" and 
packet injection (see [this tutorial for checking if your wireless card is compa
tible](http://www.aircrack-ng.org/doku.php?id=compatible_cards) and also [this g
uide](https://en.wikipedia.org/wiki/Wi-Fi_Protected_Setup#Offline_brute-force_at
tack)). There are many cheap wireless cards that plug into USB available from on
line stores.

Second, only the latest versions of these programs are supported and must be ins
talled for Wifite to work properly:

**Required:**

* `python`: Wifite is compatible with both `python2` and `python3`.
* [`iwconfig`](https://wiki.debian.org/iwconfig): For identifying wireless devic
es already in Monitor Mode.
* [`ifconfig`](https://en.wikipedia.org/wiki/Ifconfig): For starting/stopping wi
reless devices.
* [`Aircrack-ng`](http://aircrack-ng.org/) suite, includes:
   * [`airmon-ng`](https://tools.kali.org/wireless-attacks/airmon-ng): For enume
rating and enabling Monitor Mode on wireless devices.
   * [`aircrack-ng`](https://tools.kali.org/wireless-attacks/aircrack-ng): For c
racking WEP .cap files and WPA handshake captures.
   * [`aireplay-ng`](https://tools.kali.org/wireless-attacks/aireplay-ng): For d
eauthing access points, replaying capture files, various WEP attacks.
   * [`airodump-ng`](https://tools.kali.org/wireless-attacks/airodump-ng): For t
arget scanning & capture file generation.
   * [`packetforge-ng`](https://tools.kali.org/wireless-attacks/packetforge-ng):
 For forging capture files.

**Optional, but Recommended:**

* [`tshark`](https://www.wireshark.org/docs/man-pages/tshark.html): For detectin
g WPS networks and inspecting handshake capture files.
* [`reaver`](https://github.com/t6x/reaver-wps-fork-t6x): For WPS Pixie-Dust & b
rute-force attacks.
   * Note: Reaver's `wash` tool can be used to detect WPS networks if `tshark` i
s not found.
* [`bully`](https://github.com/aanarchyy/bully): For WPS Pixie-Dust & brute-forc
e attacks.
   * Alternative to Reaver. Specify `--bully` to use Bully instead of Reaver.
   * Bully is also used to fetch PSK if `reaver` cannot after cracking WPS PIN.
* [`coWPAtty`](https://tools.kali.org/wireless-attacks/cowpatty): For detecting 
handshake captures.
* [`pyrit`](https://github.com/JPaulMora/Pyrit): For detecting handshake capture
s.
* [`hashcat`](https://hashcat.net/): For cracking PMKID hashes.
   * [`hcxdumptool`](https://github.com/ZerBea/hcxdumptool): For capturing PMKID
 hashes.
   * [`hcxpcaptool`](https://github.com/ZerBea/hcxtools): For converting PMKID p
acket captures into `hashcat`'s format.


Run Wifite
----------
```
git clone https://github.com/derv82/wifite2.git
cd wifite2
sudo ./Wifite.py
```

Install Wifite
--------------
To install onto your computer (so you can just run `wifite` from any terminal), 
run:

```bash
sudo python setup.py install
```

This will install `wifite` to `/usr/sbin/wifite` which should be in your termina
l path.

**Note:** Uninstalling is [not as easy](https://stackoverflow.com/questions/1550
226/python-setup-py-uninstall#1550235). The only way to uninstall is to record t
he files installed by the above command and *remove* those files:

```bash
sudo python setup.py install --record files.txt \
  && cat files.txt | xargs sudo rm \
  && rm -f files.txt
```

Brief Feature List
------------------
* [PMKID hash capture](https://hashcat.net/forum/thread-7717.html) (enabled by-d
efault, force with: `--pmkid`)
* WPS Offline Brute-Force Attack aka "Pixie-Dust". (enabled by-default, force wi
th: `--wps-only --pixie`)
* WPS Online Brute-Force Attack aka "PIN attack". (enabled by-default, force wit
h: `--wps-only --no-pixie`)
* WPA/2 Offline Brute-Force Attack via 4-Way Handshake capture (enabled by-defau
lt, force with: `--no-wps`)
* Validates handshakes against `pyrit`, `tshark`, `cowpatty`, and `aircrack-ng` 
(when available)
* Various WEP attacks (replay, chopchop, fragment, hirte, p0841, caffe-latte)
* Automatically decloaks hidden access points while scanning or attacking.
   * Note: Only works when channel is fixed. Use `-c <channel>`
   * Disable this using `--no-deauths`
* 5Ghz support for some wireless cards (via `-5` switch).
   * Note: Some tools don't play well on 5GHz channels (e.g. `aireplay-ng`)
* Stores cracked passwords and handshakes to the current directory (`--cracked`)
   * Includes information about the cracked access point (Name, BSSID, Date, etc
).
* Easy to try to crack handshakes or PMKID hashes against a wordlist (`--crack`)

What's new?
-----------
Comparing this repo to the "old wifite" @ https://github.com/derv82/wifite

* **Less bugs**
   * Cleaner process management. Does not leave processes running in the backgro
und (the old `wifite` was bad about this).
   * No longer "one monolithic script". Has working unit tests. Pull requests ar
e less-painful!
* **Speed**
   * Target access points are refreshed every second instead of every 5 seconds.
* **Accuracy**
   * Displays realtime Power level of currently-attacked target.
   * Displays more information during an attack (e.g. % during WEP chopchop atta
cks, Pixie-Dust step index, etc)
* **Educational**
   * The `--verbose` option (expandable to `-vv` or `-vvv`) shows which commands
 are executed & the output of those commands.
   * This can help debug why Wifite is not working for you. Or so you can learn 
how these tools are used.
* More-actively developed.
* Python 3 support.
* Sweet new ASCII banner.

What's gone?
------------
* Some command-line arguments (`--wept`, `--wpst`, and other confusing switches)
.
   * You can still access some of these obscure options, try `wifite -h -v`

What's not new?
---------------
* (Mostly) Backwards compatible with the original `wifite`'s arguments.
* Same text-based interface everyone knows and loves.

Screenshots
-----------
Cracking WPS PIN using `reaver`'s Pixie-Dust attack, then fetching WPA key using
 `bully`:
![Pixie-Dust with Reaver to get PIN and Bully to get PSK](https://i.imgur.com/Q5
KSDbg.gif)

-------------

Cracking WPA key using PMKID attack:
![PMKID attack](https://i.imgur.com/CR8oOp0.gif)

-------------

Decloaking & cracking a hidden access point (via the WPA Handshake attack):
![Decloaking and Cracking a hidden access point](https://i.imgur.com/F6VPhbm.gif
)

-------------

Cracking a weak WEP password (using the WEP Replay attack):
![Cracking a weak WEP password](https://i.imgur.com/jP72rVo.gif)

-------------

Cracking a pre-captured handshake using John The Ripper (via the `--crack` optio
n):
![--crack option](https://i.imgur.com/iHcfCjp.gif)
