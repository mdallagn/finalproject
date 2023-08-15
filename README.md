#Building Docker Images for Database and Web Application

docker build -f Dockerfile_mysql -t sql .
docker build -t webapp .

#Running the sql and webapp containers from the images
docker run -d --name sql_container  -e MYSQL_ROOT_PASSWORD=db123 sql
docker run -d --name flaskapp -p 80:81 -e DBPWD=db123 -e DBPORT=3306 -e DBHOST=172.17.0.2 webapp

#Verifying using curl
docker exec -it flaskapp -- bash 
root# curl localhost:81

# Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv -v /tmp/eksctl /usr/local/bin

# Enable eksctl bash completion
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
# create cluster
eksctl create cluster -f eks_config.yaml

#Deploy Manifests

cd kubernetes/manifests
kubectl create ns final 
kubectl apply -f serviceaccount.yaml -n final
kubectl apply -f secret.yaml -n final
kubectl apply -f mysql_deployment.yaml -n final
kubectl apply -f mysql_clusterip.yaml -n final
kubectl apply -f Configmap.yaml -n final
kubectl apply -f webapp_deployment.yaml -n final
kubectl apply -f nodeport.yaml -n final

#Verify the Application through Browser
<IPAddress of the Node from EKS Cluster>:30000
