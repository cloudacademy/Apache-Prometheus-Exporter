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

All docker images are provided for this course from CloudAcademy in the YAML files

## Getting Started

To kick things off clone this repo:
```
git clone https://github.com/cloudacademy/Flyby-Prometheus-Exporter.git
cd Flyby-Prometheus-Exporter
```
After that we are going to create the services for our Deployment:
```
kubectl create -f environment.yaml
```
Set the context:
```
kubectl config set-context --current --namespace prometheus
```
Now we will create the deployment itself. Since our config for prometheus resides in a configMap we will create that first and then the deployment. The selectors from the environment.yaml will associate the deployment with the appropriate services and expose our microservices approriately.
```
kubectl create -f config.yaml
kubectl create -f deployment.yaml
```
Verify we can see the deployment:
```
kubectl get po -w
```
(To stop watching the Pod creation, simply press Ctl-C)

You should see a deployment with three containers created no more than 20 seconds after the above command is ran.
After we can see the pods, run the following command to get the IP of the deployment:
```
kubectl get po -o wide
```
Place the IP provided there in the config.yaml file for prometheus to scrape against (If these are the only pods, it will likely be the 172.17.0.3 IP Address):
```
      global:
        scrape_interval: 15s
      scrape_configs:
        - job_name: 'apache'
          scrape_interval: 5s
          static_configs:
          - targets: ['{PLACE_THE_IP_ADDRESS_HERE}:9117']
```

## Accessing our Prometheus Dashboard

Now that our environment is setup with a proper Apache server, exporter, and a Prometheus server, we can access them to see their data.

The following command will output the Prometheus server URL (and open it for you if you have a browser open)
```
minikube service prom-service -n prometheus
```

Navigate to the URL provided and in the PromQL expression bar, begin typing 'apache' and you will receive a list of metrics to choose from.
There's also another way we can quickly check that the Apache exporter is working as well: through a simple curl to the Prometheus Server
```
curl http://{URL_OF_PREVIOUS_COMMAND}/api/v1/label/job/values
```

### What if we wanted to see the metrics at the Apache Exporter itself? Instead of the Prometheus Server?

Let's run a kubernetes pod with a curl container to curl the /metrics endpoint for the Apache webserver. This pod will run, curl the provided URL (replace the IP address below with your deployment/pod IP), then return the output to our terminal. The reason we have to run this within the cluster, is because we are not exposing the Apache exporter publicly, only through a ClusterIP. Lastly, we are piping the output to the more command in order to sift through the metrics easier.
```
kubectl run curl --image curlimages/curl -it --rm --restart=Never -- http://172.17.0.3:9117/metrics | more
```

Perfect! We now see our Apache exporter metrics outside of our Prometheus Server. 

### Generating some Metrics

Apache has a stress testing tool that we can use call 'ab' to test some connections to the server. Let's run that pod now with the similar commands previously used. This command will run 1000 requests with a concurrency of 20 (requests ran simultaenously). Ensure you replace the IP Address below with your Minikube IP and NodePort (minikube service apache -n prometheus):
```
k run ab --image jordi/ab --rm -it --restart=Never -- -n 1000 -c 20 http://192.168.99.109:30750/
```

Navigate back to the Prometheus Dashboard and check the apache metrics 'apache_cpuload' and notice the spike in traffic

## Conclusions

Now that you've got a working Prometheus Deployment complete with an Apache Webserver and Apache Exporter - Explore the environment! 

And when you're all done - Clean up the environment:

```
kubectl delete -f environment.yaml -f config.yaml -f deployment.yaml
```
