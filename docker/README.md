# Running on Docker

There are two Docker images in this repository:

- `usb.docker`: should be used for Linux USB receiver setups and it also works
  with standalone demodulators.
- `sdr.docker`: should be used for SDR-based setups.

If using a USB demodulator, however, the drivers need to be installed on the
Docker host (not the container).

## Linux USB Container

First, build the image:

```
docker build -t blockstream/blocksat-usb -f usb.docker ..
```

Run the container while sharing the DVB network interfaces `dvb0_0` and `dvb0_1`
(rename as needed):

```
docker run --rm \
	--device=/dev/dvb0_0 \
	--device=/dev/dvb0_1 \
	--network=host \
	--cap-add=NET_ADMIN \
	--cap-add=SYS_ADMIN \
	-it blockstream/blocksat-usb \
	launch
```

After running, configure reverse path filters by running the following from the
host (not from the container):

```
sudo ./blocksat.py rp -i dvb0_0
```

## SDR Container

First, build the image:

```
docker build -t blockstream/blocksat-sdr -f sdr.docker ..
```

Next, run the container as follows:

```
docker run --rm \
	--privileged \
	-v /dev/bus/usb:/dev/bus/usb \
	-it blockstream/blocksat-sdr
```

Note **privileged mode** is used in order to allow the container to access the
RTL-SDR USB device of the host.

> On a Mac OSX host, you will need to set up a
> [docker-machine](https://docs.docker.com/machine/) in order share the SDR USB
> device. Once this docker-machine is active, you can share the USB device via
> the settings of the [machine
> driver](https://docs.docker.com/machine/drivers/). Then, run the above `docker
> run` command normally.
