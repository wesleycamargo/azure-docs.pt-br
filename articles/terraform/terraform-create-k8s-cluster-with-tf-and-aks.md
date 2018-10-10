---
title: Criar um cluster do Kubernetes com o Serviço de Kubernetes do Azure (AKS) e o Terraform
description: Tutorial ilustrando como criar um cluster do Kubernetes com o serviço do Azure Kubernetes e o Terraform
services: terraform
ms.service: terraform
keywords: terraform, devops, máquina virtual, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/08/2018
ms.openlocfilehash: f261c59193349d55d407e6079002b75884273e84
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960236"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Criar um cluster do Kubernetes com o Serviço do Azure Kubernetes e o Terraform
[ O Azure Kubernetes Service (AKS) ](/azure/aks/) gerencia seu ambiente hospedado do Kubernetes, tornando rápido e fácil implantar e gerenciar aplicativos em contêiner sem a experiência em orquestração de contêineres. Também elimina a sobrecarga das operações em andamento e a manutenção provisionando, atualizando e dimensionamento os recursos sob demanda, sem colocar seus aplicativos offline.

Neste tutorial, você aprenderá como executar as seguintes tarefas ao criar um cluster [ Kubernetes ](https://www.redhat.com/en/topics/containers/what-is-kubernetes) usando [ Terraform ](http://terraform.io) e AKS:

> [!div class="checklist"]
> * Use HCL (HashiCorp Language) para definir um cluster do Kubernetes
> * Use Terraform e AKS para criar um cluster Kubernetes
> * Use a ferramenta kubectl para testar a disponibilidade de um cluster do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configure o Terraform**: Siga as instruções no artigo, [ Terraform e configure o acesso ao Azure ](/azure/virtual-machines/linux/terraform-install-configure)

- **Entidade de serviço do Azure**: siga as instruções na seção **Criar a entidade de serviço** do artigo [Criar uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Anote os valores para o appId, displayName, senha e inquilino.

## <a name="create-the-directory-structure"></a>Crie a estrutura de diretórios
O primeiro passo é criar o diretório que contém os arquivos de configuração do Terraform para o exercício.

1. Navegue até o [Portal do Azure](http://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se você não selecionou um ambiente anteriormente, selecione **Bash** como o ambiente.

    ![Aviso do Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Altere os diretórios para o novo diretório:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Declarar o provedor do Azure
Crie o arquivo de configuração Terraform que declara o provedor do Azure.

1. No Cloud Shell, crie um arquivo chamado `main.tf`.

    ```bash
    vi main.tf
    ```

1. Entre no modo de inserção selecionando a tecla I.

1. Cole o código a seguir no editor:

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Saia do modo de inserção selecionando a tecla **Esc**.

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Definir um cluster do Kubernetes
Crie o arquivo de configuração do Terraform que declara os recursos para o cluster do Kubernetes.

1. No Cloud Shell, crie um arquivo chamado `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Entre no modo de inserção selecionando a tecla I.

1. Cole o código a seguir no editor:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS2_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    O código anterior define o nome do cluster, local e o resource_group_name. Além disso, o valor dns_prefix - que faz parte do nome de domínio totalmente qualificado (FQDN) usado para acessar o cluster - é definido.

    O registro **linux_profile** permite que você defina as configurações que permitem a assinatura nos nós do trabalhador usando o SSH.

    Com o AKS, você paga apenas pelos nós do trabalhador. O registro **agent_pool_profile** configura os detalhes para esses nós do trabalhador. O **registro agent_pool_profile** inclui o número de nós do trabalhador a serem criados e o tipo de nós do trabalhador. Se você precisar aumentar ou reduzir o cluster no futuro, modifique o valor **count** neste registro.

1. Saia do modo de inserção selecionando a tecla **Esc**.

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Declarar as variáveis

1. No Cloud Shell, crie um arquivo chamado `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Entre no modo de inserção selecionando a tecla I.

1. Cole o código a seguir no editor:

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }
    ```

1. Saia do modo de inserção selecionando a tecla **Esc**.

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Crie um modelo do Terraform
[ As saídas do Terraform ](https://www.terraform.io/docs/configuration/outputs.html) permitem definir valores que serão destacados para o usuário quando o Terraform aplica um plano, e podem ser consultados usando o comando `terraform output`. Nesta seção, você cria um arquivo de saída que permite acesso ao cluster com [ kubectl ](https://kubernetes.io/docs/reference/kubectl/overview/).

1. No Cloud Shell, crie um arquivo chamado `output.tf`.

    ```bash
    vi output.tf
    ```

1. Entre no modo de inserção selecionando a tecla I.

1. Cole o código a seguir no editor:

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Saia do modo de inserção selecionando a tecla **Esc**.

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Configurar o armazenamento do Azure para armazenar o estado Terraform
Terraform rastreia o estado localmente através do arquivo `terraform.tfstate`. Esse padrão funciona bem em um ambiente de uma única pessoa. No entanto, em um ambiente multi-pessoa mais prático, você precisa rastrear o estado no servidor utilizando o [ armazenamento do Azure ](/azure/storage/). Nesta seção, você recupera as informações necessárias da conta de armazenamento (nome da conta e chave da conta) e cria um contêiner de armazenamento no qual as informações do estado Terraform serão armazenadas.

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda.

1. Selecione **Contas de armazenamento**.

1. Na guia **Contas de armazenamento**, selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, você pode usar a conta de armazenamento criada quando você abriu o Cloud Shell pela primeira vez.  O nome da conta de armazenamento criada pelo Cloud Shell geralmente começa com `cs` seguido por uma sequência aleatória de números e letras. **Lembre-se do nome da conta de armazenamento que você selecionou, pois ela é necessária mais tarde.**

1. Na guia da conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu da conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anote o valor da **key1** **key**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. No Cloud Shell, crie um contêiner em sua conta de armazenamento do Azure (substitua os espaços reservados &lt; YourAzureStorageAccountName> e &lt; YourAzureStorageAccountAccessKey> pelos valores apropriados para sua conta de armazenamento do Azure).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Crie o cluster do Kubernetes
Nesta seção, você verá como usar o comando `terraform init` para criar os recursos definidos nos arquivos de configuração criados nas seções anteriores.

1. No Cloud Shell, inicialize o Terraform (substitua os espaços reservados &lt; YourAzureStorageAccountName> e &lt; YourAzureStorageAccountAccessKey> pelos valores apropriados para sua conta de armazenamento do Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    O comando `terraform init` exibe o sucesso da inicialização do plug-in de backend e provedor:

    ![Exemplo de resultados de "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exporte as credenciais da entidade de serviço. Substitua os espaços reservados &lt;your-client-id> e &lt;your-client-secret> pelos valores de **appId** e **senha** associados à entidade de serviço respectivamente.

    ```bash
    export TF_VAR_client_id=<your-client-id>
    export TF_VAR_client_secret=<your-client-secret>
    ```

1. Execute o comando `terraform plan` para criar o plano Terraform que define os elementos da infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` exibe os recursos que serão criados quando você executar o comando `terraform apply`:

    ![Exemplo de resultados do "plano de terraform"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster do Kubernetes pode levar vários minutos, resultando no tempo limite da sessão do Cloud Shell. Se a sessão do Cloud Shell expirar, você poderá seguir as etapas na seção [ "Recuperar de um tempo limite do Cloud Shell" ](#recover-from-a-dloud-shell-timeout) para permitir que você conclua o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` exibe os resultados da criação dos recursos definidos em seus arquivos de configuração:

    ![Exemplo de resultados de "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda para ver os recursos criados para o novo cluster do Kubernetese.

    ![Aviso do Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um limite de tempo do Cloud Shell
Se a sessão do Cloud Shell expirar, você poderá executar as seguintes etapas para recuperar:

1. Inicie uma sessão do Cloud Shell.

1. Mude para o diretório que contém os arquivos de configuração do Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Execute o comando a seguir:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Teste o cluster do Kubernetes
As ferramentas do Kubernetes podem ser usadas para verificar o cluster recém-criado.

1. Obtenha a configuração do Kubernetes do estado Terraform e armazene-a em um arquivo que o kubectl possa ler.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Obtenha a configuração do Kubernetes do estado Terraform e armazene-a em um arquivo que o kubectl possa ler.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Verifique a integridade do cluster.

    ```bash
    kubectl get nodes
    ```

    Você deve ver os detalhes de seus nós de trabalho e todos devem ter um status **Pronto**, conforme mostrado na imagem a seguir:

    ![A ferramenta kubectl permite verificar a integridade do seu cluster do Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Terraform e o AKS para criar um cluster do Kubernetes. A seguir são apresentados alguns recursos adicionais para ajudá-lo a saber mais sobre o Terraform no Azure: 

 [Hub do Terraform no Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentação do provedor do Terraform do Azure](http://aka.ms/terraform)  
 [Origem do provedor do Terraform do Azure](http://aka.ms/tfgit)  
 [Módulos do Terraform do Azure](http://aka.ms/tfmodules)
