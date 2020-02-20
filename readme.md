


1: make sure you are on us-west-2

2: AWS Cloud9 --> Environments --> Create environment

3:Install kubectl

```
sudo curl --silent --location -o /usr/local/bin/kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl


sudo chmod +x /usr/local/bin/kubectl
```
5: Create and attach an IAM role to your Cloud9 instance

6: update IAM role for your Cloud9 

7: Install EKSCTL

```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp


sudo mv -v /tmp/eksctl /usr/local/bin
```

8: Install jq, envsubst (from GNU gettext utilities) and bash-completion

```
sudo yum -y install jq gettext bash-completion
```


9: switch credentials

```
rm -vf ${HOME}/.aws/credentials
#
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
#
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
#
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

10: double check your credentials:

```
aws sts get-caller-identity

```

11: create your cluster

```
eksctl create cluster --name=eksworkshop-eksctl --nodes=3 --managed --alb-ingress-access --region=${AWS_REGION}

```

12: check you can access the cluster: 

```
kubectl get nodes
```

13: clone the repo with the examples: 

```
git clone https://github.com/marco-aws/deploy-examples.git

```

14: deploy examples

```
kubectl apply -f deploy-examples
```


### Bonus:

install HELM: 

```
cd ~/environment

curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh

chmod +x get_helm.sh

./get_helm.sh
```

init helm:

```
helm reset --force
helm init --service-account tiller
```

install metric server:

```
helm install stable/metrics-server --name metrics-server --version 2.0.4 --namespace metrics
```

