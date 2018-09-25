---
title: Usar o Ansible para criar uma máquina virtual do Linux no Azure
description: Saiba como usar o Ansible para criar uma máquina virtual do Linux no Azure
ms.service: ansible
keywords: ansible, azure, devops, máquina virtual
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/22/2018
ms.openlocfilehash: bf2116d7fc143e66309e3bbbdefc50bfa33f1521
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974554"
---
# <a name="use-ansible-to-create-a-linux-virtual-machine-in-azure"></a>Usar o Ansible para criar uma máquina virtual do Linux no Azure
Usando uma linguagem declarativa, o Ansible permite automatizar a criação, configuração e implantação dos recursos do Azure por meio dos *guias estratégicos* do Ansible. Cada seção deste artigo mostra a aparência de cada seção de um guia estratégico do Ansible para criar e configurar os diferentes aspectos de uma máquina virtual do Linux. O [guia estratégico completo do Ansible](#complete-sample-ansible-playbook) está listado no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure** - Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
O Ansible precisa de um grupo de recursos para implantar todos os seus recursos. A seção do guia estratégico de exemplo do Ansible a seguir cria um grupo de recursos denominado `myResourceGroup` no local `eastus`:

```yaml
- name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Ao criar uma máquina virtual do Azure, você deve criar uma [rede virtual](/azure/virtual-network/virtual-networks-overview) ou usar uma rede virtual existente. Você também precisa decidir como suas máquinas virtuais devem ser acessadas na rede virtual. A seção a seguir do guia estratégico de exemplo do Ansible cria uma rede virtual chamada `myVnet` no espaço de endereço `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Todos os recursos do Azure implantados em uma rede virtual são implantados em uma [sub-rede](/azure/virtual-network/virtual-network-manage-subnet) dentro de uma rede virtual. 

A seção a seguir do guia estratégico de exemplo do Ansible cria uma sub-rede chamada `mySubnet` na rede virtual `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público
[Endereços IP públicos](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) permitem recursos de Internet para comunicar a entrada para recursos do Azure. Endereços IP públicos também habilitam recursos do Azure para comunicar a saída à Internet e serviços do Azure voltados ao público com um endereço IP atribuído ao recurso. O endereço é dedicado para o recurso, até que ele seja desatribuído por você. Se um endereço IP público não está atribuído a um recurso, o recurso ainda pode comunicar a saída à Internet, mas o Azure atribui dinamicamente um endereço IP disponível que não é dedicado ao recurso. 

A seção a seguir do guia estratégico de exemplo do Ansible cria um endereço IP público chamado `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Um [grupo de segurança de rede](/azure/virtual-network/security-overview) permite filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure. Um grupo de segurança de rede contém regras de segurança que permitem ou negam o tráfego de rede de entrada ou de saída em relação a vários tipos de recursos do Azure. 

A seção do guia estratégico de exemplo do Ansible a seguir cria um grupo de segurança de rede denominado `myNetworkSecurityGroup` e define uma regra para permitir o tráfego de SSH na porta TCP 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-a-virtual-network-interface-card"></a>Criar uma placa de adaptador de rede virtual
Uma placa de adaptador de rede virtual conecta-se à máquina virtual para uma determinada rede virtual, um endereço IP público e um grupo de segurança de rede. 

A seção a seguir do guia estratégico de exemplo do Ansible cria uma placa de adaptador da rede virtual denominada `myNIC` conectada aos recursos da rede virtual que você criou:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A etapa final é criar uma máquina virtual que usa todos os recursos que você criou nas seções anteriores deste artigo. 

A seção de guia estratégico de exemplo do Ansible apresentada nesta seção cria uma máquina virtual denominada `myVM` e anexa a placa de adaptador de rede virtual denominada `myNIC`. Substitua o espaço reservado &lt;your-key-data> pelos seus próprios dados de chave pública completos.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Guia estratégico de exemplo do Ansible completo

Esta seção lista todo o guia estratégico de exemplo do Ansible que você criou ao longo deste artigo. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Executar o guia estratégico de exemplo do Ansible

Esta seção orienta você na execução do guia estratégico de exemplo do Ansible apresentado neste artigo.

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um arquivo (para conter seu guia estratégico) chamado `azure_create_complete_vm.yml`, e abra-o no editor VI da seguinte maneira:

  ```azurecli-interactive
  vi azure_create_complete_vm.yml
  ```

1. Entre no modo de inserção selecionando a tecla **I**.

1. Cole [todo o guia estratégico de exemplo do Ansible](#complete-sample-ansible-playbook) no editor.

1. Saia do modo de inserção selecionando a tecla **Esc**.

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

1. Execute o guia estratégico de exemplo do Ansible.

  ```bash
  ansible-playbook azure_create_complete_vm.yml
  ```

1. A saída é semelhante ao seguinte exemplo, onde você pode ver que uma máquina virtual foi criada com êxito:

  ```bash
  PLAY [Create Azure VM] ****************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Create resource group] *********************************************
  changed: [localhost]

  TASK [Create virtual network] *********************************************
  changed: [localhost]

  TASK [Add subnet] *********************************************************
  changed: [localhost]

  TASK [Create public IP address] *******************************************
  changed: [localhost]

  TASK [Dump public IP for VM which will be created] ********************************************************************
  ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
  }

  TASK [Create Network Security Group that allows SSH] **********************
  changed: [localhost]

  TASK [Create virtual network inteface card] *******************************
  changed: [localhost]

  TASK [Create VM] **********************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0
  ```

1. O comando SSH é usado para acessar sua VM do Linux. Substitua o espaço reservado &lt;ip-address> pelo endereço IP da etapa anterior.

  ```bash
  ssh azureuser@<ip-address>
  ```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Usar o Ansible para gerenciar uma máquina virtual do Linux no Azure](./ansible-manage-linux-vm.md)
