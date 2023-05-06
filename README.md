## Commands to run the deployment

# Set the compute zone according to the deplyment

gcloud config set compute/zone 

# Create a cluster

gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"

# Create auth and hello YAML files

kubectl cretae -f Deployments/auth.yml
kubectl create -f services/auth.yml

kubectl create -f Deployments/hello.yml
kubectl create -f services/hello.yml

# Verify the Deployment

kubectl get Deployments
kubectl get pods
kubectl get replicasets

# Scaling the Deployments

kubectl scale deployments hello --replicas=5

# Rollback from an Update

kubectl rollout pause Deployments/hello   ## pauses the update
kubectl rollout status Deployments/hello  ## displays rollback status
kubectl rollout resume Deployments/hello  ## continues the rollback from pause
kubectl rollout undo Deployments/hello    ## reverts the update

kubectl rollout history Deployments/hello  ## rollback history

## Canary Deployment

A canary deployment consists of a separate deployment with your new version and a service that targets both your normal, stable deployment as well as your canary deployment.

kubectl create -f Deployments/hello-canary.yml
kubectl get deployments

## Blue-Green Deployment

Rolling updates are ideal because they allow you to deploy an application slowly with minimal overhead, minimal performance impact, and minimal downtime. There are instances where it is beneficial to modify the load balancers to point to that new version only after it has been fully deployed. In this case, blue-green deployments are the way to go.

Kubernetes achieves this by creating two separate deployments; one for the old "blue" version and one for the new "green" version. Use your existing hello deployment for the "blue" version. The deployments will be accessed via a Service which will act as the router. Once the new "green" version is up and running, you'll switch over to using that version by updating the Service.

kubectl create -f services/hello-blue.yml 

# updates the services file to point to the new version app:hello and version:2.0.0

kubectl create -f services/hello-green.yml

# To rollback from green deployment just update the services back to 1.0.0

kubectl create -f sevices/hello-blue.yml
  