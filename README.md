# gitops_101

# Prerequisites
-Docker
-Minikube
-Terraform
-Helm
-kubectl


Step 1: Python flask app Docker image is built with Dockerfile.

	$ docker build -t python-flask-project .
	$ docker tag python-flask-project username/private-repo:tag-name

Step 2: Push that python flask app Docker image to my private Dockerhub registry.

	$ docker push username/private-repo:tag-name

Step 3: ArgoCD on minikube will be deployed using Terraform.You have to enter terraform commands in the "terraform" directory.

	$ terraform init
	$ terraform plan
	$ terraform apply --auto-approve

To access the ArgoCD web UI, you'll need to port forward to the argocd-server service:

	$ kubectl port-forward svc/argocd-server -n argocd 8080:80
 
And then,navigate to localhost:8080 in your browser. You should see the ArgoCD login page.
Default username is admin. 
To retrieve the password from the secret using:

	$ kubectl get secrets argocd-initial-admin-secret -o yaml -n argocd
 
Copy the password and decode it with base64 utility:

	$ echo "copied-password" | base64 -d
 
Now, you can login to ArgoCD UI.

Step 4: Python Flask App will be deployed via Helm charts on minikube by using ArgoCD.
Next,set up values.yaml,deployments.yaml,image-pull-secret.yaml files.
(Note:To pull an image from private registry, you have to set image pull secret of your docker hub access.)
You can login to Docker by using CLI command and encode that credentials contents with base64 utility.Use that encoded docker config to create a secret.

	$ docker login -u username
	$ cat ~/.docker/config.json 
	$ base64  ~/.docker/config.json
 
you can also check this secret configuration in this path 
"helms/python-flask-project/templates/image-pull-secret.yaml"
 


Now , my python-flask application can be deployed on ArgoCD.

