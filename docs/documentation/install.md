Orb consists of two major components:

1. The **Control Plane**—comprised of microservices, communication systems, databases, etc.—deploys to a 
central location (usually a cloud environment on Kubernetes).
1. The **[Orb Agent](/documentation/running_orb_agent)**—a lightweight observability agent—deploys to all the infrastructure you wish to monitor.

!!! info
    
    The instructions below are for installing the **Control Plane**. If you just need to install the **Orb Agent** (`orb-agent`), [see these instructions instead](/documentation/running_orb_agent).

The **Control Plane** can be [self-hosted](#self-host), or you can use our free [Orb SaaS](#orblive) service. Self-hosting gives you full privacy and control but is more complex. On the other hand, our SaaS gets you up and running quickly since you only need to create a free account on orb.live and then install the **Orb Agent** to your infrastructure.


## Orb.live 
The Orb SaaS platform ([**orb.live**](http://orb.live)) is now in active development. This free service allows you to enjoy the benefits of the Orb platform without having to run your own control plane.

If you need to install the **Orb Agent** to be used with orb.live, [see these instructions](/documentation/running_orb_agent).

## Self-host

Start by cloning the orb project in your local environment using the following command:

```
git clone git@github.com:orb-community/orb.git
```

<br>
A Kubernetes environment is required for both development environments and production deployment. For development, the [kind tool](https://github.com/orb-community/orb/blob/develop/kind/README.md) is preferred. For production deployments, use [orb-helm](https://github.com/orb-community/orb-helm).<br>

#### Port Requirements

In order for the agent to be able to communicate with the control plane, the following outgoing ports must be permitted:

| Protocol |   Port number   | 
|:--------:|:---------------:|
|   MQTT   |      8883       |
|   HTTP   | 443<sup>*</sup> |

> <sup>*</sup> port 443 is only required if you want the agent to [auto-provision](/documentation/running_orb_agent/#advanced-auto-provisioning-setup)



<br><br>
**Follow the steps from [orb/kind](https://github.com/orb-community/orb/blob/develop/kind/README.md) to set up a local k8s cluster and deploy Orb.**


Upon successful installation, visit our [Getting Started](https://orb.community/getting_started) section to keep moving forward with Orb.

!!! warning
    
    To connect an agent to the self-hosted development control plane, disable TLS verification using the environmental variable below in your provisioning command:
    
    ``` shell
    -e ORB_TLS_VERIFY=false
    ```

    See example [here](/documentation/running_orb_agent/#sample-provisioning-commands).

!!! bug

    Is the installation/deployment not working correctly? Found a bug? Come talk to us [live on Slack](https://netdev.chat/) in the `#orb` channel,
    or [file a GitHub issue here](https://github.com/orb-community/orb/issues/new/choose).

#### Setting up a prometheus sink


In addition to deploying Orb, you can optionally set up a Prometheus instance to act as a metrics data Sink. You may wish to do this if you do not already have an existing time series database to send Orb Policy metric output to, and would like to host your own.
 
=== "prometheus.yml"
    ```yaml
    global:
      scrape_interval: 5m
      scrape_timeout:  1m        
    scrape_configs:
      - job_name: "prometheus"
        basic_auth:
          username: 'MYUSER'
          password: 'MYPASSWORD'
    ```

=== "web-config.yml"
    You will need to generate password using htpasswd [as described in the prometheus documentation](https://prometheus.io/docs/guides/basic-auth/)
    ```yaml
    basic_auth_users:
      admin: BASIC-AUTH-PASSWORD
    ```

=== "docker-compose.yml"
    ```yaml
    version: "3"
    services:
      prometheus:
        image: prom/prometheus:latest
        ports:
          - 9090:9090
        volumes:
          - "/storage-docker/prometheus/prometheus.yaml:/etc/prometheus/prometheus.yml"
          - "/storage-docker/prometheus/web-config.yaml:/etc/prometheus/web-config.yml"
        command:
          - '--config.file=/etc/prometheus/prometheus.yml'
          - '--web.config.file=/etc/prometheus/web-config.yml'
          - '--enable-feature=remote-write-receiver'
      grafana:
        image: grafana/grafana:latest
        ports:
          - 3000:3000
        depends_on:
          - prometheus
    ```