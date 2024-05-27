## Install microk8s on your local machine
	$ snap install microk8s --classic

## Add the thuto.k8s to the hosts
	$ sudo vim /etc/hosts
	127.0.0.1	thuto.k8s	# Add this line to the number of hosts

## Add relevant addons
	$ microk8s enable dashboard
	$ microk8s enable dns
	$ microk8s enable host-access
	$ microk8s enable hostpath-storage
	$ microk8s enable ingress
	$ microk8s enable observability

## Run nginx for external access
	$ microk8s kubectl run nginx --image nginx

## Create a storage class
	$ microk8s kubectl apply -f templates/sakai-storage-class.yaml

## Run sakai database separately from the cluster
	$ microk8s kubectl apply -f db/sakaidb.yaml


helm install db-setup db-setup --repo https://bahmni.github.io/helm-charts --devel --wait --wait-for-jobs --atomic --timeout 1m \
  --set DB_HOST=sakaidb \
  --set DB_ROOT_USERNAME="sakai" \
  --set DB_ROOT_PASSWORD="examplerootpassword" \
  --set databases.sakai.DB_NAME="sakai" \
  --set databases.sakai.USERNAME="sakai" \
  --set databases.sakai.PASSWORD="examplepassword"

## Install the chart for deployment
	$  helm install --name-template=sakai-local . --values=values/values.yaml

## Stress testing our pods for Autoscaling capabilities
	$ kubectl exec <pod-name> -- stress-ng --cpu 2 --vm 2 --vm-bytes 512M --timeout 60s
