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

## Step 4: Create the AKS Cluster
### Initiate Terraform
Open the Zip file you donwloaded and go to the <b>./examples/kubernetes/basic-cluster</b> directory. Run the following command to initiate Terraform:
<pre>
$ terraform init
</pre>

You should see an output like this:
<pre>
Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/azurerm...
- Installing hashicorp/azurerm v2.32.0...
- Installed hashicorp/azurerm v2.32.0 (signed by HashiCorp)

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, we recommend adding version constraints in a required_providers block
in your configuration, with the constraint strings suggested below.

* hashicorp/azurerm: version = "~> 2.32.0"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
</pre>

### Generate the Terraform plan for the AKS Cluster
The <b>main.tf</b> script creates an AKS Cluster for us. Inside the same directory, generate the Terraform plan with the following command:
<pre>
terraform plan -out kuma-aks.tfplan
</pre>

You should see the following output. Notice we have to choose a region where the AKS Cluster will be created and a prefix name for all the resources:
<pre>
$ terraform plan -out kuma-aks.tfplan
var.location
  The Azure Region in which all resources in this example should be provisioned

  Enter a value: eastus

var.prefix
  A prefix used for all resources in this example

  Enter a value: kuma

Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_kubernetes_cluster.example will be created
  + resource "azurerm_kubernetes_cluster" "example" {
      + dns_prefix              = "kuma-k8s"
      + fqdn                    = (known after apply)
      + id                      = (known after apply)
      + kube_admin_config       = (known after apply)
      + kube_admin_config_raw   = (sensitive value)
      + kube_config             = (known after apply)
      + kube_config_raw         = (sensitive value)
      + kubelet_identity        = (known after apply)
      + kubernetes_version      = (known after apply)
      + location                = "eastus"
      + name                    = "kuma-k8s"
      + node_resource_group     = (known after apply)
      + private_cluster_enabled = (known after apply)
      + private_fqdn            = (known after apply)
      + private_link_enabled    = (known after apply)
      + resource_group_name     = "kuma-k8s-resources"
      + sku_tier                = "Free"

      + addon_profile {
          + aci_connector_linux {
              + enabled = false
            }

          + azure_policy {
              + enabled = false
            }

          + http_application_routing {
              + enabled                            = false
              + http_application_routing_zone_name = (known after apply)
            }

          + kube_dashboard {
              + enabled = true
            }

          + oms_agent {
              + enabled            = false
              + oms_agent_identity = (known after apply)
            }
        }

      + auto_scaler_profile {
          + balance_similar_node_groups      = (known after apply)
          + max_graceful_termination_sec     = (known after apply)
          + scale_down_delay_after_add       = (known after apply)
          + scale_down_delay_after_delete    = (known after apply)
          + scale_down_delay_after_failure   = (known after apply)
          + scale_down_unneeded              = (known after apply)
          + scale_down_unready               = (known after apply)
          + scale_down_utilization_threshold = (known after apply)
          + scan_interval                    = (known after apply)
        }

      + default_node_pool {
          + max_pods             = (known after apply)
          + name                 = "default"
          + node_count           = 1
          + orchestrator_version = (known after apply)
          + os_disk_size_gb      = (known after apply)
          + type                 = "VirtualMachineScaleSets"
          + vm_size              = "Standard_DS2_v2"
        }

      + identity {
          + principal_id = (known after apply)
          + tenant_id    = (known after apply)
          + type         = "SystemAssigned"
        }

      + network_profile {
          + dns_service_ip     = (known after apply)
          + docker_bridge_cidr = (known after apply)
          + load_balancer_sku  = (known after apply)
          + network_plugin     = (known after apply)
          + network_policy     = (known after apply)
          + outbound_type      = (known after apply)
          + pod_cidr           = (known after apply)
          + service_cidr       = (known after apply)

          + load_balancer_profile {
              + effective_outbound_ips    = (known after apply)
              + idle_timeout_in_minutes   = (known after apply)
              + managed_outbound_ip_count = (known after apply)
              + outbound_ip_address_ids   = (known after apply)
              + outbound_ip_prefix_ids    = (known after apply)
              + outbound_ports_allocated  = (known after apply)
            }
        }

      + role_based_access_control {
          + enabled = (known after apply)

          + azure_active_directory {
              + admin_group_object_ids = (known after apply)
              + client_app_id          = (known after apply)
              + managed                = (known after apply)
              + server_app_id          = (known after apply)
              + server_app_secret      = (sensitive value)
              + tenant_id              = (known after apply)
            }
        }

      + windows_profile {
          + admin_password = (sensitive value)
          + admin_username = (known after apply)
        }
    }

  # azurerm_resource_group.example will be created
  + resource "azurerm_resource_group" "example" {
      + id       = (known after apply)
      + location = "eastus"
      + name     = "kuma-k8s-resources"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

This plan was saved to: kuma-aks.tfplan

To perform exactly these actions, run the following command to apply:
    terraform apply "kuma-aks.tfplan"
</pre>

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
