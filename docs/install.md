# Getting started

There is not yet an official release of Orb, but you can [interact with us](contact.md) today! Releases will be made available in [GitHub](https://github.com/ns1labs/orb/releases) and [DockerHub](https://hub.docker.com/u/ns1labs).

In the lead-up to an official release, you can utilize Orb in the following ways:
* Preview Orb technology today by checking out [pktvisor](https://pktvisor.dev), a production-ready observability agent.
* Try out the Orb platform for the first time by self-installing it from GitHub and running everything locally on a single node. Follow the instructions below to self-install Orb.

## Orb with Docker Compose
Try out Orb with Docker Compose by accessing the YAML file [here](https://github.com/ns1labs/orb/blob/develop/docker/docker-compose.yml).

## Orb Helm Chart
The repo for these Orb Helm Chart deployment instructions can be found [here](https://github.com/ns1labs/orb-helm/tree/main/charts/orb).

### Prerequisites
* Helm v3

### Configuration
#### Instructions
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



