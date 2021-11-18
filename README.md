# Traefik 2.5 

## Deploying Traefik with the official Helm Chart

* Add Traefik's chart repository to Helm: 

```sh
helm repo add traefik https://helm.traefik.io/traefik
```

* Update the chart repository by running: 

```
helm repo update
```

* Customise the `values.yaml` by adding extra configuration. 

* Create Traefik namespace: 

```sh
kubectl create namespace traefik 
```

* Deploy Traefik by running the following command:

```sh
helm upgrade --install traefik -f values.yaml traefik/traefik -n traefik
```

* Verify the installation. 

* Explore Traefik dashboard by creating port forwarding to Traefik's pod by running the following command:

```
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000
```

## Deploying sample applications

* Create a namepsace where apps and services will be created
* Deploy the applications 
* Create Ingressroute for deployed applications


## Ingress and IngressClass

* Please check if IngressClass has been created with the new API version
* Deploy the Ingress object using the appropriate API version. Verify what annotatios has been added to the Ingress object. 

## Weighted Round Robin with Nested Healtchecks

The feature is currently only available as dynamic configuration provided through File. That's why we have to add file provider that points to a config map containing Traefik's extra configuration.

* Create configmap:

```sh
 kubectl create configmap traefik-configs -n traefik --from-file=dynamic.yaml=config/dynamic.yaml 
```

* Add File provider and VolumeMounts to the `values.yaml`
* Deploy Traefik using the files `values.yaml`
```sh
helm upgrade --install traefik -f values.yaml traefik/traefik -n traefik
```
* Verify running configuration using the following command:

```sh
curl https://nested.waw.demo.traefiklabs.tech
```

You should receive responses from both services `whoamiv1` and `whoamiv2` according to the weights set in the dynamic configuration. 

* Scale down to 0 replicas on one of the service
 
```sh
kubectl scale deployment whoamiv2 --replicas=0
```

* Verify configuration using the following command:

```sh
curl https://nested.waw.demo.traefiklabs.tech
```

You should receive reposnes only from `whoamiv1`.

Traefik automatically discover that `whoamiv2` is down and send reuqests only to the healthy services, that is `whoamiv1`.


## Local provider plugins

* A simple command `docker-compose up` should deploy the example. 
* Check dashboard to see the new provider plugins

## HTTP3, still in the experimental phase

