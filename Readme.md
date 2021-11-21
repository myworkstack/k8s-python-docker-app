# Sample Hello World Python Web App Docker image deployment using Kubernetes YAML

# 1. Develop Python flask webapp
       python/hello.py

# 2. Create Dockerfile and Build the Docker image of the python app using below command
        $ docker build -f docker/Dockerfile -t udayalingam/hello-from-myworkspace .

# 3. Push the docker image to private registry using below command
        $ docker push udayalingam/hello-from-myworkspace

# 4. Before proceeding with kubernetes deployment, run the docker image to validate
        $ docker run -p 5001:5000 udayalingam/hello-from-myworkspace

# 5. Create secret to access the docker image from the private registry
        $ kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/  --docker-username=<user_name> --docker-password=<password> --docker-email=<test@gmail.com>

# 6. Execute k8s deployment.yaml to deploy the udayalingam/hello-from-myworkspace by using the secret in spec to pull the image
        $ kubectl create -f kubernetes/deployment.yaml

# Validate the deployment and 2 instances of the container will be created (as replicas given as 2)
        $ kubectl get deployment hello-myworkspace-deployment -o wide
        NAME                           READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS          IMAGES                                      SELECTOR
        hello-myworkspace-deployment   2/2     2            2           156m   hello-myworkspace   udayalingam/hello-from-myworkspace:latest   app=hello-myworkspace

        $ kubectl get pods
        NAME                                            READY   STATUS    RESTARTS   AGE
        hello-myworkspace-deployment-66fbdfdd45-dhfnq   1/1     Running   0          157m
        hello-myworkspace-deployment-66fbdfdd45-sdjwn   1/1     Running   0          157m

# 7. Execute k8s loadbalancer.yaml to create the service and expose the app url on port 6000 using k8s Loadbalancer service
        $ kubectl create -f kubernetes/loadbalancer.yaml 

# Validate the loadbalancer service and access the app using http://localhost:6000 or using External IP and port
            $ kubectl describe service hello-myworkspace-service
            Name:                     hello-myworkspace-service
            Namespace:                default
            Labels:                   <none>
            Annotations:              <none>
            Selector:                 app=hello-myworkspace-deployment
            Type:                     LoadBalancer
            IP Families:              <none>
            IP:                       10.98.235.163
            IPs:                      10.98.235.163
            Port:                     http  6000/TCP
            TargetPort:               5000/TCP
            NodePort:                 http  30424/TCP
            Endpoints:                <none>
            Session Affinity:         None
            External Traffic Policy:  Cluster
            Events:                   <none>

$ kubectl get service hello-myworkspace-service -o wide
NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE   SELECTOR
hello-myworkspace-service   LoadBalancer   10.98.54.166   <pending>     6000:31151/TCP   56s   app=hello-myworkspace-deployment