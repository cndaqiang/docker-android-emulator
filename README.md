# Docker Android Emulator

[![Docker Image CI](https://github.com/cndaqiang/docker-android-emulator/actions/workflows/docker-image.yml/badge.svg)](https://github.com/cndaqiang/docker-android-emulator/actions/workflows/docker-image.yml)
[![DeepSource](https://deepsource.io/gh/cndaqiang/docker-android-emulator.svg/?label=active+issues&show_trend=true&token=dsp_2d663e311e8b28513abf8582687b015394a2)](https://deepsource.io/gh/cndaqiang/docker-android-emulator/?ref=repository-badge)
![Docker Pulls](https://img.shields.io/docker/pulls/cndaqiang/docker-android-emulator)

This repository contains Docker images for building Android emulator. The images are based on [HQarroum/docker-android](https://github.com/HQarroum/docker-android) and have been forked and modified to better suit my needs. The modified version can be found at [cndaqiang/docker-android-emulator](https://github.com/cndaqiang/docker-android-emulator).

You can pull the Docker image from Docker Hub:[cndaqiang/docker-android-emulator](https://hub.docker.com/r/cndaqiang/docker-android-emulator).


## Modifications

This fork includes the following modifications:
* **`-e PARTITION=16384`**:
   - **Meaning**: Sets the environment variable `PARTITION` to `16384`.
   - **Description**: This environment variable is typically used to specify the partition size for the emulator. In Android emulators, the partition size determines the storage capacity of the virtual device (in MB). `16384` represents a partition size of 16GB. This value will affect the amount of storage available within the virtual device, such as for app installations and data storage.
* **`EMULATOR_ARGS`**:
   - **Meaning**: A string containing any additional arguments you want to pass to the emulator.
   - **Description**: This environment variable allows you to specify custom parameters when starting the emulator. For example:
     - `-timezone Asia/Shanghai`

The emulator is started with the following command, which incorporates both default and custom parameters:
```
emulator \
  -avd android \
  -gpu "$GPU_MODE" \
  -memory $OPT_MEMORY \
  -partition-size $PARTITION \
  -no-boot-anim \
  -cores $OPT_CORES \
  -ranchu \
  $AUTH_FLAG \
  -no-window \
  -no-snapshot  \
  $EMULATOR_ARGS || update_state "ANDROID_STOPPED"
```

## Why ARM64 is not supported:

Currently, on ARM64 devices, the `sdkmanager` does not include the `emulator` component, making it impossible to create an emulator image on such platforms. As a result, this container can only be run on `amd64` platforms. More See [build.arm64.faild](build.arm64.faild.md)

Additionally, the ARM64 emulator support is not available because the QEMU2 emulator does not support the ARM64 CPU architecture on an x86_64 host. This limitation prevents the use of the Android emulator for ARM64 images, even on systems that support ARM64-based containers.

For ARM64 devices, we provide only a minimal version of the image that excludes the Android SDK and emulator, useful primarily as a base image for other purposes.

## Credits

This project is a fork of [HQarroum/docker-android](https://github.com/HQarroum/docker-android). I would like to thank the original author for providing a great foundation and the base Dockerfiles that made it possible to easily create these custom Android environments.

## Example:

```
docker run -d --device /dev/kvm -p 5555:5555 -v androiddata:/data -e PARTITION=24576 -e MEMORY=6144 -e CORES=2 --name docker-android-emulator cndaqiang/docker-android-emulator:api-33
```

## Debug:
* 也许不同时间sdkmanager安装的内容不一样
* 我最新创建的镜像比原始的[HQarroum/docker-android](https://github.com/HQarroum/docker-android) 要大好几G
* 而且最新创建的, 安装wzry等软件会出错
* 而我基于[HQarroum/docker-android](https://github.com/HQarroum/docker-android) 二次修改的镜像[cndaqiang/docker-android](https://github.com/cndaqiang/docker-android)就可以安装软件.
* 真麻了
* 而且这是基于x86的安卓模拟器, 适用的app真的有限.

```
docker stop docker-android-emulator; docker volume rm androiddata
docker run -d --rm --device /dev/kvm -p 5555:5555 -v androiddata:/data -e PARTITION=24576 -e EMULATOR_ARGS="-timezone Asia/Shanghai" -e MEMORY=6144 -e CORES=4 --name docker-android-emulator cndaqiang/docker-android-emulator:api-33 
docker run -it --rm --device /dev/kvm --entrypoint /bin/bash -p 5555:5555 -v androiddata:/data -e PARTITION=24576 -e EMULATOR_ARGS="-timezone Asia/Shanghai" -e MEMORY=6144 -e CORES=4 --name docker-android-emulator cndaqiang/docker-android-emulator:api-33 

# my mod version
docker stop docker-android; docker volume rm data
docker run -d --rm --device /dev/kvm -p 5555:5555 -v data:/data -e PARTITION=24576 -e EMULATOR_ARGS="-timezone Asia/Shanghai" -e MEMORY=6144 -e CORES=4 --name docker-android cndaqiang/docker-android:api-33
#
```

```
docker pull cndaqiang/docker-android-emulator:api-27arm64v8a

docker run -it --rm  --entrypoint /bin/bash  --device /dev/kvm -p 5555:5555 -v androiddata:/data -e PARTITION=24576 -e EMULATOR_ARGS="-timezone Asia/Shanghai" -e MEMORY=6144 -e CORES=4 --name docker-android-emulator cndaqiang/docker-android-emulator:api-27arm64v8a
docker run -it --rm --device /dev/kvm -p 5555:5555 -v androiddata:/data -e PARTITION=24576 -e EMULATOR_ARGS="-timezone Asia/Shanghai" -e MEMORY=6144 -e CORES=4 --name docker-android-emulator cndaqiang/docker-android-emulator:api-27arm64v8a
```

---


<br /><br /><br />
<p align="center">
  <img width="400" src="assets/icon.png" />
</p><br /><br />

# docker-android
> A minimal and customizable Docker image running the Android emulator as a service.

[![Docker Image CI](https://github.com/HQarroum/docker-android/actions/workflows/docker-image.yml/badge.svg)](https://github.com/HQarroum/docker-android/actions/workflows/docker-image.yml)
[![DeepSource](https://deepsource.io/gh/HQarroum/docker-android.svg/?label=active+issues&show_trend=true&token=JTfGSHolIiMj0WNfv2ES0I6X)](https://deepsource.io/gh/HQarroum/docker-android/?ref=repository-badge)
![Docker Pulls](https://img.shields.io/docker/pulls/halimqarroum/docker-android)

Current version: **1.1.0**

## 📋 Table of contents

- [Features](#-features)
- [Description](#-description)
- [Usage](#-usage)
- [See also](#-see-also)

## 🔖 Features

- Minimal Alpine based image bundled with the Android emulator and KVM support.
- Bundles the Java Runtime Environment 11 in the image.
- Customizable Android version, device type and image types.
- Port-forwarding of emulator and ADB on the container network interface built-in.
- Emulator images are wiped each time the emulator re-starts.
- Runs headless, suitable for CI farms. Compatible with [`scrcpy`](https://github.com/Genymobile/scrcpy) to remotely control the Android screen.

## 🔰 Description

The focus of this project is to provide a size-optimized Docker image bundled with the minimal amount of software required to expose a fully functionning Android emulator that's remotely controllable over the network. This image only contains the Android emulator itself, an ADB server used to remotely connect into the emulator from outside the container, and QEMU with `libvirt` support.

You can build this image without the Android SDK and without the Android emulator to make the image smaller. Below is a size comparison between some of the possible build variants.

Variant                   |   Uncompressed   |  Compressed  |
------------------------- | ---------------- | ------------ |
API 33 + Emulator         |      5.84 GB     |    1.97 GB   |
API 32 + Emulator         |      5.89 GB     |    1.93 GB   |
API 28 + Emulator         |      4.29 GB     |    1.46 GB   |
Without SDK and emulator  |      414 MB      |    138 MB    |

## 📘 Usage

By default, a build will bundle the Android SDK, platform tools and emulator with the image.

with docker-compose:

```bash
docker compose up android-emulator
```

or with GPU acceleration
```bash
docker compose up android-emulator-cuda
```

or for example with GPU acceleration and google playstore
```bash
docker compose up android-emulator-cuda-store
```

with only docker


```bash
docker build -t android-emulator .
```

## Keys

To run google_apis_playstore image, you need to have same adbkey between emulator and client.

You can generate one by running `adb keygen adbkey`, that generates 2 files - adbkey and adbkey.pub.

override them inside ./keys directory.

### Running the container

Once the image is built, you can mount your KVM driver on the container and expose its ADB port.

> Ensure 4GB of memory and at least 8GB of disk space for API 33.

```bash
docker run -it --rm --device /dev/kvm -p 5555:5555 android-emulator
```

### Save data/storage after restart (wipe)

All avd save in docker dir `/data`, name for avd is `android`

```bash
docker run -it --rm --device /dev/kvm -p 5555:5555 -v ~/android_avd:/data android-emulator
```

### Connect ADB to the container

The ADB server in the container will be spawned automatically and listen on all interfaces in the container. After a few seconds, once the kernel has booted, you will be able to connect ADB to the container.

```bash
adb connect 127.0.0.1:5555
```

Additionally, you can use [`scrcpy`](https://github.com/Genymobile/scrcpy) to control the screen of the emulator remotely. To do so, you simply have to connect ADB and run it locally.

> By default, the emulator runs with a Pixel preset (1080x1920).

```bash
scrcpy
```

<br />
<table>
  <tr>
    <td>
      <img width="260" src="assets/screenshot.png" />
    </td>
    <td>
      <img width="260" src="assets/screenshot-2.png" />
    </td>
    <td>
      <img width="260" src="assets/screenshot-3.png" />
    </td>
  </tr>
</table>
<br />

### Customize the image

It is possible to customize the API level (Android version) and the image type (Google APIs vs PlayStore) when building the image.

> By default, the image will build with API 33 with support for Google APIs for an x86_64 architecture.

This can come in handy when integrating multiple images as part of a CI pipeline where an application or a set of applications need to be tested against different Android versions. There are 2 variables that can be specified at build time to change the Android image.

- `API_LEVEL` - Specifies the [API level](https://apilevels.com/) associated with the image. Use this parameter to change the Android version.
- `IMG_TYPE` - Specifies the type of image to install.
- `ARCHITECTURE` Specifies the CPU architecture of the Android image. Note that only `x86_64` and `x86` are actively supported by this image.

The below example will install Android Pie with support for the Google Play Store.

```bash
docker build \
  --build-arg API_LEVEL=28 \
  --build-arg IMG_TYPE=google_apis_playstore \
  --build-arg ARCHITECTURE=x86 \
  --tag android-emulator .
```

### Variables
#### Default variables

#### Disable animation
DISABLE_ANIMATION=false

#### Disable hidden policy
DISABLE_HIDDEN_POLICY=false

#### Skip adb authentication
SKIP_AUTH=true

#### Memory for emulator
MEMORY=8192

#### Cores for emulator
CORES=4

### Mount an external drive in the container

It might be sometimes useful to have the entire Android SDK folder outside of the container (stored on a shared distributed filesystem such as NFS for example), to significantly reduce the size and the build time of the image.

To do so, you can specify a specific argument at build time to disable the download and installation of the SDK in the image.

```bash
docker build -t android-emulator --build-arg INSTALL_ANDROID_SDK=0 .
```

> You will need mount the SDK in the container at `/opt/android`.

```bash
docker run -it --rm --device /dev/kvm -p 5555:5555 -v /shared/android/sdk:/opt/android/ android-emulator
```

### Pull from Docker Hub

Different pre-built images of `docker-android` exist on [Docker Hub](https://hub.docker.com/r/halimqarroum/docker-android). Each image variant is tagged using its the api level and image type. For example, to pull an API 33 image, you can run the following.

```bash
docker pull halimqarroum/docker-android:api-33
```

## 👀 See also

- The [alpine-android](https://github.com/alvr/alpine-android) project which is based on a different Alpine image.
- The [docker-android](https://github.com/budtmo/docker-android) project which offers a WebRTC interface to an Android emulator.
