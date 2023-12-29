Kevin's Messages 



Deploying on VanDeBron play cluster RHSSO through ansible
when are you able to do this.
Their perferred method is as follows:
Use Ansible playbook to kick off HELM chart for deployment of RHSSO and do rest of configuration with Ansible
Is this something we can test?


We need to deploy and configure RHSSO Build 22+ on plain K8
Completely automated
Thry prefer kicking off a playbook which kicks off a helmchart and then rest ansible roles for config
Let me know and show me the possibilities for RHSSO

docker run --name rhsso -p 8080:8080 custom-rhsso:latest 
docker run -d --name rhsso --network host -p 8080:8080 custom-rhsso:latest 
docker run -d --name rhsso -p 8080:8080 --network bridge custom-rhsso:latest
docker run -d --name rhsso -p 8091:8080 --network bridge custom-rhsso:latest
docker run -d --name rhsso -p 8091:8080 custom-rhsso:latest

# RUNNING RHBK IMAGE 
docker run -d --name rhbk -p 8080:8080 --env KEYCLOAK_ADMIN=admin --env KEYCLOAK_ADMIN_PASSWORD=admin registry.redhat.io/rhbk/keycloak-rhel9:22-6 start-dev


https://developers.redhat.com/articles/2023/02/20/automate-your-sso-ansible-and-keycloak#run_the_playbook 

Can't use ansible collection for 

	RH SSO Installation 
	https://github.com/kenmoini/rh-sso-demo 
	https://www.itix.fr/blog/running-redhat-sso-outside-openshift/ 
	https://developers.redhat.com/articles/2023/02/20/automate-your-sso-ansible-and-keycloak#configure_single_sign_on
	
	
	  
  	kubectl create secret docker-registry rh-registry-secret \
  	  --docker-server=registry.redhat.io \
  	  --docker-username= {{ email }} \
  	  --docker-password={{ password }} \
  	  --namespace=rhsso
	  
	  kubectl patch serviceaccount default \
	    --namespace=rhsso \
	    -p '{"imagePullSecrets": [{"name": "rh-registry-secret"}]}'
	

KeyCloak Configuration on a local k8s cluster

 helm repo add bitnami https://charts.bitnami.com/bitnami
 helm install pockeycloak bitnami/keycloak --set postgresql.persistence.enabled=false --set service.type=NodePort

 export HTTP_NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[?(@.name=='http')].nodePort}" services pockeycloak)
 export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
 echo "http://${NODE_IP}:${HTTP_NODE_PORT}/"
 
 port forward node port to access keycloak gui
 
 
 minikube start
 kubectl config get-contexts
 kubectl config use-context docker-desktop
  
OLM (Operator Lifecycle Manager) Installed on minikube 
kubectl create -f deploy/upstream/quickstart/crds.yaml
kubectl create -f deploy/upstream/quickstart/olm.yaml

Start and Get minikube dashboard url 
minikube dashboard
minikube dashboard --url
Install operator-sdk 
brew install operator-sdk 
operator-sdk olm install latest




  
  References
  	https://taesunny.github.io/howto/how-to-install-keycloak-in-kubernetes-cluster-with-helm/	*
	https://www.talkingquickly.co.uk/installing-keycloak-kubernetes-helm
	https://bitnami.com/stack/keycloak/helm
	
	https://github.com/operator-framework/operator-lifecycle-manager/blob/master/doc/install/install.md	*
	
	https://suedbroecker.net/2022/03/16/run-an-operator-using-a-bundle-with-an-operator-lifecycle-manager-olm/	*
	
	https://operatorhub.io/operator/keycloak-operator *
	
# Dec 19, 2023 
Deployed RHBK on K8s however failed to deploy RHSSO on K8s 

#Dec 20, 2023 

Run as a contianer fails while starting the container, due to unable to download some extensions. 
docker ps
docker inspect redhat-sso
docker logs redhat-sso
docker cp redhat-sso:./extensions.sh ./extensions.sh
sudo ./extensions.sh 
cat extensions.sh 

#Dec 21, 2023 
- Created cusotm rhsso image, deployed on Docker and local k8s. KeyCloak is running and is accessible within the container, but isn't exposed to the outer world. 


docker run -d --name rhsso -p 8778:8778 -p 8080:8080 -p 8443:8443 -p 8888:8888 -e SSO_HOSTNAME=localhost -e SSO_ADMIN_USERNAME=admin -e SSO_ADMIN_PASSWORD=password -e SSO_REALM=test -e HTTPS_KEYSTORE_DIR=/etc/keystore -e HTTPS_KEYSTORE=keystore.jks -e HTTPS_KEYSTORE_TYPE=jks -e HTTPS_NAME=jboss -e HTTPS_PASSWORD=secret -e JGROUPS_ENCRYPT_KEYSTORE_DIR=/etc/jgroups -e JGROUPS_ENCRYPT_KEYSTORE=jgroups.jceks -e JGROUPS_ENCRYPT_NAME=secret-key -e JGROUPS_ENCRYPT_PASSWORD=secret -e JGROUPS_CLUSTER_PASSWORD=random -e SSO_TRUSTSTORE=truststore.jks -e SSO_TRUSTSTORE_DIR=/etc/truststore -e SSO_TRUSTSTORE_PASSWORD=secret -e EXTENSIONS_VOLUME=/opt/extensions -v $PWD/keystore:/etc/keystore -v $PWD/jgroups:/etc/jgroups custom-rhsso:latest

# Dec 26, and 27 

Fixing Connectivity Issue
cat /proc/net/tcp
Found internal server wildfly using 127.0.0.1 so changed it using Dockrfile to 0.0.0.0

# Dec 28 

# RUNNING RHBK IMAGE 
docker run -d --name rhbk -p 8080:8080 --env KEYCLOAK_ADMIN=admin --env KEYCLOAK_ADMIN_PASSWORD=admin registry.redhat.io/rhbk/keycloak-rhel9:22-6 start-dev

# Dec 29
ansible-playbook deploy-helm-chart.yaml 
Docker, Minikube and Octant Installation on RHEL 
https://infotechys.com/install-minikube-on-rhel9-or-centos9/ 
https://docs.docker.com/engine/install/centos/ 
https://pwittrock.github.io/docs/tasks/tools/install-kubectl/ 
https://infotechys.com/install-minikube-on-rhel9-or-centos9/ 

# Dec 30 

