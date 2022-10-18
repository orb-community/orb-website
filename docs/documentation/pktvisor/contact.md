---
hide:
- tags
---
<script async defer src="https://buttons.github.io/buttons.js"></script>

## Contribute
**pktvisor** is an open source project founded at [NS1 Labs](https://ns1.com/labs). Work with us on [GitHub](https://github.com/ns1labs/pktvisor) and star the project to show your interest.

<a class="github-button" href="https://github.com/ns1labs/pktvisor" data-size="large" aria-label="Star ns1labs/pktvisor on GitHub">Star on GitHub</a>

## Contact

We want to hear about your use cases, feature requests, and other feedback. Please open *Pull Requests* against the `develop` branch. If you are considering a larger contribution, please contact us to discuss your design via the following options:

* Sign up to get pktvisor and Orb [updates](https://resources.ns1.com/get-orb-updates)
* File an [issue](https://github.com/ns1labs/pktvisor/issues/new)
* See existing [issues](https://github.com/ns1labs/pktvisor/issues)
* Start a [discussion](https://github.com/ns1labs/pktvisor/discussions)
* Join us on [Slack](https://join.slack.com/t/ns1labs/shared_invite/zt-qqsm5cb4-9fsq1xa~R3h~nX6W0sJzmA)
* Send mail to [info@pktvisor.dev](mailto:info@pktvisor.dev)

See the [NS1 Contribution Guidelines](https://github.com/ns1/community) for more information.

## Build
The main code base is written in clean, modern C++. The `pktvisor-cli` command-line interface is written in Go. The build system requires CMake and the [Conan](https://conan.io/) package manager system.

pktvisor adheres to [semantic versioning](https://semver.org/).

pktvisor is developed and tested on Linux and OSX. A Windows port is in progress. Both x86_64 and ARM architectures are known to function.

### Dependencies
* [Conan](https://conan.io/) C++ package manager
* CMake >= 3.13 (`cmake`)
* C++ compiler supporting C++17

For the list of packages included by conan, see [conanfile.txt](https://github.com/ns1labs/pktvisor/blob/develop/conanfile.txt).

### Building
The general build steps are:
```shell
# clone the repository
git clone https://github.com/ns1labs/pktvisor.git
cd pktvisor
mkdir build && cd build

# configure and handle dependencies 
cmake -DCMAKE_BUILD_TYPE=Release ..

# build and run tests
make all test

# the binaries will be in the build/bin directory
bin/pktvisord --help
```

As development environments can vary widely, please see the [Dockerfile](https://github.com/ns1labs/pktvisor/blob/master/docker/Dockerfile) and [Continuous Integration build file](https://github.com/ns1labs/pktvisor/blob/master/.github/workflows/build.yml) for reference.

## Explore

### Articles
* [Orb Data Sheet: Actionable Edge Observability](https://ns1.com/writable/resources/orb-actionable-edge-observability/ns1-ds-orb.pdf)
* [Using DNS to Minimize Cyber Threat Exposure](https://ns1.com/blog/using-dns-to-minimize-cyber-threat-exposure)
* [Deep Network Traffic Observability with Pktvisor and Prometheus](https://ns1.com/blog/deep-network-traffic-observability-pktvisor-prometheus)
* [Q4 2021 Update on NS1 Labs: pktvisor, Orb, NetBox Cloud](https://ns1.com/blog/an-update-on-open-source-innovation-at-ns1-labs?utm_content=buffer1816e&utm_medium=social&utm_source=twitter&utm_campaign=nstw)
* [Extracting the Signal: Rethinking Network Observability](https://ns1.com/blog/extracting-the-signal-rethinking-network-observability)
* [A Wave of Open Source Innovation at NS1 Labs with Orb and NetBox](https://ns1.com/blog/a-wave-of-open-source-innovation-at-ns1-labs-with-orb-and-netbox)
* [NS1 Launches Innovation Lab to Solve Challenges in Modern Application Delivery and Edge Networking](https://ns1.com/press/ns1-launches-innovation-lab-to-solve-challenges-in-modern-application-delivery-and-edge-networking)
* [NS1 Releases Open Source Tool for Network Visibility, Announces Support for DNS over HTTPS to Flamethrower](https://ns1.com/press/ns1-releases-open-source-tool-for-network-visibility-announces-support-for-dns-over-https-to-flamethrower)

### Conference Presentations
* [PromCon North America: Deep Network Traffic Observability](https://promconna21.sched.com/event/4240ae066bc0fbe9c8c31530af2e5c96), 2021 - [Recording](https://www.youtube.com/watch?v=2yIY0cCJ6Vs), [Slides](https://static.sched.com/hosted_files/promconna21/5c/pktvisor%20PromCon%202021.pdf)
* [ICANN DNS Symposium](https://www.icann.org/ids), 2021 - [Recording](https://drive.google.com/file/d/14sDxmwvMV1wF_1cBUBv3qj29Hen_thea/view), [Slides](https://www.icann.org/en/system/files/files/presentation-day1c-pktvisor-weyrick-25may21-en.pdf)
* [DNS-OARC](https://www.dns-oarc.net/), 2020 - [Recording](https://www.youtube.com/watch?v=PwEOePOvkug) (first talk), [Slides](https://indico.dns-oarc.net/event/34/contributions/783/attachments/774/1328/pktvisor3-OARC-sweyrick.pdf)
* [O'Reilly Velocity San Jose](https://www.oreilly.com/library/view/oreilly-velocity-conference/9781492050582/video325462.html), 2019 - [Recording](https://drive.google.com/file/d/1AjhbUjkXT5saBP6iYIZjoinYsiZ5LDaV/view), [Slides](https://docs.google.com/presentation/d/e/2PACX-1vR984fhii0Pso97RRjSFgZupknwQqf-XMhGuriT8HPHHiLlB1c4SnDtRnJtX66nxYv2GETk4ex81QiU/pub?start=false&loop=false&delayms=3000&slide=id.p1)