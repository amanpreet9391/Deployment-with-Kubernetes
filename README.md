# Deployment With Kubernetes
Deployment of web application parse-server with Kubernetes. Kubernetes is used for clustered level deployment. The other tool which can be used for the same purpose is Docker Swarm. This application is already deployed with the help of Docker Swarm in my previous repository(On Premise Deployment). Docker compose file consisting of deployment of different application like grafana,node-inspector and parse-server is also deployed in cluster with Kubernetes.
## Multinode Cluster using Kubeadm
Cluster's most important component is master. The Kubernetes master is responsible for maintaining the desired state for your cluster. When you interact with Kubernetes, such as by using the kubectl command-line interface, you’re communicating with your cluster’s Kubernetes master. Master distributes workload on other nodes in the cluster alos known as worker nodes. 
To start the cluster , first initialise master by ` kubeadm init --kubernetes-version=version --apiserver-advertise-address=ip of master` command.
After initialising the cluster a token is generated at master `kubeadm join ` , that token will be copied to the worker nodes to let them join the cluster. To manage the Kubernetes cluster, the client configuration and certificates are required. This configuration is created when kubeadm initialises the cluster. The command copies the configuration to the users home directory and sets the environment variable for use with the CLI. Run the commands given below for that.
` sudo cp /etc/kubernetes/admin.conf $HOME/
sudo chown $(id -u):$(id -g) $HO ME/admin.conf
export KUBECONFIG=$HOME/admin.conf`
![master-token](https://user-images.githubusercontent.com/25201552/55382164-96efa100-5542-11e9-97f5-86209ff120b4.PNG)
Copy this token to worker node to add them in cluster.
![worker-join](https://user-images.githubusercontent.com/25201552/55382265-dddd9680-5542-11e9-86ee-48d718be29d1.PNG)
Now in order to check all the nodes in the cluster run `kubectl get nodes` command.
![p-3](https://user-images.githubusercontent.com/25201552/55382368-19786080-5543-11e9-9bac-f0cd1381dc7b.PNG)
Nodes will be displayed, but they are showing not ready status. This is because Container Network interface has not been deployed yet.Use the command ` kubectl apply -f <addon.yaml>.`Weave-kube is deployed here.
To check deployment status run command ` kubectl get pod -n kube-system`
![p-4](https://user-images.githubusercontent.com/25201552/55382774-29dd0b00-5544-11e9-95ea-c074bae05596.PNG)
After successfully running this command, master and the worker nodes will show ready status. 
Now create pods, as it is the runable  unit of work in Kubernetes. To create run command ` kubectl create deployment parse-server --image=amanpreet9391/parse-server `
![p-5](https://user-images.githubusercontent.com/25201552/55383014-bf789a80-5544-11e9-84ef-283fc29c1d23.PNG)
All these commands are to be run on master.
to check running pods, run `kubectl get pods`
![p-6](https://user-images.githubusercontent.com/25201552/55383089-f2bb2980-5544-11e9-86d5-1a4646771a0a.PNG)
In order to add worker nodes, just copy and paste the token generated on master to those nodes. 
Kubernetes have an interactive graphical dashboard which shows everything happening in the cluster.
![dashboard-parse-server (1)](https://user-images.githubusercontent.com/25201552/55383300-7bd26080-5545-11e9-93df-509e52d8d763.png)

![dashborad-2 (1)](https://user-images.githubusercontent.com/25201552/55383275-6a895400-5545-11e9-905b-f7ea191a6138.png)
Now add one more worker node. Along with scale the application by changing number of replicas from 1 to 3 by `kubectl scale --replicas=3 deployment parse-server`
![p-8-scaling](https://user-images.githubusercontent.com/25201552/55383452-d4a1f900-5545-11e9-822c-974286180605.PNG)
By kubectl get pods check new running pods. By interactive graphical interface one can also monitor the changes.

## Run Docker compose with Kompose
Docker compose can be run in the Kubernetes cluster my `kompose up` command.
![kompose-4](https://user-images.githubusercontent.com/25201552/55383658-5f82f380-5546-11e9-90e1-b6402d2c0c52.png)
Then check the running pods, services and deployments by ``kubectl get deployemnt,svc,pods,pvc` command.















