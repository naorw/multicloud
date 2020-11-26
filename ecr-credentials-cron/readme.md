Solution to connect to Amazon Web Services ECR (elastic container registry) for external Kubernetes clusters (For example from a cluster running in GKE))

You need an AWS access token to ECR. That token expires every 12 hours
Proposed solution is to run a Kubernetes cronjob that will create the token and store it in a secret.
The CronJob runs with a Service Account that is allowed to delete and update secrets.
On deployment of custom images, Kubernetes will access ECR using current token and pull the needed images. 

Preparations:

Setup access and assigned to it appropriate permissions to access the ECR (elastic container registry)
reference policy is attached:
AmazonEC2ContainerRegistryPowerUser
reference documentation from:
https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr_managed_policies.html


Cronjob is executed using a container that you can either pull from my docker hub (naorw/ecr-credentials-cron) or build yourself. 
Image build
you have a Dockerfile to verify processes and create your own image if needed.

Actual process on cluster:

Step 1: create service account that modify secrets and assign it's role
kubectl apply -f ecr-cred-sa.yaml
kubectl apply -f ecr-cred-users.yaml

Step 2: create a cronjob that will refresh the secrets with updated token
kubectl apply -f ecr-cred-cronjob.yml

Additional material: You can also create a test batch job:
kubectl apply -f aws-auth-test-job.yml

Notes:
1 AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY are not needed if you already publish AWS access credentials in another way
2 You will need to set correct zone for your ECR, as well as namespace and other variables for your setup
3 Based on ideas proposed by https://github.com/xynova and others. Thank you guys!

