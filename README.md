# rancher-kubernetes-cluster
The following project includes Rancher setup/configuration, Minikube Cluster importation/configuration, and the deployment of an example workload.

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

Enter Image 

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
      
## Cluster Configuration and Setup

### 1. Adding the Cluster
Once logged in, you will be presented with the following screen -

![image](https://user-images.githubusercontent.com/83971386/214011251-c7cbcff6-c921-4e8c-8a3d-edfd1f1f4c5a.png)

You will need to select the 'Import Existing' button, followed by 'Custom'.

![image](https://user-images.githubusercontent.com/83971386/214011626-7192889e-bb16-482f-b0ad-90ed60230f1f.png)

Enter the name of the cluster and description, then select the 'Create' button.

**NOTE:** There is also the option of entering environment variables and assigning a label/annotation.

![image](https://user-images.githubusercontent.com/83971386/214011948-d44814a7-7471-41b2-9bed-27b4c5816b18.png)

### 2. Register the Cluster into Rancher
Now it's time to register the Cluster into Rancher. This can be done by following the instructions within the screenshot below -

![image](https://user-images.githubusercontent.com/83971386/214012981-ba7d4cd9-7c02-4cfb-8a44-202aa4783b2a.png)

These commands will need to be entered within the running Minikube Cluster -

      kubectl apply -f https://localhost/v3/import/7lzggvls7m2cdhr8ptl87nflwktjdgjn5cpr99kwb7tvsjvm8zvkvr_c-m-5ql2dkvx.yaml
      curl --insecure -sfL https://localhost/v3/import/7lzggvls7m2cdhr8ptl87nflwktjdgjn5cpr99kwb7tvsjvm8zvkvr_c-m-5ql2dkvx.yaml | kubectl apply -f -
      kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user <your username from your kubeconfig>
      
Enter Image

### 3. Cluster Rancher Verification
Now just wait a few minutes so that your cluster’s State changes from Pending to Active. Once changed, click on your Cluster’s Name and you will see this screen -

Enter Image

This verifies that the Minikube Cluster has been successfully imported within your Rancher setup.

## Deploying an example Workload 

configmap, secret, deployment, service

## List of tools/services used
* [Minikube](https://minikube.sigs.k8s.io/docs/)
* [Docker](https://docs.docker.com/get-started/overview/)
* [K8s Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

## Useful Links -
* https://ranchermanager.docs.rancher.com/v2.5/pages-for-subheaders/rancher-on-a-single-node-with-docker
* https://www.youtube.com/watch?v=1j5lhDzlFUM
