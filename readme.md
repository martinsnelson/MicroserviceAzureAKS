# Microservice with Azure AKS

# Create APP
dotnet new webapi -o MyMicroservice --no-https -f net5.0

The -o parameter creates a directory named MyMicroservice where your app is stored.
The --no-https flag creates an app that will run without an HTTPS certificate, to keep things simple for deployment.


# Create image
Install docker

docker --version

Crate docker file

docker build -t mymicroservice .

docker images

docker run -it --rm -p 3000:80 --name mymicroservicecontainer mymicroservice

docker ps

# Upload image to Docker Hub
Sign in to Docker Hub
docker login

Upload image to Docker Hub
docker tag mymicroservice [YOUR DOCKER USERNAME]/mymicroservice
docker push [YOUR DOCKER USERNAME]/mymicroservice


# Sign in to Azure to deploy microservice to azure with AKS and resource group
Install Azure CLI
az login


Install AKS CLI
Kubernetes is a container orchestration platform. An orchestrator is responsible for running, distributing, scaling, and healing apps comprised of a collection of containers. Azure Kubernetes Service (AKS) provides Kubernetes as a managed service.
az aks install-cli


Create a resource group
A resource group is used to organize a set of resources related to a single app.


Run the following command to create a resource group on the West US region:
az group create --name MyMicroserviceResources --location westus


If you want to use a different location on the previous command, 
you can run the following command to see which regions are available on your account and pick one closer to you:
az account list-locations -o table


If you want to use a different subscription for the session you can get a list of all subscriptons by running the following command:
az account list --all


Then you can run the following command to set a specific subscription for the session:
az account set -s NAME_OR_ID


Create an AKS cluster
Run the following command to create an AKS cluster in the resource group:
 OBS:It's normal for this command to take several minutes to complete.
az aks create --resource-group MyMicroserviceResources --name MyMicroserviceCluster --node-count 1 --enable-addons http_application_routing --generate-ssh-keys


Run the following command to download the credentials to deploy to your AKS cluster:
az aks get-credentials --resource-group MyMicroserviceResources --name MyMicroserviceCluster


# Deploy to Azure
Return to app directory
Since you opened a new command prompt in the previous step, you'll need to return to the directory you created your service in.
cd MyMicroservice


Create a deployment file
The AKS tools use a .yaml file to define how to deploy your container.
Create a file called deploy.yaml
Replace the content of the deploy.yaml to the following in the text editor, making sure to replace [YOUR DOCKER ID] with your actual Docker ID.


Run deployment
Run the following command to deploy your microservice based on the settings in deploy.yaml:
kubectl apply -f deploy.yaml


Test your deployed service
Run the following command to see the details of your deployed service:
kubectl get service mymicroservice --watch

Among other things, the previous command will show the external IP address that your service is available on (EXTERNAL-IP).
Using the external IP address, open a new browser window and navigate to http://[YOUR EXTERNAL IP ADDRESS]/WeatherForecast
If the EXTERNAL-IP is marked as <pending>, a new line will automatically appear once the external IP has been allocated.

# Congratulations! You've deployed a microservice to Azure.
Press CTRL+C on your command prompt to end the kubectl get service command.
kubectl get service

# Scale your service
A benefit of using Kubernetes is that you can easily scale up a deployment to multiple instances to handle additional load. Currently, there's only a single instance, so let's scale to two instances.

Run the following command to scale your service up to two instances:
kubectl scale --replicas=2 deployment/mymicroservice


# Clean up resources
When you're done testing the microservice, you can delete all resources that you created with the following command:
az group delete -n MyMicroserviceResources

# Clean up resources in azure portal 
Open -> Resource groups -> select your name resource -> delete