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
## Docker Image

A custom Docker image was created for this course, if you'd like to create it navigate to the docker folder and run:

```
sudo docker build . 
```

## Getting Started

First we are going to create the services for our Deployment:
```
kubectl create -f https://github.com/cloudacademy/Flyby-Prometheus-Exporter/blob/master/environment.yaml
```
Set the context:
```
kubectl config set-context --current --namespace prometheus
```
After we can see the pods, run the following command to get the IP of the deployment:
```
kubectl get po -o wide
```
Place the IP provided there in the config.yaml file for prometheus to scrape against:
```
      global:
        scrape_interval: 15s
      scrape_configs:
        - job_name: 'apache'
          scrape_interval: 5s
          static_configs:
          - targets: ['{PLACE_THE_IP_ADDRESS_HERE}:9117']
```
Now create the configmap:
```
kubectl create -f config.yaml
```

Now we will create the deployment itself. The selectors from the environment.yaml will associate the deployment with the appropriate services and expose our
microservices approriately.
```
kubectl create -f https://github.com/cloudacademy/Flyby-Prometheus-Exporter/blob/master/deployment.yaml
```
Verify we can see the deployment:
```
kubectl get po -w
```
(To stop watching the Pod creation, simply press Ctl-C)

You should see a deployment with three containers created no more than 20 seconds after the above command is ran.

## Accessing our Prometheus Dashboard

Now that our environment is setup with a proper Apache server, exporter, and a Prometheus server, we can access them to see their data.

The following command will output the Prometheus server URL (and open it for you if you have a browser open)
```
minikube service prom-service -n prometheus
```

Navigate to the URL provided and in the PromQL expression bar, begin typing 'apache' and you will receive a list of metrics to choose from.
There's another way we can quickly check that the Apache exporter is working as well: through a simple curl to the Prometheus Exporter
```
curl http://{URL_OF_PREVIOUS_COMMAND}/api/v1/label/job/values
```