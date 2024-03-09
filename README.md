step-1:eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
----------------------------------------------------------------------------------
step-2:curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
----------------------------------------------------------------------------------
step-3:Create IAM Policy
       aws iam create-policy \
           --policy-name AWSLoadBalancerControllerIAMPolicy \
           --policy-document file://iam_policy.json
----------------------------------------------------------------------------------	 
step-4:eksctl create iamserviceaccount \
         --cluster=<your-cluster-name> \
         --namespace=kube-system \
         --name=aws-load-balancer-controller \
         --role-name AmazonEKSLoadBalancerControllerRole \
         --attach-policy-arn=arn:aws:iam::<your-aws-account- 
                                                  id>:policy/AWSLoadBalancerControllerIAMPolicy \
         --approve
--------------------------------------------------------------------------------------
DEPLOY ALB CONTROLLER
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
---------------------------------------------------------------------------------
INSTALL
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<region> \
  --set vpcId=<your-vpc-id>
-------------------------------------------------------------------------
VERIFY THAT THE DEPLOYMENTS ARE RUNNING
kubectl get deployment -n kube-system aws-load-balancer-controller or kubectl get deployment -n kube-system aws-load-balancer-controller -w
--------------------------------------------------------------------------
kubectl get pods -n kube-system -w
-------------------------------------------------------------------------
kubectl get deployment -n kube-system aws-load-balancer-controller -w
--------------------------------------------------------------------------
kubectl get ingress -n game-2048  #we get load balancer DNS
