# containerd-arm

This repo contains binary releases of [containerd](https://github.com/containerd/containerd) built for arm and arm64.

## Why is this needed?

> The primary argument of the containerd team is that they cannot run e2e tests, however k3s/Rancher, Balena and several other projects/companies already ship containerd binaries to arm devices, showing that this is a viable option.

The upstream containerd team are not providing binaries for arm architectures, and to build them yourself on a SoC device will take a significant amount of time. It's rather unfortunate, but you can pull in the binaries from this repo if you want to save yourself a long wait.

I also provide build instructions for those of you who prefer to do things yourselves.

These binaries are consumed by users of [faasd](https://github.com/openfaas/faasd/).

## Installation

Check that you are running on an arm machine with `uname -a`.

You may need to install pre-reqs using `apt`:

```bash
sudo apt update \
  && sudo apt install -qy \
    git \
    tmux \
    build-essential \
    runc \
    bridge-utils \
    libbtrfs-dev btrfs-tools
```

On some versions of Ubuntu, you may need `btrfs-progs` instead.

On Ubuntu Bionic I was unable to find the `libbtrfs-dev` package.

Fortunately CNI is available for multiple architectures. See [the faasd instructions](https://github.com/openfaas/faasd/blob/master/docs/DEV.md) for how to install CNI for armhf

### 32-bit ARM

Aka armhf for the 32-bit Raspberry Pi OS.

```bash
curl -sSL https://github.com/alexellis/containerd-arm/releases/download/v1.3.5/containerd-1.3.5-linux-armhf.tar.gz | \
  sudo tar -xvf --strip-components=1 -C /usr/local/bin/
```

### 64-bit ARM

For Ubuntu and 64-bit RaspiOS.

```bash
curl -sSL https://github.com/alexellis/containerd-arm/releases/download/v1.3.5/containerd-1.3.5-linux-arm64.tar.gz | \
  sudo tar -xvf --strip-components=1 -C /usr/local/bin/
```

## Building your own binaries

If you want to build from source, install the pre-reqs from above, along with Go 1.13 or later.

See also [BUILDING.md](https://github.com/containerd/containerd/blob/master/BUILDING.md#build-containerd)

```bash
curl -sLS https://get.arkade.dev | sudo sh \
  && sudo arkade system install go
```

Make sure that you've installed the pre-reqs from the "Installation" section.

```bash
mkdir -p $HOME/go/src/github.com/containerd/
cd $HOME/go/src/github.com/containerd/
git clone https://github.com/containerd/containerd

cd containerd

export VER="1.6.4"

git checkout $VER
tmux

export PATH=$PATH:/usr/local/go/bin/
export GOPATH=$HOME/go
time make
```

If you run into an error, check the pre-reqs are present from the top of the README file.

Then extract the binaries, for the releases page:

```bash
export VER="1.6.4"

cd bin/ \
 && tar -czvf $HOME/containerd-$VER-linux-armhf.tar.gz ./

```
