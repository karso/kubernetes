## Running Kubernetes Dashboard on minikube

My setup is as follows. I have a windows host running a VBox. The guest is Ubuntu 18.04. Inside the guest 
I am runnig a minikube cluster. The guest has a private network with static IP. The idea is to run the kubernetes dashboard on minikube and access it from the Ubuntu host as well as from the Windows Host.

### Steps
1. Enable the dashboard plugin from minikube.
$ minikube addons enable metrics-server # A Dependency for dashboard
$ minikube addons enable dashboard

This will enable the addon and dashboard service will start. However, the service will be type ClusterIP and you will not 
be able to access it from the browser. For that we will have to change the service type from ClusterIP to NodePort.

2. Remove the existing service and deploy the service as NodePort.
$ kubectl delete service kubernetes-dashboard -n kubernetes-dashboard
$ kubectl create -f kubernetes-dashboard_svc_nodeport.yaml 

Now the service is changed to NodePort type; it will be accessible. However, it will be accessible ONLY using the k8s cluster IP. 

3. To try it out, run the following 
$ minikube service list
$ curl [THE URL FROM ABOVE COMMAND]

To make it accessible from the UBUNTU localhost or from the Windows host; we will use a concept called 'port-forwarding'.
The funda is; kubernetes can forward the pod port(s); 
which can be identified by the service name to the localhost and to the private IP. The command is 
``` kctl port-forward --address localhost,[private_ip] service/[service_name] [localhost/private_ip port]:[pod port] ```
There are other forwarding tricks that "port-forward" has; like forwarding random ports, multiple ports etc. I must explore 
``` kubectl port-forward --help ```

4. Port forward to host
$ kctl port-forward --address localhost,[private_ip] service/[service_name] [localhost/private_ip port]:[pod port]

Using the browser on your windows host; hit the following URL

http://PRIVATE_IP:PRIVATE_IP_PORT