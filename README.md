# KubernetesByExamples

This project contains example projects used to create Kubernetes resources.

1. example1 - In this example we create a deployment resource in Kubernetes.

```
#Create the Kubernetes deployment resource
kubectl create -f example1/deployment.yaml
```

Creating the deployment resource will create a replicaset resource to keep track on the desired replicas of the container deployed in POD. It also creates POD deploying the containerized application using the POD template added in deployment resource.

**Scale resources in Kubernetes**
In order to scale up/down a POD in Kubernetes, it can be achieved by scalling the deployment resource as below.
```
kubectl scale deployment/<deployment-name> -n <namespace> --replicas=<desired replicas>
```

2. example2 - In this example we create a service resource in Kubernetes

```
#Create the Kubernetes service resource of type nodePort
kubectl create -f example2/service_nodeport.yaml

#Create the Kubernetes service resource of type ClusterIP
kubectl create -f example2/service_clusterip.yaml
```

Creating the service resource will manage the traffic routing to the running PODs. So, client donot have to keep a track of the POD IP address. This is achieved using the labels specified in service resource and same resource is used in POD definition.

**To test a service deployed as NodePort**
```
#Fetch the NodePort of the desired service
kubectl get service/<service-name> -n <namespace>

#Access the containerized application using ClusterIP and Port
http://<service-name-ip-address>:<nodePort>/
```

**To test a service deployed as ClusterIP**
```
#Fetch the ClusterIP of the desired service
kubectl get service/<service-name> -n <namespace>

#Access the containerized application using ClusterIP and Port
http://<service-name-ip-address>:<service-port>/
```

3. Deploy Nginx and Apache2 application in Containers, then create a Ingress-Nginx controller, create Ingress rule for Nginx and Apache2 service and access the applications.

- CREATE NGINX DEPLOYMENT
```
kubectl create -f example3/nginx_deployment_definition.yaml -n <namespace>
```

- CREATE NGINX CLUSTERIP SERVICE
```
kubectl create -f example3/nginx_service_definition.yaml -n <namespace>
```

- CREATE APACHE2 DEPLOYMENT
```
kubectl create -f example3/apache2_deployment_definition.yaml -n <namespace>
```

- CREATE APACHE2 CLUSTERIP SERVICE
```
kubectl create -f example3/apache2_service_definition.yaml -n <namespace>
```

- LIST RESOURCES
```
kubectl get all -n <namespace>
```

- SETUP INGRESS-NGINX CONTROLLER
```
#Below command would deploy ingress-nginx controller version 1.1.1. This will create a new namespace name ingress-nginx.
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/baremetal/deploy.yaml

#List resource created with ingress-nginx controller
kubectl get all -n ingress-nginx
```

- CREATE INGRESS RULE FOR NGINX AND APACHE2 SERVICE
```
kubectl create -f example3/ingress_definition.yaml -n <namespace>
```

- ACCESS APPLICATION AT NODEPORT IP
```
#Get the Kubernetes Ingress IP address and access nginx
http://<IP>:<Port>/nginx

#Get the Kubernetes Ingress IP address and access apache
http://<IP>:<Port>/apache
```

4. example4: In this example project we create a Persistence Volume, create a volume claim resource to the created PV, create secrets and see how to taint a node. 

- CREATE PERSISTENT VOLUME
```
kubectl create -f example4/pv_definition.yaml -n <namespace>
```
- CREATE PERSISTENT VOLUME CLAIM
```
kubectl create -f example4/pvc_definition.yaml -n <namespace>
```
- CREATE SECRETS
```
kubectl create -f example4/secret_definition.yaml -n <namespace>
```
- TAINT A NODE
```
Kubectl taint nodes <node-name> key=value:taint-effect

#example
kubectl taint nodes ip-172-31-11-223 color=red:NoSchedule
```

5. example5: Deployment of Prometheus and Grafana in Kubernetes

- Clone Github project
Github project : https://github.com/15richa/website.git
```
git clone https://github.com/15richa/website.git
```

- BUILD DOCKERFILE AND PUSH TO DOCKER HUB
```
cd website
docker build . -t richasrivastava15/k8s_app --no-cache

docker push richasrivastava15/ks8_app
```

- DEPLOY CUSTOM DOCKER CONTAINER IN KUBERNETES
```
kubectl create -f example5/deployment_definition.yaml -n <namespace>
```

- CREATE CLUSTERIP SERVICE
```
kubectl create -f example5/service_definition.yaml -n <namespace>
```

- ACCESS CUSTOM APPLICATION
```
#Fetch ClusterIP of the service deployed
kubectl get service/custom-website-service -n <namespace>
#Access application
curl http://<Cluster-Ip>:<service-port>
```

- SETUP PROMETHEUS & GRAFANA
```
#Create Monitoring Namespace
kubectl create namespace monitoring

#Create Cluster Role
kubectl create -f example5/clusterrole_definition.yaml -n monitoring

#Create ClusterRoleBinding
kubectl create -f example5/clusterrolebinding_definition.yaml -n monitoring

#Create Prometheus ConfigMap
kubectl create -f example5/prometheus_configmap_definition.yaml -n monitoring

#Create Prometheus deployment
kubectl create -f example5/prometheus_deployment_definition.yaml -n monitoring

#Create Prometheus Service
kubectl create -f example5/prometheus_service_definition.yaml -n monitoring

#Create Config-map for Grafana
kubectl create -f example5/grafana_configmap_definition.yaml -n monitoring

#Create Grafana deployment
kubectl create -f example5/grafana_deployment_definition.yaml -n monitoring

#Create Grafana service
kubectl create -f example5/grafana_service_definition.yaml -n monitoring

#List all resource create
kubectl get all -n monitoring

#Access Prometheus dashboard
#Fetch prometheus service IP and Port
kubectl get service/prometheus-service -n monitoring

http://<prometheus-service-ip>:<node-port>/status

#Access Grafana dashboard
#Fetch Grafana service IP and Port
kubectl get service/grafana -n monitoring

http://<grafana-service-ip>:<node-port>
```

6. Happy Learning.!