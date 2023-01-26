Orb consists of two major components:

1. The **Control Plane**—comprised of microservices, communication systems, databases, etc.—deploys to a 
central location (usually a cloud environment on Kubernetes).
1. The **[Orb Agent](/documentation/running_orb_agent)**—a lightweight observability agent—deploys to all the infrastructure you wish to monitor.

!!! info
    
    The instructions below are for installing the **Control Plane**. If you just need to install the **Orb Agent** (`orb-agent`), [see these instructions instead](/documentation/running_orb_agent).

The **Control Plane** can be [self-hosted](#self-host), or you can use our free [Orb SaaS](#orblive) service. Self-hosting gives you full privacy and control but is more complex. On the other hand, our SaaS gets you up and running quickly since you only need to create a free account on orb.live and then install the **Orb Agent** to your infrastructure.


## orb.live 
The Orb SaaS platform ([**orb.live**](http://orb.live)) is now in active development. This free service allows you to enjoy the benefits of the Orb platform without having to run your own control plane.

If you need to install the **Orb Agent** to be used with orb.live, [see these instructions](/documentation/running_orb_agent).

## self-host

Start by cloning the orb project in your local environment using the following command:

```
git clone git@github.com:orb-community/orb.git
```

<br>
For local/test/development control plane installs or for production deployments, it's required a Kubernetes cluster, which is implemented with helm chart and kind.<br>
<br>
**Follow the steps from [orb/kind](https://github.com/orb-community/orb/blob/develop/kind/README.md) to set up a local k8s cluster and deploy Orb.**


Upon successful installation, visit our [Getting Started](https://orb.community/getting_started) section to keep moving forward with Orb.

!!! warning
    
    To run your agent using the self-hosted control plane, disable TLS verification using the enviromental variable below in your provisioning command:
    
    ``` shell
    -e ORB_TLS_VERIFY=false
    ```

    Check an example [here](/documentation/running_orb_agent/#sample-provisioning-commands).

!!! bug

    Is the installation/deployment not working correctly? Found a bug? Come talk to us [live on Slack](https://netdev.chat/) in the `#orb` channel,
    or [file a GitHub issue here](https://github.com/orb-community/orb/issues/new/choose).