---
hide:
- navigation
---

# Install

### Docker

One of the easiest ways to get started with pktvisor is to use
the [public docker image](https://hub.docker.com/r/ns1labs/pktvisor). The image contains the collector
agent (`pktvisord`), the command line UI (`pktvisor-cli`), and the pcap file analyzer (`pktvisor-pcap`). When running
the container, you specify which tool to run.

1. *Pull the container*

```
docker pull ns1labs/pktvisor
``` 

2. *Start the collector agent*

This will start in the background and stay running. Note that the final two arguments select `pktvisord` agent and
the `eth0` ethernet interface for packet capture. You may substitute `eth0` for any known interface on your device.
_Note that this step requires docker host networking_ to observe traffic outside the container, and
that [currently only Linux supports host networking](https://docs.docker.com/network/host/):

```
docker run --net=host -d ns1labs/pktvisor pktvisord eth0
```

If the container does not stay running, check the `docker logs` output.

3. *Run the command line UI*

After the agent is running, you can observe results locally with the included command line UI. This command will run the
UI (`pktvisor-cli`) in the foreground, and exit when Ctrl-C is pressed. It connects to the running agent locally using
the built in [REST API](https://app.swaggerhub.com/apis/ns1labs/pktvisor/3.0.0-oas3).

```
docker run -it --rm --net=host ns1labs/pktvisor pktvisor-cli
```

### Linux Static Binary (AppImage)

You may also use the Linux static binary, built with [AppImage](https://appimage.org/), which is available for
download [on the Releases page](https://github.com/ns1labs/pktvisor/releases). It is designed to work on all modern
Linux distributions and does not require installation or any other dependencies.

```shell
curl -L http://pktvisor.com/download -o pktvisor-x86_64.AppImage
chmod +x pktvisor-x86_64.AppImage
./pktvisor-x86_64.AppImage pktvisord -h
```

For example, to run the agent on ethernet interface `eth0`:

```
./pktvisor-x86_64.AppImage pktvisord eth0
```

The AppImage contains the collector agent (`pktvisord`), the command line UI (`pktvisor-cli`), and the pcap file
analyzer (`pktvisor-pcap`). You can specify which tool to run by passing it as the first argument:

For example, to visualize the running agent started above with the pktvisor command line UI:

```shell
./pktvisor-x86_64.AppImage pktvisor-cli
```

Note that when running the AppImage version of the agent, you may want to use the `-d` argument to daemonize (run in the
background), and either the `--log-file` or `--syslog` argument to record logs.

Also see [Advanced Agent Example](#advanced-agent-example).

### Other Platforms

If you are unable to use the Docker container or the Linux binary, then you will have to build your own executable,
please see the [Build](#build) section below.

If you have a preferred installation method that you would like to see support
for, [please create an issue](https://github.com/ns1/pktvisor/issues/new).