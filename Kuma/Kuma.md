# Kuma Service Mesh

The Kuma Service Mesh deployment is based on a Terraform template available at https://github.com/terraform-providers/terraform-provider-azurerm/tree/master/examples/kubernetes/basic-cluster. This template based offer is meant for pre-production and production environments where customers want to leverage Kuma Service Mesh capabilities, running on an AKS Cluster.

For more information about the template, please refer to: https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html


#  Installation Process

## Step 1: Install Azure CLI and Terraform
Open a terminal and install Azure CLI as described [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

The Terraform installation process is documented [here](https://learn.hashicorp.com/tutorials/terraform/install-cli)


## Step 2: Download Terraform Provider Azure Resource Manager repository
![TerraformProvider](https://github.com/Kong/azure-marketplace/blob/master/Kuma/screenshots/github.png)


## Step 3: Login to Azure
Login to Azure with the following command:
<pre>
az login
</pre>
You will be redirected to Azure Portal page. Use your Azure credentials to login. After getting authenticated, you should receive a response like this:

<pre>
You have logged in. Now let us find all the subscriptions to which you have access...
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "f177c1d6-zzz-yyy-818a-xxxxxxxd8d",
    "id": "aaaaaa95-bbbd-4fff-b2ba-0ooooooo7948",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Subscription1",
    "state": "Enabled",
    "tenantId": "f177c1d6-zzz-yyy-818a-xxxxxxxd8d",
    "user": {
      "name": "email@youcompany.com",
      "type": "user"
    }
  }
]
</pre>


## Step 3: Generate the Terraform plan
Open the Zip file you donwloaded and go to the <b>./examples/kubernetes/basic-cluster</b> directory. Run the following command to generate the Terraform plan:
<pre>
terraform plan -out kuma-aks.tfplan
</pre>

or you can use the CloudFormation Stack [Wizard](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=eks-kuma&templateURL=https://kuma-cloudformation.s3.amazonaws.com/amazon-eks.yaml)

![CloudFormation](https://github.com/Kong/aws-marketplace/blob/master/Kuma/screenshots/CF-step1.png)

Click on <b>Next</b>

![CloudFormation](https://github.com/Kong/aws-marketplace/blob/master/Kuma/screenshots/CF-step2.png)

Click on <b>Next</b> and on <b>Next</b> again to go the <b>Review Page</b>

![CloudFormation](https://github.com/Kong/aws-marketplace/blob/master/Kuma/screenshots/CF-step3.png)

On the bottom of the page click on both check boxes: <b>I acknowledge that AWS CloudFormation might create IAM resources with custom names.</b> and <b>I acknowledge that AWS CloudFormation might require the following capability: CAPABILITY_AUTO_EXPAND</b>.

Click on "Create stack"



### Checking your EKS Cluster

This checking assumes you have aws cli and kubectl commands installed locally

### Listing your Clusters
<pre>
$ aws eks list-clusters
{
    "clusters": [
        "eks-kuma"
    ]
}
</pre>

### Configure kubectl locally with your EKS Cluster info
<pre>
$ aws eks --region eu-central-1 update-kubeconfig --name eks-kuma
</pre>

### Get your Cluster info
<pre>
$ kubectl cluster-info
Kubernetes master is running at https://43C4C665F3F1283D711B41CDFE53FA2D.gr7.eu-central-1.eks.amazonaws.com
CoreDNS is running at https://43C4C665F3F1283D711B41CDFE53FA2D.gr7.eu-central-1.eks.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
</pre>


### Checking your Pods
<pre>
$ kubectl get pod --all-namespaces
NAMESPACE     NAME                      READY   STATUS    RESTARTS   AGE
kube-system   aws-node-qg8gg            1/1     Running   0          111m
kube-system   coredns-5fdf64ff8-9pk9n   1/1     Running   0          125m
kube-system   coredns-5fdf64ff8-tbbz9   1/1     Running   0          125m
kube-system   kube-proxy-54vxr          1/1     Running   0          111m
</pre>

### Checking your Services
<pre>
$ kubectl get services --all-namespaces
NAMESPACE     NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)         AGE
default       kubernetes   ClusterIP   172.20.0.1    <none>        443/TCP         125m
kube-system   kube-dns     ClusterIP   172.20.0.10   <none>        53/UDP,53/TCP   125m
</pre>



## Step 3: Deploy Kuma Service Mesh

### Deploy Kuma Service Mesh with AWS CLIS

<pre>
aws cloudformation create-stack --stack-name kuma --template-url \
https://kuma-cloudformation.s3.amazonaws.com/kuma.yaml \
--parameters \
ParameterKey=Cluster,ParameterValue=eks-kuma
</pre>

or you can use the [CloudFormation Stack](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=kuma&templateURL=https://kuma-cloudformation.s3.amazonaws.com/kuma.yaml)


![CloudFormation](https://github.com/Kong/aws-marketplace/blob/master/Kuma/screenshots/CF-step4.png)



## Step 4: Checking Kuma Service Mesh deployment

<pre>
$ kubectl get pod --all-namespaces
NAMESPACE     NAME                                  READY   STATUS    RESTARTS   AGE
default       kuma-control-plane-7db4999799-hzmrc   1/1     Running   0          20s
kube-system   aws-node-qg8gg                        1/1     Running   0          126m
kube-system   coredns-5fdf64ff8-9pk9n               1/1     Running   0          139m
kube-system   coredns-5fdf64ff8-tbbz9               1/1     Running   0          139m
kube-system   kube-proxy-54vxr                      1/1     Running   0          126m
</pre>

<pre>
$ kubectl get service --all-namespaces
NAMESPACE     NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                  AGE
default       kubernetes           ClusterIP   172.20.0.1       <none>        443/TCP                                                                  140m
default       kuma-control-plane   ClusterIP   172.20.109.231   <none>        5681/TCP,443/TCP,5676/TCP,5677/TCP,5678/TCP,5679/TCP,5682/TCP,5653/UDP   35s
kube-system   kube-dns             ClusterIP   172.20.0.10      <none>        53/UDP,53/TCP                                                            140m
</pre>

Check Kuma GUI also. On on terminal expose the Control Plane port

<pre>
$ kubectl port-forward service/kuma-control-plane 5681:5681
Forwarding from 127.0.0.1:5681 -> 5681
Forwarding from [::1]:5681 -> 5681
</pre>

Redirect your browser to http://localhost:5681/gui and click on <b>Skip to Dashboard</b>
![KumaGUI](https://github.com/Kong/aws-marketplace/blob/master/Kuma/screenshots/GUI.png)


## Kuma official documentation

Browse this [guides](https://kuma.io/docs) to get started configuring Kuma.
