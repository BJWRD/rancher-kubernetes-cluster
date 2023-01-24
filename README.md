# rancher-kubernetes-cluster
The following project includes Rancher setup/configuration, Minikube Cluster importation/configuration, and the deployment of a simple Webapp.

## Prerequisites
* Minikube installation - [steps](https://minikube.sigs.k8s.io/docs/start/)
* Docker installation - [steps](https://docs.docker.com/desktop/install/mac-install/) 

**NOTE:** the following installation/steps were performed on MacOS.

## Minikube Cluster 

### 1. Start Minikube Cluster

      minikube start --driver=docker

## Docker Rancher Container 

### 1. Running the Rancher Container
If you are installing Rancher for learning purposes and therefore identity verification isn't a concern, run Rancher via Docker using a self-signed certificate by entering the following command -

      docker run -d --restart=unless-stopped \
      -p 80:80 -p 443:443 \
      --privileged \
      rancher/rancher:latest
      
This command will run a Rancher container on port 80 and 443.

<img width="563" alt="image" src="https://user-images.githubusercontent.com/83971386/214293091-0631c666-bdaf-4a50-9f78-52e3544cbe7c.png">

## Accessing Rancher 

### 1. Access the Rancher webpage

 To access the Rancher site, type and search localhost:443 into your browser -
 
 Upon the first run you will be asked to enter the following command to retrieve the auto-generated bootstrap password -
 
      docker logs  container-id  2>&1 | grep "Bootstrap Password:"
 
![image](https://user-images.githubusercontent.com/83971386/214004856-0a146f17-2363-4c13-8ef2-1c783786a815.png)

Enter image of bootstrapping password
 
 ### 2. Setting your own password
 You will then be prompted to set your own password for access to Rancher -
 
![image](https://user-images.githubusercontent.com/83971386/214005286-8465807a-abe0-410e-a1cd-aa90e159811e.png)
 
 ### 3. Rancher Server URL
The next step is an important one, you will now be in the position to set the Rancher Server URL. If you were to leave the default “localhost” as your server URL, your Minikube’s nodes won’t be able to reach your server, so alternatively enter your local IP address -
 
      ip a
      ifconfig
      ipconfig
      
![image](https://user-images.githubusercontent.com/83971386/214243499-48ace274-f409-4e80-b810-a25dc9840157.png)

## Cluster Configuration and Setup

### 1. Adding the Cluster
Once logged in, you will be presented with the following screen -

![image](https://user-images.githubusercontent.com/83971386/214011251-c7cbcff6-c921-4e8c-8a3d-edfd1f1f4c5a.png)

You will need to select the 'Import Existing' button, followed by 'Custom'.

![image](https://user-images.githubusercontent.com/83971386/214011626-7192889e-bb16-482f-b0ad-90ed60230f1f.png)

Enter the name of the cluster and description, then select the 'Create' button.

**NOTE:** There is also the option of entering environment variables and assigning a label/annotation.

<img width="506" alt="image" src="https://user-images.githubusercontent.com/83971386/214292910-67bd8975-40c6-4896-9eb5-15c3c196b9a3.png">

### 2. Register the Cluster into Rancher
Now it's time to register the Cluster into Rancher. This can be done by following the instructions within the screenshot below -

![image](https://user-images.githubusercontent.com/83971386/214012981-ba7d4cd9-7c02-4cfb-8a44-202aa4783b2a.png)

These commands will need to be entered within the running Minikube Cluster -

      kubectl apply -f https://localhost/v3/import/7lzggvls7m2cdhr8ptl87nflwktjdgjn5cpr99kwb7tvsjvm8zvkvr_c-m-5ql2dkvx.yaml
      curl --insecure -sfL https://localhost/v3/import/7lzggvls7m2cdhr8ptl87nflwktjdgjn5cpr99kwb7tvsjvm8zvkvr_c-m-5ql2dkvx.yaml | kubectl apply -f -
      kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user <your username from your kubeconfig>

### 3. Cluster Rancher Verification
Now just wait a few minutes so that your cluster’s State changes from Pending to Active. Once changed, click on your Cluster’s Name and you will see this screen -

<img width="596" alt="image" src="https://user-images.githubusercontent.com/83971386/214292634-fed0144d-98fc-4a46-95ae-4951cbe0c325.png">

This verifies that the Minikube Cluster has been successfully imported within your Rancher setup.

## KubeConfig Configuration

### 1. Download the Cluster KubeConfig file
Access the newly imported cluster and select the following buttons to download the Clusters kubeConfig file -

<img width="511" alt="image" src="https://user-images.githubusercontent.com/83971386/214250194-0ee79f96-1c1b-43a1-b2f8-7ae044575017.png">

### 2. Access the downloaded KubeConfig file
Using an IDE of your choice, open the downloaded cluster.yaml file, to view your cluster contents -

<img width="439" alt="image" src="https://user-images.githubusercontent.com/83971386/214250672-c882c6a5-cb69-4838-a05f-9805ca980628.png">

### 3. Test Cluster access via Kubectl/KubeConfig

      kubectl get node --kubeconfig=/pathofkubeconfigfile/test.yaml

<img width="284" alt="image" src="https://user-images.githubusercontent.com/83971386/214279744-14dfd4d9-9de6-4c99-9702-d4528e5471e7.png">

## Deploying an example Workload - Hello World webapp

### 1. Clone this repository

      git clone https://github.com/BJWRD/rancher-kubernetes-cluster
      
### 2. Create the Kubernetes resources 

      kubectl create -f namespace.yaml
      kubectl create -f configmap.yaml
      kubectl create -f deployment.yaml
      kubectl create -f service.yaml
      
### 3. View all created Kubernetes resources

      kubectl -n webapp-namespace get all
      
<img width="544" alt="image" src="https://user-images.githubusercontent.com/83971386/214288624-489827fc-3cb4-4451-be2b-c3be60ae0f49.png">
      
You can also view the newly created resources via the Rancher GUI -

<img width="813" alt="image" src="https://user-images.githubusercontent.com/83971386/214289036-3515a160-ad6a-4b29-98ed-3d8d3eb697cc.png">

### 4. Test Webapp Deployment

      minikube service list
      
<img width="542" alt="image" src="https://user-images.githubusercontent.com/83971386/214291303-75141ace-fb9d-48df-8663-ed304c61c68d.png">

Due to Rancher using Port 8080, we will need to port forward to Port 8081 for the Webapp to then be accessible -

      kubectl -n webapp-namespace port-forward svc/webapp-service 8081:8080
      
<img width="693" alt="image" src="https://user-images.githubusercontent.com/83971386/214291628-87b8670a-784c-413e-a98f-465740ad0844.png">

      curl http://localhost:8081
      
<img width="448" alt="image" src="https://user-images.githubusercontent.com/83971386/214292308-98d936d2-9121-4ac2-a311-c494692005cf.png">

## List of tools/services used
* [Minikube](https://minikube.sigs.k8s.io/docs/)
* [Docker](https://docs.docker.com/get-started/overview/)
* [K8s Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

## Useful Links -
* https://ranchermanager.docs.rancher.com/v2.5/pages-for-subheaders/rancher-on-a-single-node-with-docker
* https://www.youtube.com/watch?v=1j5lhDzlFUM
