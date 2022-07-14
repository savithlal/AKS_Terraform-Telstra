Read me

**main.tf**

First we will create a "main.tf" file in which we will specify all the
details regarding cluster which are as follows:

-   We will specify a resource group for our cluster which is mentioned
    > as follows:

> resource \"azurerm_resource_group\" \"rg\"
>
> In which our cluster will reside and inside which we will specify the
> details like **NAME** and **LOCATION** of the resource group.

-   Then we will specify the configuration of our AKS cluster which is
    > mentioned below:

> resource \"azurerm_kubernetes_cluster\" \"aks\"
>
> In which we will define and configure details of our "aks" cluster.
>
> Details are as follows:

1.  **NAME**: Name of the cluster

2.  **LOCATION**: Location of the cluster.

3.  **KUBERNETES VERSION:** The version of kubernetes we are currently
    > using.

4.  **RESOURCE GROUP NAME**: Name of the resource group we are using
    > which is already defined in the resource group in our "main.tf"
    > file. (above)

5.  **DNS PREFIX**: the prefix of DNS we are using.

6.  **NODE RESOURCE GROUP**: the name of the resource group of nodes
    > which will reside inside our cluster.

-   Then we will specify the configuration of the node pool which is
    > mentioned below:

> default_node_pool
>
> In which we will define and configure details of the node pool which
> will be present inside our cluster.
>
> Details are as follows:

1.  **NAME**: Name of the node pool.

2.  **NODE COUNT**: The number of nodes we want in our cluster.

3.  **VM SIZE**: The size of the VM we want to specify and configure
    > which we will choose from azure cloud depending upon our use case.

4.  **TYPE**: The type of virtual machine we will use for the
    > configuration (In this case

> "**VirtualMachineScaleSets**" )

5.  **AVAILABILITY ZONE**: The number of zones should be available will
    > be defined here.

6.  **ENABLING AUTOSCALING**: Here we will define if we want to
    > autoscale our cluster or not.It will be in the boolean form.

-   Then we will define the IDENTITY and network profile (in network
    > profile we will specify type of loadbalancer.)Details are as
    > follows:

> identity {
>
> type = \"SystemAssigned\"
>
> }
>
> network_profile {
>
> load_balancer_sku = \"Standard\"
>
> network_plugin = \"kubenet\" \# azure (CNI)
>
> }

-   Then we will specify "Role based Access Control" and will specify if
    > we want to enable it or not. Details are as follows:

> role_based_access_control {
>
> enabled = true
>
> }

-   Then we will specify **NETWORK SECURITY GROUP** and **NETWORK
    > SECURITY GROUP RULES**. Details are as follows:

> resource \"azurerm_network_security_group\" \"nsg\" {
>
> name = \"acceptanceTestSecurityGroup2\"
>
> location = var.location
>
> resource_group_name = var.resource_group_name
>
> }
>
> resource \"azurerm_network_security_rule\" \"nsg1\" {
>
> name = \"test123\"
>
> priority = 100
>
> direction = \"Inbound\"
>
> access = \"Allow\"
>
> protocol = \"Tcp\"
>
> source_port_range = \"\*\"
>
> destination_port_range = \"3389\"
>
> source_address_prefix = \"\*\"
>
> destination_address_prefix = \"\*\"
>
> resource_group_name = var.resource_group_name
>
> network_security_group_name = \"acceptanceTestSecurityGroup2\"
>
> }

**variables.tf**

Then we will create a "variables.tf" file in which we will specify all
the details of the values to the configuration we want to specify in our
cluster.

Details of the variables we will use are as follows:

1.  **RESOURCE GROUP NAME**: the variable for the resource group name
    > that we will specify in our "main.tf". Details are as follows:

type = string

description = \"aks_terraform_rg2\"

2.  **LOCATION**: the variable for the Location where our resource group
    > and cluster will reside which we will specify in our "main.tf"
    > file. Details are as follows:

> type = string
>
> description = \"Resources location in Azure\"

3.  **NAME**: the variable for the Name of our cluster which we will
    > specify here but function for this variable we be specified in the
    > "main.tf" file. Details are as follows:

type = string

description = \"AKS name in Azure\"

Similarly we will specify the variables of **KUBERNETES VERSION, NODE
RESOURCE GROUP** and **NODE COUNT** which we have specified in our
"main.tf" file.

Details are as follows, respectively:

type = string

description = \"Kubernetes version\"

variable \"node_resource_group\" {

type = string

description = \"aks_terraform_resources_rg2\"

}

variable \"system_node_count\" {

type = number

description = \"Number of AKS worker nodes\"

}

**output.tf**

Then we will create "output.tf". Here we will specify the names of the
configuration we want to see at the time of creation of the cluster.

**NOTE: we can change according to the client\'s needs and
requirements.**

Here we will specify **AKS_ID , AKS_FQDN (Fully Qualified Domain Name)**

And **AKS_Node_Resource_Group**

1.  **AKS_ID** : ID of our AKS Kubernetes cluster (resource ID)

2.  **AKS_FQDN**: Which is responsible for the routing of internal
    > services and pods inside our AKS Kubernetes cluster.

3.  **AKS_Node_Resource_Group**: Resource group of the nodes as
    > mentioned above.

Details are as follows:

output \"aks_id\" {

value = azurerm_kubernetes_cluster.aks.id

}

output \"aks_fqdn\" {

value = azurerm_kubernetes_cluster.aks.fqdn

}

output \"aks_node_rg\" {

value = azurerm_kubernetes_cluster.aks.node_resource_group

}

**providers.tf**

Then we will create a "providers.tf" file. As we know we are creating or
launching our kubernetes cluster on Azure cloud. So in this file we will
specify the type or the name and version of the cloud provider as well
as that of Terraform.

Details are as follows:

provider \"azurerm\" {

features {}

}

terraform {

required_providers {

azurerm = {

source = \"hashicorp/azurerm\"

version = \"2.78.0\"

}

}

}

**terraform.tfvars**

Then we will create a "terraform.tfvars" file.Here also we will define
the variables that we had already specified in our "main.tf" file like
we have done in case of "variables.tf" file.

tfvars and variables.tf serve a similar role, as in both are variable
files. However, **a tfvars file stores the default values from a
variables.tf file and allows you to override values if required**.

Details are as follows:

resource_group_name = \"aks_terraform_rg2\"

location = \"West Europe\"

cluster_name = \"terraform-aks44\"

kubernetes_version = \"1.22.6\"

system_node_count = 3

node_resource_group = \"aks_terraform_resources_rg2\"

**Commands and steps:**

Before you start creating clusters and utilizing Terraform, you have to
install the Azure CLI.

The Azure Command-Line Interface (CLI) is a cross-platform command-line
tool that can be installed locally on Windows computers.

For Windows, the Azure CLI is installed via a MSI, which gives you
access to the CLI through the Windows Command Prompt (CMD) or
PowerShell.

The MSI distributable is used for installing or updating the Azure CLI
on Windows.

1.  Start PowerShell as administrator and run the following command:

**\$ProgressPreference = \'SilentlyContinue\'; Invoke-WebRequest -Uri
https://aka.ms/installazurecliwindows -OutFile .\\AzureCLI.msi;
Start-Process msiexec.exe -Wait -ArgumentList \'/I AzureCLI.msi
/quiet\'; rm .\\AzureCLI.msi**

**This will download and install the latest version of the Azure CLI for
Windows. If you already have a version installed, the installer will
update the existing version.**

2\. The CLI provides an in-tool command to update to the latest version:

**az upgrade**

After you install the Azure CLI and update it, you should run:

**az version**

After that,

\"azure-cli\": \"2.18.0\",

\"azure-cli-core\": \"2.18.0\",

\"azure-cli-telemetry\": \"1.0.6\",

\"extensions\": {}

If you can see the above output, that means the installation is
successful.

Next, you need to link your account to the Azure CLI, and you can do
this with command:

**az login**

This will open a login page where you can authenticate with your
credentials.

Once completed, you should see the \"You have logged in. Now let us find
all the subscriptions to which you have access...\" and your
subscription output in JSON format.

You can now try listing all your AKS clusters(if any) with command:

**az aks list.**

**Terraform commands**

**make sure terraform CLI is installed**

terraform

**format the tf files**

terraform fmt

**initialize terraform Azure modules**

terraform init

**validate the template**

terraform validate

**plan and save the infra changes into tfplan file**

terraform plan -out tfplan

**show the tfplan file**

terraform show -json tfplan

terraform show -json tfplan \>\> tfplan.json

**Format tfplan.json file**

terraform show -json tfplan \| jq \'.\' \> tfplan.json

**show only the changes**

cat tfplan.json \| jq -r \'(.resource_changes\[\] \|
\[.change.actions\[\], .type, .change.after.name\]) \| \@tsv\'

cat tfplan.json \| jq \'\[.resource_changes\[\] \| {type: .type, name:
.change.after.name, actions: .change.actions\[\]}\]\'

**apply the infra changes**

terraform apply tfplan

**delete the infra**

terraform destroy

**cleanup files**

rm terraform.tfstate

rm terraform.tfstate.backup

rm tfplan

rm tfplan.json

rm -r .terraform/
