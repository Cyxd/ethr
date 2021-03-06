# Ethr [![Build Status](https://travis-ci.org/Microsoft/ethr.svg?branch=master)](https://travis-ci.org/Microsoft/ethr)

Ethr is a cross platform network performance measurement tool written in golang. The goal of this project is to provide a native tool for comprehensive network performance measurements of bandwidth, connections/s, packets/s, latency, loss & jitter, across multiple protocols such as TCP, UDP, HTTP, HTTPS, and across multiple platforms such as Windows, Linux and other Unix systems.

<p align="center">
  <img alt="Ethr server in action" src="https://user-images.githubusercontent.com/44273634/49815752-506f0000-fd21-11e8-954e-d587e79c5d85.png">
</p>

Ethr takes inspiration from existing open source network performance tools and builds upon those ideas. For Bandwidth measurement, it is similar to iPerf3, for TCP & UDP traffic. iPerf3 has many more options for doing such as throttled testing, richer feature set, while Ethr has support for multiple threads, that allows it to scale to 1024 or even higher number of connections, multiple clients communication to a single server etc. For latency measurements, it is similar to latte on Windows or sockperf on Linux.

Ethr provides more test measurements as compared to other tools, e.g. it provides measurements for bandwidth, connections/s, packets/s, latency, and TCP connection setup latency, all in a single tool. In the future, there are plans to add more features (hoping for others to contribute) as well as more protocol support to make it a comprehensive tool for network performance measurements.

Ethr is natively cross platform, thanks to golang, as compared to compiling via an abstraction layer like cygwin that may limit functionality. It hopes to unify performance measurement by combining the functionality of tools like iPerf3, ntttcp, psping, sockperf, and latte and offering a single tool across multiple platforms and multiple protocols.

# Download

```
For Windows 10: https://github.com/Microsoft/Ethr/files/2640289/ethr.zip
For Ubuntu: https://github.com/Microsoft/Ethr/files/2640288/ethr.gz
For ArchLinux: https://aur.archlinux.org/packages/ethr
```

# Installation

Note: go version 1.10 or higher is required building it from the source.

## Building from Source

```
git clone https://github.com/Microsoft/ethr.git
cd ethr
dep ensure -v
go build
```

## Docker

Build image using command: 
```
docker build -t microsoft/ethr .
```

Make binary:

**Linux**
```
docker run -e GOOS=linux -v $(pwd):/out microsoft/ethr make build-docker
```

**Windows**

```
docker run -e BINARY_NAME=ethr.exe -e GOOS=windows -v $(pwd):/out microsoft/ethr make build-docker
```

**OS X**
```
docker run -e BINARY_NAME=ethr -e GOOS=darwin -v $(pwd):/out microsoft/ethr make build-docker
```

## Using go get

```
go get github.com/Microsoft/ethr
```

## Using ArchLinux AUR

Assuming you are using [`yay`](https://aur.archlinux.org/packages/yay/) (https://github.com/Jguer/yay):

```
yay -S ethr
```

# Usage

## Simple Usage
Help:
```
ethr -h
```

Server:
```
ethr -s
```

Server with Text UI:
```
ethr -s -ui
```

Client:
```
ethr -c <server ip>
```

Example:
```
// Start server
ethr -s

// Start client for default (bandwidth) test measurement using 1 thread
ethr -c localhost

// Start connections/s test using 64 threads
ethr -c localhost -t c -n 64
```

## Complete Command Line
### Common Parameters
```
-h                        Help
-no                       Disable logging to a file
-o <filename>             Log to the file specified by filename
                          Default: ethrs.log for server, ethrc.log for client, ethrxc.log for external client mode
-debug                    Log debug output
-ports <string>           Use custom port numbers instead of default ones
                          Format: "Key1=Value, Key2=value, ..."
                          Default: "control=8888, tcp=9999, udp=9999, http=9899, https=9799"
                          Control is used for control channel communication for ethr.
                          For protocols, base port is specified by value and other ports are calculated.
                          Example: tcp=9999 means port for Bandwidth: 9999, CPS: 9998, PPS: 9997, Latency: 9996
                          Note: Same ports must be used on client and server
                          Note: This option is not valid for external client mode
-4                        Use only IP v4 version
-6                        Use only IP v6 version
```
### Server Parameters
```
-s                        Server mode
-ui                       Display text UI
```
### Client Parameters
```
-c <server>                   Client mode, connect to name or IP specified by server
-t <b|c|p|l>                  Test to be done, b: bandwidth, c: connections/s, p: packets/s, l: latency
                              Default is bandwidth test
-p <tcp|udp|http|https|icmp>  Protocol to use, default is TCP
-n <number>                   Number of sessions/threads to use
-l <number>                   Buffer size to use for each request
-i <number>                   Number of iterations for latency test
-d <duration>                 Duration for the test run, for example, 10s, 4m, 5h etc. 0 - forever, default: 10s
```
### External Server Parameters
```
-m x                      External server mode - In this mode, Ethr only supports TCP, listening on 9999
                          Any client can connect to Ethr in this mode. Ethr can also receive data in this mode
                          This mode is useful for running Ethr server on multiple instances behind a load balancer,
                          and traffic from clients getting distributed by load balancer to these instances
-s                        Server mode
-ui                       Display text UI
```
### External Client Mode
```
-m x                          Set mode to External client mode
-c <destination>              External client mode, connect to destination specified by host:port
                              Example: -x www.microsoft.com:443 or -x 10.1.0.4:22 etc.
-t <b|cl>                     Test to be done, b: bandwidth, cl: connection latency                              
-d <duration>                 Duration for the test run, for example, 10s, 4m, 5h etc. 0 - forever, default: 10s
-g <duration>                 Gap (or interval) between successive connection setup (for connection latency test)
```

# Status

Protocol  | Bandwidth | Connections/s | Packets/s | Latency
------------- | ------------- | ------------- | ------------- | -------------
TCP  | Yes | Yes | No | Yes
UDP  | Yes | NA | Yes | No
HTTP | Yes | No | No | No
HTTPS | Yes | No | No | No
ICMP | No | NA | No | No

# Platform Support

**Windows**

Tested: Windows 10, Windows 7 SP1

Untested: Other Windows versions

**Linux**

Tested: Ubuntu Linux 18.04.1 LTS, OpenSuse Leap 15

Untested: Other Linux versions

**OSX**

Tested: OSX is tested by contributors

**Other**

No other platforms are tested at this time

# Todo List

Todo list work items are shown below. Contributions are most welcome for these work items or any other features and bugfixes.

* Test Ethr on other Windows versions, other Linux versions, FreeBSD and other OS
* Support for UDP bandwidth & latency testing
* Support for HTTPS bandwidth, latency, requests/s
* Support for HTTP latency and requests/s
* Support for ICMP bandwidth, latency and packets/s

# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
