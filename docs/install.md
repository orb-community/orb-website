Orb consists of two major components:

1. The **Control Plane**, which is comprised of microservices, communication systems, databases, etc. and is deployed to a 
central location (usually a cloud environment on Kubernetes).
1. The **[Orb Agent](/docs/#running-orb-agent)**, which is a lightweight observability agent deployed to all the infrastructure you wish to monitor.

!!! info
    
    The instructions below are for installing the **Control Plane**. If you just need to install the **Orb Agent** (`orb-agent`), [see these instructions instead](/docs/#running-orb-agent).

The **Control Plane** may be [self-hosted](#self-host), or you may use the free [Orb SaaS](#orblive) service. Self-hosting gives you full privacy and control but is more complex.
On the other hand, our SaaS allows you to get up and running quickly since you only need to create a free account on orb.live and then install the **Orb Agent** to your infrastructure.

## orb.live 
The Orb SaaS platform ([**orb.live**](http://orb.live)) is now in active development. This free-forever service allows you to enjoy the benefits of the Orb platform without having to run your own control plane.

If you need to install the **Orb Agent** to be used with orb.live, [see these instructions](/docs/#running-orb-agent).

!!! danger

    The Orb SaaS service is still under active development and is not yet production ready! Please use it for non-production, testing purposes only. The service may become unavailable, and your data may be reset without notice.

## Self-host
There are two main deployment methods for those wishing to self-host:

* **Docker Compose** - This option is useful for developer or testing installations, allowing you to run both the Orb Control Plane and the Orb Agent on a single machine.
* **Helm Chart** - This option is intended for production deployments, requiring access to a Kubernetes cluster.

Follow the instructions below after choosing a self-host option. 

!!! tip

    If you're just interested in trying out Orb quickly to see what it's all about, use the Docker Compose method.

### Orb with Docker Compose
The Orb repo contains a full Docker Compose environment, useful both for developers wishing to contribute and casual users looking to try Orb for the first time.

Running Orb with Docker Compose requires you to have a copy of the Orb repo, although it is not necessary to build any software as the compose file will download appropriate versions of the services from Docker Hub.

If you would like to build the software, or you need to troubleshoot your environment, you can find more detailed instructions on [setting up a development environment here](https://github.com/ns1labs/orb/wiki/Development-Environment-Configuration).

#### Requirements

* `git`
* `make`
* [Docker](https://docs.docker.com/engine/install/)
* [Docker Compose](https://docs.docker.com/compose/gettingstarted/) (minimum version 1.29.2)

#### Instructions

```shell
git clone https://github.com/ns1labs/orb.git
```
```shell
cd orb
```
```shell
make run
```

This will pull Orb’s containers from [Docker Hub](https://hub.docker.com/u/ns1labs) and start all services. You may run `docker ps` to confirm all services started successfully.

If you are running on your local machine, then upon success the Orb UI will be available at [https://localhost/](https://localhost/)

!!! bug

    Is the Docker Compose environment not starting correctly? Found a bug? Come talk to us [live on Slack](https://join.slack.com/t/ns1labs/shared_invite/zt-qqsm5cb4-9fsq1xa~R3h~nX6W0sJzmA), or [file a GitHub issue here](https://github.com/ns1labs/orb/issues/new/choose).

### Orb Helm Chart
[Helm](https://helm.sh/) is a package manager for Kubernetes. A Helm Chart is a package that allows you to customize your deployment on Kubernetes.

To configure and deploy an Orb Helm Chart, follow the instructions below.

#### Requirements
* [Helm v3](https://helm.sh/docs/intro/quickstart/)

#### Configuration
This guide assumes installation into namespace `orb`. It requires a HOSTNAME over which you have DNS control. It uses [Let's Encrypt](https://letsencrypt.org/) for TLS certification management.

* cd to working directory `charts/orb`

* Add helm repos for dependencies.



        helm repo add jaegertracing https://jaegertracing.github.io/helm-charts
        helm repo add bitnami https://charts.bitnami.com/bitnami
        helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
        helm repo add jetstack https://charts.jetstack.io
        helm repo update
        helm dependency update


* Create `orb` namespace.

        kubectl create namespace orb

* Create JWT signing key secret.

        kubectl create secret generic orb-auth-service --from-literal=jwtSecret=MY_SECRET -n orb

* Create admin user secrets.

        kubectl create secret generic orb-user-service --from-literal=adminEmail=user@example.com --from-literal=adminPassword=12345678 -n orb

* Deploy [ingres-nginx helm](https://kubernetes.github.io/ingress-nginx/deploy/#using-helm) (to default namespace) with tcp config map configured from helm for 8883 (MQTTS). Note you need to reference both namespace and helm release name here.

        helm install --set tcp.8883=orb/my-orb-nginx-internal:8883 ingress-nginx ingress-nginx/ingress-nginx

* Wait for an external IP to be available.

        kubectl --namespace default get services -o wide -w ingress-nginx-controller

* Choose a HOSTNAME, then point an A record for this hostname to the external IP.

* Deploy [cert manager helm](https://cert-manager.io/docs/installation/helm/) to [secure nginx ingress](https://cert-manager.io/v0.14-docs/tutorials/acme/ingress/).

        helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.5.3 --set installCRDs=true

- Create Issuer CRDs (in the `orb` namespace!)
    - `cp issuers/production-issuer-tpt.yaml issuers/production-issuer.yaml`
    - Edit `issuers/production-issuer.yaml` and change `spec.acme.email` to a real email address.
    - `kubectl create -f issuers/production-issuer.yaml -n orb`

* Install Orb. Replace `my-orb` with your helm release name.

        helm install --set ingress.hostname=HOSTNAME -n orb my-orb .

!!! bug

    Is the Helm deployment not working correctly? Found a bug? Come talk to us [live on Slack](https://join.slack.com/t/ns1labs/shared_invite/zt-qqsm5cb4-9fsq1xa~R3h~nX6W0sJzmA),
    or [file a GitHub issue here](https://github.com/ns1labs/orb/issues/new/choose).


Upon successful installation, visit our [Getting Started](https://getorb.io/docs/#getting-started) section to keep moving forward with Orb.