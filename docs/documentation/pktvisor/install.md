
# Running pktvisor

## Docker

Get started quickly with pktvisor via the [public Docker image](https://hub.docker.com/r/ns1labs/pktvisor). The image contains the collector agent (`pktvisord`), the command-line UI (`pktvisor-cli`), and the pcap and dnstap file analyzer (`pktvisor-reader`). You will specify which tool to operate when running the container.

- *Pull the container*


        docker pull ns1labs/pktvisor
 

Or use `ns1labs/pktvisor:latest-develop` to get the latest development version.

- *Start the collector agent*


        docker run --net=host -d ns1labs/pktvisor pktvisord eth0

This will start in the background and stay running. Note that the final two arguments select `pktvisord` agent and the `eth0` ethernet interface for packet capture. You may substitute `eth0` for any known interface on your device. _Note that this step requires Docker host networking_ to observe traffic outside the container, and that [currently only Linux supports host networking](https://docs.docker.com/network/host/).

 If the container does not stay running, check the `docker logs` output.

- *Run the command-line UI*


        docker run -it --rm --net=host ns1labs/pktvisor pktvisor-cli

After the agent is running, you can observe results locally with the included command-line UI. This command will run the UI (`pktvisor-cli`) in the foreground and exit after pressing `Ctrl+C`. It connects to the running agent locally using the built-in REST API.

## Linux Static Binary (AppImage, x86_64)

You may also use the Linux all-in-one binary, built with [AppImage](https://appimage.org/), which is available for download [on the Releases page](https://github.com/ns1labs/pktvisor/releases). It is designed to work on all modern Linux distributions and does not require installation or any other dependencies.

```shell
curl -L http://pktvisor.com/download -o pktvisor-x86_64.AppImage
chmod +x pktvisor-x86_64.AppImage
./pktvisor-x86_64.AppImage pktvisord -h
```

For example, to run the agent on ethernet interface `eth0`:

```
./pktvisor-x86_64.AppImage pktvisord eth0
```

The AppImage contains the collector agent (`pktvisord`), the command-line UI (`pktvisor-cli`), and the pcap and dnstap file analyzer (`pktvisor-reader`). You can specify which tool to run by passing it as the first argument.

For example, to visualize the running agent started above with the pktvisor command-line UI:

```shell
./pktvisor-x86_64.AppImage pktvisor-cli
```

When running the AppImage version of the agent, you may want to use the `-d` argument to daemonize (run in the background), and either the `--log-file` or `--syslog` argument to record logs.

Also see the [Advanced Agent Example](https://pktvisor.dev/docs/#advanced-agent-example).

## Linux Static Binaries (Stand Alone, x86_64)
Finally, pktvisor also provides statically linked, dependency-free Linux binaries for each individual pktvisor tool (`pktvisord`, `pktvisor-cli`, and `pktvisor-reader`). These are the smallest, most compact versions of the binaries.

`pktvisord`:
```shell
curl -L http://pktvisor.com/download/pktvisord -o pktvisord-x86_64
chmod +x pktvisord-x86_64
./pktvisord-x86_64 -h
```

`pktvisor-cli`:
```shell
curl -L http://pktvisor.com/download/cli -o pktvisor-cli-x86_64
chmod +x pktvisor-cli-x86_64
./pktvisor-cli-x86_64 -h
```

`pktvisor-reader`:
```shell
curl -L http://pktvisor.com/download/reader -o pktvisor-reader-x86_64
chmod +x pktvisor-reader-x86_64
./pktvisor-reader-x86_64 -h
```


## Other Platforms

We are working on support for additional operating systems, CPU architectures, and packaging systems. If you do not see your binary available, please see the [Build](https://github.com/ns1labs/pktvisor#build) section to build your own.

If you have a preferred installation method you would like to see supported, [please create an issue](https://github.com/ns1/pktvisor/issues/new).
