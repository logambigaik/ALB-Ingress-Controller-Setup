# ALB Install Ingress Controller:

In Kubernetes, an Ingress is an object that allows access to your Kubernetes services from outside the Kubernetes cluster. You configure access by creating a collection of rules that define which inbound connections reach which services. This lets you consolidate your routing rules into a single resource.

# Create a Kubernetes service account named alb-ingress-controller in the kube-system namespace
	# List Service Accounts
	kubectl get sa -n kube-system
	
	# Create ClusterRole, ClusterRoleBinding & ServiceAccount
	kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/master/docs/examples/rbac-role.yaml
	
	# List Service Accounts
	kubectl get sa -n kube-system
  
	# Describe Service Account alb-ingress-controller 
	kubectl describe sa alb-ingress-controller -n kube-system
	
![image](https://user-images.githubusercontent.com/54719289/113128672-f7894580-9211-11eb-894e-0e2271fe3adc.png)

# Create policy using below json file:
Create AWS IAM Policy "alb-ingress-controller-policy" with below Json code

    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "VisualEditor0",
                "Effect": "Allow",
                "Action": [
                    "wafv2:AssociateWebACL",
                    "ec2:AuthorizeSecurityGroupIngress",
                    "elasticloadbalancing:ModifyListener",
                    "ec2:DescribeInstances",
                    "wafv2:GetWebACLForResource",
                    "elasticloadbalancing:RegisterTargets",
                    "iam:ListServerCertificates",
                    "wafv2:GetWebACL",
                    "elasticloadbalancing:SetIpAddressType",
                    "ec2:DescribeInternetGateways",
                    "elasticloadbalancing:DeleteLoadBalancer",
                    "elasticloadbalancing:SetWebAcl",
                    "waf-regional:GetWebACLForResource",
                    "elasticloadbalancing:DescribeLoadBalancers",
                    "acm:GetCertificate",
                    "waf-regional:GetWebACL",
                    "shield:DescribeSubscription",
                    "elasticloadbalancing:CreateRule",
                    "ec2:DescribeAccountAttributes",
                    "elasticloadbalancing:AddListenerCertificates",
                    "elasticloadbalancing:ModifyTargetGroupAttributes",
                    "waf:GetWebACL",
                    "iam:GetServerCertificate",
                    "wafv2:DisassociateWebACL",
                    "ec2:CreateTags",
                    "shield:GetSubscriptionState",
                    "ec2:ModifyNetworkInterfaceAttribute",
                    "elasticloadbalancing:CreateTargetGroup",
                    "elasticloadbalancing:DeregisterTargets",
                    "ec2:RevokeSecurityGroupIngress",
                    "elasticloadbalancing:DescribeLoadBalancerAttributes",
                    "acm:DescribeCertificate",
                    "elasticloadbalancing:DescribeTargetGroupAttributes",
                    "shield:CreateProtection",
                    "elasticloadbalancing:ModifyRule",
                    "elasticloadbalancing:AddTags",
                    "elasticloadbalancing:DescribeRules",
                    "ec2:DescribeSubnets",
                    "elasticloadbalancing:ModifyLoadBalancerAttributes",
                    "waf-regional:AssociateWebACL",
                    "ec2:DescribeAddresses",
                    "tag:GetResources",
                    "ec2:DeleteTags",
                    "shield:DescribeProtection",
                    "shield:DeleteProtection",
                    "elasticloadbalancing:RemoveListenerCertificates",
                    "tag:TagResources",
                    "elasticloadbalancing:RemoveTags",
                    "elasticloadbalancing:CreateListener",
                    "ec2:DescribeNetworkInterfaces",
                    "elasticloadbalancing:DescribeListeners",
                    "ec2:CreateSecurityGroup",
                    "acm:ListCertificates",
                    "elasticloadbalancing:DescribeListenerCertificates",
                    "ec2:ModifyInstanceAttribute",
                    "elasticloadbalancing:DeleteRule",
                    "cognito-idp:DescribeUserPoolClient",
                    "ec2:DescribeInstanceStatus",
                    "elasticloadbalancing:DescribeSSLPolicies",
                    "elasticloadbalancing:CreateLoadBalancer",
                    "waf-regional:DisassociateWebACL",
                    "ec2:DescribeTags",
                    "elasticloadbalancing:DescribeTags",
                    "elasticloadbalancing:SetSubnets",
                    "elasticloadbalancing:DeleteTargetGroup",
                    "ec2:DescribeSecurityGroups",
                    "iam:CreateServiceLinkedRole",
                    "ec2:DescribeVpcs",
                    "ec2:DeleteSecurityGroup",
                    "elasticloadbalancing:DescribeTargetHealth",
                    "elasticloadbalancing:SetSecurityGroups",
                    "elasticloadbalancing:DescribeTargetGroups",
                    "shield:ListProtections",
                    "elasticloadbalancing:ModifyTargetGroup",
                    "elasticloadbalancing:DeleteListener"
                ],
                "Resource": "*"
            }
        ]
    }
	
![image](https://user-images.githubusercontent.com/54719289/113129192-85653080-9212-11eb-8925-fcb44b2065bd.png)

arn:aws:iam::XXXXXXXXXXXX:policy/alb-ingress-controller-policy

# Create Role and attach policy to a Role:
    eksctl create iamserviceaccount \
        --region us-east-1 \
        --name alb-ingress-controller \
        --namespace kube-system \
        --cluster eksdemo \
        --attach-policy-arn arn:aws:iam::XXXXXXXX:policy/alb-ingress-controller-policy \
	--override-existing-serviceaccounts \
        --approve
	
![image](https://user-images.githubusercontent.com/54719289/113129998-75018580-9213-11eb-9caa-e02f95f03b37.png)

# To check iam service account:
	eksctl  get iamserviceaccount --cluster eksdemo --region us-east-1
![image](https://user-images.githubusercontent.com/54719289/113130045-83e83800-9213-11eb-86a7-44cc9b642c56.png)

	kubectl describe sa alb-ingress-controller -n kube-system
	
![image](https://user-images.githubusercontent.com/54719289/113130208-b6923080-9213-11eb-9246-ca9d9714936b.png)

	kubectl get pods -n kube-system

![image](https://user-images.githubusercontent.com/54719289/113132100-eb9f8280-9215-11eb-9f05-096f7cb81377.png)

# Deploy ALB Ingress Controller
	kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/master/docs/examples/alb-ingress-controller.yaml
	
![image](https://user-images.githubusercontent.com/54719289/113132637-a465c180-9216-11eb-872b-264044700f0c.png)

![image](https://user-images.githubusercontent.com/54719289/113132755-ca8b6180-9216-11eb-816e-9a574592d81c.png)

# For fixing the errod, do the changes in deploy [follow the below steps for editing deploy],
# Verify Deployment
	kubectl get deploy -n kube-system
	
![image](https://user-images.githubusercontent.com/54719289/113133078-31a91600-9217-11eb-9a0e-f47af866e2de.png)

# Edit ALB Ingress Controller Manifest:
	kubectl edit deploy alb-ingress-controller -n kube-system
# Replaced cluster-name with our cluster-name eksdemo1
    spec:
      containers:
      - args:
        - --ingress-class=alb
        - --cluster-name=eksdemo
  ![image](https://user-images.githubusercontent.com/54719289/113133319-6cab4980-9217-11eb-8aa1-f6d494a43898.png)

# Verify our ALB Ingress Controller is running
	# Verify if alb-ingress-controller pod is running
	kubectl get pods -n kube-system
  
	# Verify logs
	kubectl logs -f $(kubectl get po -n kube-system | egrep -o 'alb-ingress-controller-[A-Za-z0-9-]+') -n kube-system
![image](https://user-images.githubusercontent.com/54719289/113133462-95334380-9217-11eb-8b66-e93e003b32cf.png)

	[root@master ~]# kubectl logs alb-ingress-controller-5b96bb464-q5lkp -n kube-system

![image](https://user-images.githubusercontent.com/54719289/113133722-e0e5ed00-9217-11eb-8c8a-5a56de0f457f.png)

