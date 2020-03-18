# TBS5927 Professional DVB-S2 TV Tuner USB

The TBS 5927 is a USB demodulator, which will receive data from satellite and
will output data to the host over USB. The host, in turn, is responsible for
configuring the modem using specific DVB-S2 tools. Hence, next, you need to
prepare the host for driving the TBS 5927.

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [TBS5927 Professional DVB-S2 TV Tuner USB](#tbs5927-professional-dvb-s2-tv-tuner-usb)
    - [Hardware Connections](#hardware-connections)
    - [TBS 5927 Drivers](#tbs-5927-drivers)
    - [Configuration Helper](#configuration-helper)
    - [Host Requirements](#host-requirements)
    - [Launch](#launch)
    - [Docker](#docker)
    - [Further Information](#further-information)
    - [Building dvb-apps from source](#building-dvb-apps-from-source)

<!-- markdown-toc end -->

## Hardware Connections

The TBS 5927 should be connected as follows:

![TBS5927 Connections](img/usb_connections.png?raw=true "TBS5927 Connections")

- Connect the LNB directly to "LNB IN" of the TBS 5927 using a coaxial cable (an
  RG6 cable is recommended).
- Connect the TBS 5927's USB interface to your computer.

## TBS 5927 Drivers

Before anything else, note that specific device drivers are required in order to
use the TBS5927. Please, do note that driver installation can cause corruptions
and, therefore, it is safer and **strongly recommended** to use a virtual
machine for running the TBS5927. If you do so, please note that all commands
recommend in the remainder of this page are then supposed to be executed in the
virtual machine.

Next, install the drivers for the TBS 5927. A helper script is available in the
`util` directory from the root of this repository:

Run:

```
cd util/
./tbsdriver.sh
```

Once the script completes the installation, reboot the virtual machine.

## Configuration Helper

Some configurations depend on your specific setup. To obtain detailed
instructions, please run the configuration helper as follows:

```
blocksat-cli cfg
```

Furthermore, in order to run the demodulator, it is necessary to have the
so-called *channel configuration file*. The above configuration helper will
generate this configuration file for you.

Also, the configuration helper will print out all instructions that follow.

## Host Requirements

First, install all pre-requisites:

On Ubuntu/Debian:

```
sudo apt apt update
sudo apt install python3 iproute2 iptables dvb-apps dvb-tools
```

On Fedora:

```
sudo dnf update
sudo dnf install python3 iproute iptables dvb-apps v4l-utils
```

> NOTE: `iproute`/`iproute2` and `iptables` are used in order to ensure `ip` and
> `iptables` tools are available.

## Launch

Finally, launch the DVB-S2 interface by running:

```
blocksat-cli usb
```

This script will set an arbitrary IP address to the network interface that is
created in Linux in order to handle the IP traffic received via the satellite
link. To define a specific IP instead, run the above with `--ip target_ip`
argument, where `target_ip` is the IP of interest.

> NOTE: root privileges are required in order to configure firewall and *reverse
> path (RP) filtering*, as well as accessing the adapter at `/dev/dvb`. You will
> be prompted to accept or refuse the firewall and RP configurations that are
> executed as root.

At this point, if your dish is already correctly pointed, you should be able to
start receiving data in Bitcoin FIBRE. Please follow the [instructions for FIBRE
configuration](fibre.md). If your antenna is not pointed yet, please follow the
[antenna alignment guide](antenna-pointing.md).

## Docker

There is a Docker image available in this repository for running the Linux USB
setup. Please refer to instructions in the [Docker guide](../docker/README.md).

## Further Information

- [User guide](https://www.tbsiptv.com/download/tbs5927/tbs5957_user_guide.pdf)
- [Datasheet](https://www.tbsiptv.com/download/tbs5927/tbs5927_professtional_dvb-S2_TV_Tuner_USB_data_sheet.pdf)
- [Drivers](https://github.com/tbsdtv/linux_media/wiki).

## Building dvb-apps from source

If instead of installing `dvb-apps` from binary packages you desire to build it
from source, here are some instructions:

```
hg clone http://linuxtv.org/hg/dvb-apps
cd dvb-apps
make
sudo make install
```

For Kernel version greater than or equal to 4.14, [this
solution](https://gist.github.com/Kaeltis/d87dc76fc604f8b3373231dcd2d76568) can
be used to complete the compilation.

More information can be found at
[linuxtv's wiki page](https://www.linuxtv.org/wiki/index.php/LinuxTV_dvb-apps).
