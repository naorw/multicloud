Sulution to connect to Amazon Web Services ECR (elastic container registry) for external Kubernetes clusters (For exmaple from a cluster runnign in GKE))

You need an AWS access tocken to ECR. That token expires every 12 hours
Proposed solution is to run a Kubernetes cronjob that will create the tocken and store it in a secret.
The CronJob runs with a Service Account that is allowed to delete and update secrets.
On deployment of custom images, Kubernetes will access ECR using current tocken and pull the needed images. 

Preparations:

Setup acess and assignd to it appropreate permissions to access the ECR (elastic container registry)
reference policy is attached:
AmazonEC2ContainerRegistryPowerUser
reference documentation from:
https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr_managed_policies.html


Cronjob is executed using a container that you can either pull from my dockerhub (naorw/ecr-credentials-cron) or build youself. 
Image build
you have a Dockerfile to verify process and create own image if needed.

Actuall process on cluster:

Step 1: create service account that modify secrets and assign it's role
kubectl apply -f ecr-cred-sa.yaml
kubectl apply -f ecr-cred-users.yaml

Step 2: create a cronjob that will refresh the secrets with updated token
kubectl apply -f ecr-cred-cronjob.yml

Additonal material: You can also create a test batch job:
kubectl apply -f aws-auth-test-job.yml

Notes:
1 AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY are not needed if you already publish AWS access credentials in another way
2 You wil need to set correct zone for your ECR, as well as namespace and other vatiables for your setup
3 Based on ideas proposed by https://github.com/xynova and others. Thank you guys!