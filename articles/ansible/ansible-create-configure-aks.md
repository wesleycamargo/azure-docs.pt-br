---
title: Criar e configurar clusters do Serviço de Kubernetes do Azure no Azure usando o Ansible
description: Saiba como usar o Ansible para criar e gerenciar um cluster do Serviço de Kubernetes do Azure no Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, guia estratégico, aks, contêiner, Kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/21/2018
ms.openlocfilehash: de692b29902145e44a055680d662c16ed90c56c2
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617168"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Criar e configurar clusters do Serviço de Kubernetes do Azure no Azure usando o Ansible
O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar seu AKS (Serviço de Contêiner do Azure). Este artigo mostra como usar o Ansible para criar e configurar um cluster do Serviço de Kubernetes do Azure.

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- **Entidade de serviço do Azure** – ao [criar a entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), observe os seguintes valores: **appId**, **displayName**, **senha** e **locatário**.

- **Configurar o Azure Cloud Shell** ou **Instalar e configurar o Ansible em uma máquina virtual do Linux**

  **Configurar o Azure Cloud Shell**

  1. **Configurar o Azure Cloud Shell** - Se o Azure Cloud Shell for uma novidade para você, o artigo [Início Rápido para Bash no Azure Cloud Shell](/azure/cloud-shell/quickstart) ilustra como começar a usar e configurar o Cloud Shell. 

  **--OU--**

  **Instalar e configurar o Ansible em uma máquina virtual do Linux**

  1. **Instalar o Ansible** - Instalar o Ansible em uma [plataforma com suporte para Linux](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Configurar o Ansible** - [Criar credenciais do Azure e configurar o Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

> [!Note]
> O Ansible 2.6 é necessário para executar, neste tutorial, os guias estratégicos de exemplo a seguir. 

## <a name="create-a-managed-aks-cluster"></a>Criar um cluster do AKS gerenciado
O guia estratégico de exemplo do Ansible a seguir cria um grupo de recursos e um cluster do AKS que reside no grupo de recursos:

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

Os seguintes marcadores ajudam a explicar o código do guia estratégico do Ansible acima:
- A primeira seção dentro **tasks** define um grupo de recursos denominado **myResourceGroup** dentro do local **eastus**. 
- A segunda seção dentro de **tasks** define um cluster do AKS denominado **myAKSCluster** dentro do grupo de recursos **myResourceGroup**. 

Para criar o cluster do AKS com o Ansible, salve o guia estratégico de exemplo anterior como `azure_create_aks.yml` e execute o guia estratégico com o seguinte comando:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

A saída do comando **ansible-playbook* é semelhante ao seguinte, mostrando que o cluster do AKS foi criado com êxito:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Dimensionar nós do AKS

O guia estratégico de exemplo na seção anterior define dois nós. Se planeja ter mais ou menos cargas de trabalho de contêiner no cluster, você pode ajustar o número de nós com facilidade. O guia estratégico de exemplo nesta seção aumenta o número de nós de dois nós para três. A modificação da contagem de nós é feita alterando o valor **count** no bloco **agent_pool_profiles**. 

Insira seu próprio `ssh_key`, `client_id` e `client_secret` no bloco **service_principal**:

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Para dimensionar o cluster do serviço Kubernetes do Azure com o Ansible, salve o guia estratégico anterior como *azure_configure_aks.yml* e execute o guia estratégico da seguinte maneira:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

A saída a seguir mostra que o cluster do AKS foi criado com êxito:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Excluir um cluster do AKS gerenciado

A seção de exemplo de guia estratégico Ansible a seguir ilustra como excluir um cluster do AKS:

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name: 
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Para excluir o cluster do Serviço de Kubernetes do Azure com o Ansible, salve o guia estratégico anterior como *azure_delete_aks.yml* e execute o guia estratégico da seguinte maneira:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

A saída a seguir mostra que o cluster do AKS foi excluído com êxito:
  ```bash
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Tutorial: dimensionar o aplicativo no AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
