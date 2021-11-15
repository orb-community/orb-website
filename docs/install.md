You can use Orb via a SaaS or self-hosted method. While our SaaS platform is still in development, check out the self-host instructions below to get Orb up and running quickly. To find the latest release, check [GitHub](https://github.com/ns1labs/orb/releases).

## SaaS
The Orb software-as-a-service platform ([orb.live](http://orb.live)) is now in development. Check back soon for its official release.

Until then, see our self-host installation instructions below, [interact with us](https://getorb.io/contact/) today and check out [pktvisor](https://pktvisor.dev/)—a production-ready observability agent.

## Self-host
To self-host Orb (self-install and run everything locally on a single node), you can choose between two options for setup:

* **Docker Compose** - Consider this option as a developer installation in which you clone Orb’s repository and try it on a single machine.
* **Helm Chart** - Consider this option if you want to deploy a real production deployment, as you would be spinning up Kubernetes.

Follow the instructions below after choosing a self-host option. 

### Orb with Docker Compose
Try out Orb with Docker Compose by accessing the YAML file [here](https://github.com/ns1labs/orb/blob/develop/docker/docker-compose.yml).

#### Requirements

* Install `Git`
* Install `Make`
* Install [Docker](https://docs.docker.com/engine/install/)
* Install [Docker Compose](https://docs.docker.com/compose/gettingstarted/) (minimum version 1.29.2)

#### Instructions

* Clone the [Orb repository](https://github.com/ns1labs/orb).
* Paste it into your terminal via git clone command.
* Make run command. This will pull Orb’s containers from [DockerHub](https://hub.docker.com/u/ns1labs).

You are now running the Orb control plane on your machine and exposing it to your respective IP address as a website.


### Orb Helm Chart
[Helm](https://helm.sh/) is a package manager for Kubernetes. A Helm Chart is a package that allows you to customize your deployment on Kubernetes.

To configure and deploy an Orb Helm Chart, follow the instructions below.

#### Prerequisites
* [Helm v3](https://helm.sh/docs/intro/quickstart/)

#### Configuration
##### Instructions
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

* Deploy [cert manager helm](https://cert-manager.io/docs/installation/helm/) to [secure nginx ingress](https://cert-manager.io/docs/tutorials/acme/ingress/).

        helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.5.3 --set installCRDs=true

- Create Issuer CRDs (in the `orb` namespace!)
    - `cp issuers/production-issuer-tpt.yaml issuers/production-issuer.yaml`
    - Edit `issuers/production-issuer.yaml` and change `spec.acme.email` to a real email address.
    - `kubectl create -f issuers/production-issuer.yaml -n orb`

* Install Orb. Replace `my-orb` with your helm release name.

        helm install --set ingress.hostname=HOSTNAME -n orb my-orb .



