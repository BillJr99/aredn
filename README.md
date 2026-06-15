# Amateur Radio Emergency Data Network AREDN® Firmware

https://www.arednmesh.org

## About AREDN®

AREDN® wireless networks are deployed by licensed Amateur Radio operators, Technician Class or higher, under FCC Part 97 allocations adjacent to FCC part 15, unlicensed, WIFI, allocations. They are configured as adhoc nodes to form mesh networks. The firmware created below enables the effective use of valuable and dedicated frequencies for communication services to government and private relief organizations in times of disaster or other emergencies.

This firmware enables 802.11 wireless networks to be created and expanded with little to no IT expertise.  A user can deploy a 'node' anywhere to connect to and extend an AREDN® network.  Device hardware options exist to provide sector coverage, build point-to-point links, and connect end point services to the network. High speed link rates are routinely achieved over long distances, e.g. 60Mbps+ on 10MHz channels over 80km links.

For further information on obtaining an Amateur Radio Technician Class license, please refer to https://www.arrl.org/getting-your-technician-license

## Usage Information

### Ethernet Port usage

The standard Ethernet port of an AREDN® device uses the following vlan tags.  An 802.1Q switch is necessary to utilize the vlan tagged networks:

* untagged:  LAN devices - laptop, ipcam, voip phone, etc.
* vlan 1:  WAN - gateway to connect AREDN® network to home network and/or internet (some devices support changing this vlan to an alternate value)
* vlan 2:  DtDLink (device to device) - AREDN® network routing between nodes, typically cross band

The following devices have enhanced Ethernet port usage.  A single cat5 to the device could be plugged into ether the 'main' or 'secondary' port with standard port functionality. Both ports can be used interchangeably and simultaneously with LAN devices on both ports at the same time.

If the device's hardware supports POE (Power Over Ethernet) pass-through from main port to secondary port, an Advanced Setting option will show to turn on/off.  This is useful to power ipCams or other mesh nodes by daisy chaining the network cable from one device to another. Then only one network cable may be needed to reach 2 or 3 devices on the tower. Be sure to check the power capacity in the vendor specifications to not exceed. The first device in the chain will provide the DHCP address to all LAN devices on all nodes (because it powers up first).

* NanoStation M2 XW
* NanoStation M5 XW
* NanoStation M2 XM
* NanoStation M3 XM
* NanoStation M5 XM
* TP-Link CPE210 v1/v1.1
* TP-Link CPE220 v2
* TP-Link CPE220 v3
* TP-Link CPE510 v1/v1.1
* TP-Link WBS210 v1
* TP-Link WBS210 v2
* TP-Link WBS510 v1
* TP-Link WBS510 v2

The Mikrotik hAP AC Lite, Mikrotik hAP ac2, and Mikrotik hAP ac3 are pre-configured with the following VLANs:

* Port 1: WAN Port - Packets in/out of this port are expected to be untagged. The node is (by default) configured to receive a DHCP assigned address from a home network, internet, or other foreign network.
* Port 5: DtDLink Port Mesh Routing -- Connect to another mesh node or 8021.q switch. Packets in/out of this port must be vlan 2 tagged, other packets are ignored.
* Ports 2-4: LAN devices -- Packets in/out of this port are expected to be untagged. The mesh node will (default) DHCP assign an IP address to your computer, ipCam, voip phone, etc. connected to these ports.

The Ubiquiti AirRouter and AirRouter HP are pre-configured with the following VLANs:

* Port "WAN": Packets in/out of this port are expected to be untagged. The node is (by default) configured to receive a DHCP assigned address from a home network, internet, or other foreign network.
* Port 4: DtDLink Port Mesh Routing -- Connect to another mesh node or 8021.q switch. Packets in/out of this port must be vlan 2 tagged, other packets are ignored.
* Ports 1-3: LAN devices -- Packets in/out of this port are expected to be untagged. The mesh node will (default) DHCP assign an IP address to your computer, ipCam, voip phone, etc. connected to these ports.

The GL.iNet GL-AR150, GL-AR300M16, and the OpenWRT One are pre-configured with the following VLANS:

* Port labeled "WAN": untagged = AREDN® WAN
* Port labeled "LAN": untagged = AREDN® LAN, vlan 2 = DtDLink (device to device)

The GL.iNet GL-AR750 is pre-configured with the following ports, left to right:

* Left Port with internet globe icon:  WAN (untagged)
* Middle Port with "<..>" icon: DtDLink (vlan 2)
* Right Port with "<..>" icon: LAN (untagged)

IMPORTANT: For Gl.iNet devices, when initially installing AREDN® on OpenWRT, you *MUST* uncheck the "Keep Settings" checkbox.

## Submitting Bug Reports

Please submit all issues to https://github.com/aredn/aredn/issues

## Developer Only Information

The AREDN® firmware is based on OpenWrt with additional packages and patches. A Makefile automates the entire process to create firmware images.

### Images built with the instructions below

This is the active 'main' branch with latest AREDN® code. The Amateur Radio community is encouraged to participate in loading the images produced from a "nightly build" and run the AREDN® firmware in a variety of environments. Given new features may not yet be documented, participants should already have a basic knowledge of Linux and networking to understand and provide useful feedback to the Developer submitting the code.

The goal of participation is to obtain confidence that new features and the overall mesh node is stable. The more participation, the earlier an issue is found, the faster an enhancement will be turned into a release.

Please refer to https://github.com/aredn/aredn/issues for a list of outstanding defects.

### Building with Docker
Installing the Docker environment on your windows/linux/mac machine is a pre-requisite. A docker 'container' has been pre-configured with an aredn linux build environment. Alternative instructions are below if you wish to setup your linux install with the compiler pre-requisites necessary to do the build.

To build with docker:
```
docker pull arednmesh/builder
docker run -it --name builder arednmesh/builder
```

To pull an image (or any other file) out of the docker container:
```
docker cp builder:/opt/aredn/aredn/firmware/targets/ath79/generic/<image>.bin <local directory>
```

### Build Prerequisites

Please take a look at the [OpenWrt documentation](https://openwrt.org/docs/guide-developer/build-system/install-buildsystem) for a complete and up to date list of packages for your operating system.

On Ubuntu/Debian:
```
apt-get install git subversion build-essential libncurses5-dev \
  zlib1g-dev gawk unzip libxml-perl flex wget gettext quilt \
  python libssl-dev shellcheck lua5.1
```

On openSUSE:
```
zypper install --type pattern devel_basis
zypper install git subversion ncurses-devel zlib-devel gawk unzip \
  perl-libxml-perl flex wget gettext-runtime quilt python \
  libopenssl-devel shellcheck lua51
```

On Arch:
```
pacman -S base-devel subversion zlib unzip perl-xml-libxml wget \
  quilt openssl shellcheck lua51 git
```

### Building firmware images

To obtain the source and build the firmware locally use:

```
bash
git clone https://github.com/aredn/aredn.git
cd aredn
vi config.mk # enter your callsign, etc.
# build default legacy ath79 target ubnt and tplink images
make  
# build and add legacy ath79 target mikrotik images
make SUBTARGET=mikrotik
# build and add ath79 target (latest linux kernel) ubnt, tplink, and gl images
make MAINTARGET=ath79
```

Building the images may take minutes or hours depending on the machine. For more details see [build options](https://openwrt.org/docs/guide-developer/build-system/use-buildsystem).  
Review the build options in config.mk: `-j <nuMBer of cores + 1>`.
`V=s` will give more verbose error messages.

An internet connection is required during the build process. A good internet connection can improve the build time.

You need approximately 10GB of space for the build.

The build is **self-contained**: the OpenWrt tree, all downloads, the staging
directory, and the output images are created *inside* the `aredn/` directory you
clone. Nothing is written to your home directory or to system locations, so you
can remove the whole `aredn/` folder to reclaim the space when you are done.

#### Building under WSL (Windows Subsystem for Linux)

The native build works under WSL2 (Ubuntu/Debian), but two things matter:

* Clone and build on the **Linux filesystem** (e.g. `~/aredn`), **not** under
  `/mnt/c` or any other Windows drive. The Windows mount is not fully
  case-sensitive and does not handle the symlinks the build relies on.
* Do **not** run `make` as `root` — OpenWrt refuses to build as root. Use a
  normal user.

```
bash
# in a WSL Ubuntu/Debian shell, install the prerequisites listed above, then:
cd ~                      # stay on the Linux filesystem, not /mnt/c
git clone https://github.com/aredn/aredn.git
cd aredn
vi config.mk              # enter your callsign, etc.
make MAINTARGET=ath79 SUBTARGET=tiny   # or any other target
```

#### One-shot, no-trace Docker build for all targets (layered on `main`)

This recipe builds **every supported target** inside a throwaway container,
layering this branch on top of upstream `main` (this is what the nightly CI builds,
plus the extras this branch adds). Everything — the clone, the OpenWrt tree, all
intermediate files — lives inside the container; only the finished `.bin` images
are copied out, and the container is deleted at the end, so nothing is left behind.
It works the same from WSL, Linux, or macOS. The output includes the original
experimental XM `ath79/tiny` boards **and** all the revived sunset/frozen boards
listed in the "Experimental / revived legacy devices" section — they ride along in
the `ath79` generic/mikrotik/nand/tiny lines below, so no extra `make` lines are
needed.

The build runs as the image's unprivileged `aredn` user (OpenWrt refuses to build
as `root`), which cannot write into a host bind-mount. So instead of mounting a
folder, build in a **named** container, stage the images to a directory *inside* it,
then copy them to the host with `docker cp` (which runs with host privileges):

```
bash
mkdir -p aredn-out
docker run --name aredn-builder arednmesh/builder bash -lc '
  git clone --branch main https://github.com/BillJr99/aredn.git ~/aredn-build &&
  cd ~/aredn-build &&
  # A throwaway git identity is needed because layering a branch makes a merge commit.
  git config user.email aredn-build@example.invalid && git config user.name "AREDN build" &&
  # Layer this fork branch onto main. Repeat this line to bring in additional
  # branches, e.g. also run:  git pull --no-rebase --no-edit -X ours origin mcar/dhcp
  # The fork branches touch disjoint firmware files, so the code always merges
  # cleanly; `-X ours` only matters for this README (each branch customizes it) and
  # simply keeps the first branch's copy — irrelevant to what gets built.
  git pull --no-rebase --no-edit -X ours origin mcar/tiny-xm &&
  J="-j$(nproc)" &&   # parallelize across all CPU cores

  # Builds EVERY target below. To build just one, replace the whole make chain
  # with a single line, e.g.:  make MAINTARGET=ath79 SUBTARGET=tiny MAKE_ARGS="$J"
  # and narrow the copy below to that target dir (firmware/targets/ath79/tiny/).

  # ath79 (mips_24kc)
  make MAINTARGET=ath79   SUBTARGET=generic                   MAKE_ARGS="$J" &&
  make MAINTARGET=ath79   SUBTARGET=mikrotik                  MAKE_ARGS="$J" &&
  make MAINTARGET=ath79   SUBTARGET=mikrotik ALTTARGET=ath10k MAKE_ARGS="$J" &&
  make MAINTARGET=ath79   SUBTARGET=mikrotik ALTTARGET=nand   MAKE_ARGS="$J" &&
  make MAINTARGET=ath79   SUBTARGET=nand                      MAKE_ARGS="$J" &&
  make MAINTARGET=ath79   SUBTARGET=tiny                      MAKE_ARGS="$J" &&

  # ipq40xx (arm_cortex-a7)
  make MAINTARGET=ipq40xx SUBTARGET=generic                  MAKE_ARGS="$J" &&
  make MAINTARGET=ipq40xx SUBTARGET=mikrotik                 MAKE_ARGS="$J" &&

  # ramips (mipsel_24kc), incl. MorseMicro HaLow variants
  make MAINTARGET=ramips  SUBTARGET=mt7621                   MAKE_ARGS="$J" &&
  make MAINTARGET=ramips  SUBTARGET=mt7621 ALTTARGET=morse   MAKE_ARGS="$J" &&
  make MAINTARGET=ramips  SUBTARGET=mt76x8                   MAKE_ARGS="$J" &&
  make MAINTARGET=ramips  SUBTARGET=mt76x8 ALTTARGET=morse   MAKE_ARGS="$J" &&

  # mediatek (aarch64) and x86
  make MAINTARGET=mediatek SUBTARGET=filogic                MAKE_ARGS="$J" &&
  make MAINTARGET=x86      SUBTARGET=64                      MAKE_ARGS="$J" &&

  # stage every built image into a directory inside the container
  mkdir -p ~/out && cp -rv firmware/targets/* ~/out/
'
# pull the whole tree out from the host side
docker cp aredn-builder:/home/aredn/out/. aredn-out/
ls -la aredn-out/          # confirm the images are really here ...
docker rm aredn-builder    # ... and only then discard the container + build tree
```

To iterate without rebuilding the cross-toolchain, **keep the container** (skip the
final `docker rm`) and re-run later with `docker start aredn-builder` + `docker exec
aredn-builder bash -lc 'cd ~/aredn-build && git pull --no-rebase --no-edit -X ours origin mcar/tiny-xm &&
make ...'`, which reuses the toolchain, download cache, and compiled objects. A build
interrupted mid-compile can leave the tree in a state `make` will not untangle; if a
reused build starts erroring oddly, scrap it (`docker rm -f aredn-builder`) and start
fresh.

Notes:
* This builds **five CPU architectures**, so it pulls a separate toolchain for
  each — expect **tens of GB** of container scratch space (well beyond the ~10GB
  single-target figure) and **several hours**. `docker rm aredn-builder` at the
  end discards all of that scratch; only the images under `./aredn-out/` remain.
* **Speed:** `MAKE_ARGS="-j$(nproc)"` (already inline above) overrides the repo's
  `-j3` default (`config.mk`) to build in parallel across every core — by far the
  biggest win. On **Docker Desktop** (macOS / WSL2) the VM caps CPUs/RAM, which
  throttles `-j`: raise them under *Settings → Resources*, or pin them per run
  with `--cpus` / `--memory` (e.g. `docker run --cpus=16 --memory=24g ...`); on
  native Linux the container already sees all host cores. With ≥32 GB RAM you can
  build in a RAM disk by adding `--tmpfs /home/aredn:exec,size=32g` to `docker
  run` (the mount **must** be `exec`). Note these throwaway builds keep no cache,
  so each run re-downloads sources and rebuilds the toolchain.
* **On WSL**, use a Linux-filesystem output dir (e.g. `aredn-out` under `~`), not a
  `/mnt/c/...` Windows path — `docker cp` fails with `permission denied` writing to
  the Windows drive — and verify `ls -la aredn-out/` before `docker rm`. To get the
  images into Windows afterward, copy them once extracted
  (`cp ~/aredn-out/*.bin /mnt/c/Users/<you>/...`).
* The `&&` between lines aborts the whole run on the first failure. Use `;`
  instead if you would rather build best-effort and keep going past a target that
  fails, then copy out whatever succeeded.

### Experimental / revived legacy devices

A number of older boards that had been **sunset** or **frozen** are revived on
this branch as **experimental, best-effort** Babel-only builds. They fall into
two groups:

* **Legacy Ubiquiti "XM" boards (8MB flash / 32MB RAM)** — revived via a
  reduced-package `ath79/tiny` image (documented in detail just below).
* **Other formerly sunset/frozen boards** — these already build with the normal
  AREDN package set in their existing subtargets; "reviving" them only changes
  their status from sunset/frozen to experimental. No special config is needed:

  | Device | OpenWrt profile | MAINTARGET | SUBTARGET |
  | :----- | :-------------- | :--------- | :-------- |
  | Mikrotik hAP ac lite / TC | `mikrotik_routerboard-952ui-5ac2nd` | ath79 | mikrotik |
  | GL.iNet White (GL-AR150) | `glinet_gl-ar150` | ath79 | generic |
  | GL.iNet Microuter (GL-USB150) | `glinet_gl-usb150` | ath79 | generic |
  | GL.iNet Creta (GL-AR750) | `glinet_gl-ar750` | ath79 | generic |
  | GL.iNet Slate (GL-AR750S-Ext) | `gl-ar750s-nor` / `gl-ar750s-nor-nand` | ath79 | nand |
  | Ubiquiti Rocket M2 Titanium XW | `ubnt_rocket-m2-xw` | ath79 | generic |
  | Ubiquiti Rocket M5 Titanium XW | `ubnt_rocket-m-xw` | ath79 | generic |
  | Ubiquiti AirGrid M5 XW | `ubnt_bullet-m-xw` | ath79 | generic |

  All of the boards above are already enabled in `configs/ath79-*.config`, so the
  normal `make MAINTARGET=ath79 SUBTARGET={generic,mikrotik,nand}` builds (and the
  "build all" recipe above) already produce their images. The 64MB+ boards build
  the full image normally; **AirGrid M5 XW** is the exception — it has only
  8MB flash / 32MB RAM but builds from the *full* generic image (it cannot use the
  slimmed `tiny` package set, which only covers the XM subtarget), so it is the
  tightest of the revived boards and the most likely to run short on flash/RAM.

  **Boards that cannot be revived:** OpenWrt no longer ships a device profile for
  the Ubiquiti **Bullet M2Ti**, **Bullet M5Ti**, **NanoBeam M2-13**, **Rocket M2
  Titanium TI** or **Rocket M5 Titanium TI**, so no image can be produced for them
  no matter what config is used. They remain listed in `SUPPORTED_DEVICES.md` for
  reference only.

#### Experimental tiny build for legacy Ubiquiti XM devices

The `ath79/tiny` target is an **experimental, best-effort** revival of the old
8MB-flash / 32MB-RAM Ubiquiti "XM" radios (NanoStation M2/M3/M5 XM, NanoStation
Loco M2/M5/M9, Bullet M, NanoBridge M, PicoStation M, AirRouter). These boards
were *frozen* when AREDN moved to the 4.x series. They were not dropped because
of Babel itself: the transitional releases had to carry **both** OLSR and Babel
at once, and that dual stack would not fit on a 32MB-RAM node. 4.x is now
**Babel-only**, so a slimmed-down image fits again.

**What is included:** Babel mesh routing, the ath9k WiFi stack (including Part-15
capability via the standard AREDN regulatory/extended-spectrum support),
WireGuard tunnels, and the normal AREDN web UI.

**What is removed to fit 8MB flash / 32MB RAM** (see `configs/ath79-tiny.config`):
SNMP, the Prometheus exporter, iperf3, socat, curl/libcurl, ethtool, the ath9k
debug/spectral options, advanced traffic-control (`tc-full`/CAKE) and UBI tools.
`zram-swap` is enabled in the image to add compressed swap on these low-memory
boards.

**Limitations / operating guidance:**
* Experimental and **untested** on the current firmware — not for production use.
* 32MB RAM is tight. Keep tunnels to **1 or 2** and **do not install add-on
  packages** on these nodes.
* No SNMP/Prometheus/iperf3/curl or USB-storage support.

**Build it:**

```
bash
# build the experimental tiny images for the legacy Ubiquiti XM boards
make MAINTARGET=ath79 SUBTARGET=tiny
```

This target is also built automatically in the nightly/release CI alongside the
other ath79 images. Because it shares the OpenWrt `ath79/tiny` subtarget, the
output images land in `firmware/targets/ath79/tiny/` (a symlink into
`openwrt/bin/targets/ath79/tiny/`). Pick the image matching your board, for
example `*ubnt_nanostation-m*-squashfs-factory.bin` for a first-time flash from
the stock Ubiquiti firmware, or `*-squashfs-sysupgrade.bin` to upgrade a node
that is already running OpenWrt/AREDN.

### How to build prior builds of AREDN®

Prior AREDN® images can be rebuilt.  Replace one of the following after
the "cd aredn" command above:

AREDN® release 3.25.5.0

```
git checkout 3.25.5.0
```

AREDN® release 3.25.2.0

```
git checkout 3.25.2.0
```

AREDN® release 3.24.10.0

```
git checkout 3.24.10.0
```

AREDN® release 3.24.6.0

```
git checkout 3.24.6.0
```

AREDN® release 3.24.4.0

```
git checkout 3.24.4.0
```

AREDN® release 3.23.12.0

```
git checkout 3.23.12.0
```

AREDN® release 3.23.8.0

```
git checkout 3.23.8.0
```

AREDN® release 3.23.4.0

```
git checkout 3.23.4.0
```

AREDN® release 3.22.12.0

```
git checkout 3.22.12.0
```

AREDN® release 3.22.8.0

```
git checkout 3.22.8.0
```

AREDN® release 3.22.6.0

```
git checkout 3.22.6.0
```

AREDN® release 3.22.1.0

```
git checkout 3.22.1.0
```

AREDN® release 3.21.4.0

```
git checkout 3.21.4.0
```

AREDN® release 3.20.3.1

```
git checkout 3.20.3.1
```

AREDN® release 3.20.3.0

```
git checkout 3.20.3.0
```

AREDN® release 3.19.3.0

```
git checkout 3.19.3.0
```

AREDN® release 3.18.9.0

```
git checkout 3.18.9.0
```

AREDN® release 3.16.2.0

```
git checkout 3.16.2.0
```

AREDN® release 3.16.1.1

```
git checkout 3.16.1.1-make
```

AREDN® build 176

```
git checkout 91ee867
```

Return to most current changes

```
git checkout main
```

### Directory Layout

```
Included in the git Repo:
config.mk    <- build settings
openwrt.mk   <- which openwrt repo and branch/tag/commit to use
feeds.conf/  <- custom package feeds (edit to point to your clone of aredn_packages)
files/       <- file system in AREDN® created images, most customizations go here
patches/     <- patches to openwrt go here
scripts/     <- tests and other scripts called from the build
configs/     <- definitions of features in the devices' kernel and what packages to include
Makefile     <- the build definition
README.md    <- this file

Created by the build:
openwrt/     <- cloned openwrt repository
firmware/    <- the build will place the images here
results/     <- code checks and other test results in jUnit xml format
```

### Patches with quilt

The patches directory contains quilt patches applied on top of the openwrt git repo defined in config.mk.

If a patch is not yet included upstream, it can be placed in the `patches` directory with
the `quilt` tool. Please configure `quilt` as described in [OpenWrt Quilt](https://openwrt.org/docs/guide-developer/build-system/use-patches-with-buildsystem).  

#### Add, modify or delete a patch

Switch to the openwrt directory:

```bash
cd openwrt
```
Now you can use the `quilt` commands.

##### Example: add a patch

```bash
quilt push -a                 # apply all patches
quilt new 008-awesome.patch   # tell quilt to create a new patch
quilt edit somedir/somefile1  # edit files
quilt edit somedir/somefile2
quilt refresh                 # creates/updates the patch file
```

## Submitting new features and patches to AREDN®

The high level steps to submit to this repository https://github.com/aredn/aredn are:

1) create a github account and 'fork' this repo
2) git commit a change into your fork, e.g. https://github.com/ae6xe/aredn
3) create a pull request for https://github.com/aredn/aredn to consider your change
