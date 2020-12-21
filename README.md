# Pre-Requisites

- Kubectl set up 
- An active cluster to run Kubectl commands against

## Versions this Repo was Ran on

Single Node Cluster
- Kubectl Version 1.20
- Minikube Version 1.16.0
- Docker 20.10
- Latest Prometheus Image
- Latest Apache-Exporter Image
- Version 1 Apache Image from Cloud Academy [Included in the deployment.yaml]

### Getting Started

First we are going to create the services for our Deployment:
```
kubectl create -f https://github.com/cloudacademy/Flyby-Prometheus-Exporter/blob/master/environment.yaml
```
Then we will create the deployment itself. The selectors from the environment.yaml will associate the deployment with the appropriate services and expose our
microservices approriately.
```
kubectl create -f https://github.com/cloudacademy/Flyby-Prometheus-Exporter/blob/master/deployment.yaml
```


