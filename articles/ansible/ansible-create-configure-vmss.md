---
title: Criar conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible
description: Saiba como usar o Ansible para criar e configurar um conjunto de dimensionamento de máquinas virtuais no Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: f3b08c41d3bf083c7cca5897cee11a1a4b9c9092
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918568"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Criar conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible
O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar o VMSS (conjunto de dimensionamento de máquinas virtuais) no Azure, da mesma forma como você gerenciaria qualquer outro recurso do Azure. Este artigo mostra como usar o Ansible para criar e expandir um conjunto de dimensionamento de máquinas virtuais. 

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.6 é necessário para executar, neste tutorial, os guias estratégicos de exemplo a seguir. 

## <a name="create-a-vmss"></a>Criar um VMSS
Esta seção apresenta um guia estratégico de exemplo do Ansible que define os seguintes recursos:
- Um **grupo de recursos**, no qual todos os recursos serão implantados
- Uma **rede virtual** no espaço de endereço 10.0.0.0/16
- Uma **sub-rede** dentro da rede virtual
- Um **endereço IP público** que permite acessar recursos pela Internet
- Um **grupo de segurança de rede**, que controla o fluxo de entrada e saída de tráfego de rede do conjunto de dimensionamento de máquinas virtuais
- Um **balanceador de carga**, que distribui o tráfego entre um conjunto de VMs definidas usando regras do balanceador de carga
- Um **conjunto de dimensionamento de máquinas virtuais** que usa todos os recursos criados

Insira sua senha para o valor *admin_password*.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Para criar o ambiente de conjunto de dimensionamento de máquinas virtuais com o Ansible, salve o guia estratégico anterior como `vmss-create.yml` ou então [baixe o guia estratégico de exemplo do Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Depois de executar o guia estratégico, uma saída semelhante ao exemplo a seguir mostra que o conjunto de dimensionamento de máquinas virtuais foi criado com êxito:

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Expandir um VMSS
O conjunto de dimensionamento de máquinas virtuais criado tem duas instâncias. Se você navegar até o conjunto de dimensionamento de máquinas virtuais definido no portal do Azure, você verá **Standard_DS1_v2 (2 instâncias)**. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/) executando nele o seguinte comando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Você verá resultados semelhantes à seguinte saída:

  ```bash
  {
    "capacity": 2,
  }
  ```

Agora, expandiremos de duas instâncias para três instâncias. O código de guia estratégico do Ansible a seguir recupera informações sobre o conjunto de dimensionamento de máquinas virtuais e altera sua capacidade de duas para três. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Para expandir o conjunto de dimensionamento de máquinas virtuais que você criou, salve o guia estratégico anterior como `vmss-scale-out.yml` ou então [baixe o guia estratégico de exemplo do Ansible](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

O comando a seguir executará o guia estratégico:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

A saída da execução do guia estratégico do Ansible mostra que o conjunto de dimensionamento de máquinas virtuais foi expandido com êxito:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Se navegar até o conjunto de dimensionamento de máquinas virtuais que configurou no portal do Azure, você verá **Standard_DS1_v2 (3 instâncias)**. Você também pode verificar a alteração com o [Azure Cloud Shell](https://shell.azure.com/) executando o seguinte comando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Os resultados de executar o comando no Cloud Shell mostram que agora existem três instâncias. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Guia estratégico de exemplo do Ansible para VMSS](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)