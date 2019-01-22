---
title: Criar, alterar ou excluir uma tabela de rotas do Azure usando o Ansible
description: Aprender a usar o Ansible para criar, alterar ou excluir uma tabela de rotas
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 29672a75408e42fb9239e5d826784b46e7280805
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332261"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Criar, alterar ou excluir uma tabela de rotas do Azure usando o Ansible
O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão no Azure, poderá criar uma [tabela de rotas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) para fazer isso.

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Este artigo mostra como criar, alterar ou excluir uma tabela de rotas do Azure e anexar a tabela de rotas a uma sub-rede também. 

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os guias estratégicos de exemplo contidos neste tutorial.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas
Esta seção apresenta um guia estratégico de exemplo do Ansible que cria uma tabela de rotas. Há um limite para o número de tabelas de rotas que você pode criar por assinatura e local do Azure. Para obter detalhes, confira [Limites do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Salve este guia estratégico como `route_table_create.yml`. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede
Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. Uma tabela de rotas pode ser associada a zero ou várias sub-redes. Já que tabelas de rotas não são associadas a redes virtuais, você precisa associar uma tabela de rotas a cada sub-rede à qual você deseje associar a tabela de rotas. Todo o tráfego deixando a sub-rede é roteado com base nas rotas que você criou nas tabelas de rota, [as rotas padrão](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default) e as rotas propagadas de uma rede local, se a rede virtual está conectada a um gateway de rede virtual do Azure (ExpressRoute, ou então VPN caso o BGP esteja sendo usado com um gateway de VPN). Você só pode associar uma tabela de rotas a sub-redes em redes virtuais que existem no mesmo local e assinatura do Azure que a tabela de rotas.

Esta seção apresenta um guia estratégico de exemplo do Ansible que cria uma rede virtual e uma sub-rede e, em seguida, associa uma tabela de rotas à sub-rede.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Salve este guia estratégico como `route_table_associate.yml`. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar uma tabela de rotas de uma sub-rede
Quando você desassocia uma tabela de rotas de uma sub-rede, basta definir a `route_table` em uma sub-rede para `None`. Abaixo está um guia estratégico de exemplo do Ansible. 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Salve este guia estratégico como `route_table_dissociate.yml`. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Criar uma rota
Esta seção apresenta um guia estratégico de exemplo do Ansible que cria uma rota sob a tabela de rotas. Ele define `virtual_network_gateway` como `next_hop_type` e `10.1.0.0/16` como `address_prefix`. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Para saber mais sobre como o Azure seleciona rotas e uma descrição detalhada de todos os próximos tipos do salto, confira [Visão geral de roteamento](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
Salve este guia estratégico como `route_create.yml`. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Excluir uma rota
Esta seção apresenta um guia estratégico de exemplo do Ansible que exclui uma rota de uma tabela de rotas.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Salve este guia estratégico como `route_delete.yml`. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Obter informações de uma tabela de rotas
Você pode exibir detalhes de uma route_table por meio do módulo do Ansible chamado `azure_rm_routetable_facts`. O módulo de fatos retornará as informações da tabela de rotas com todas as rotas anexadas a ela.
Abaixo está um guia estratégico de exemplo do Ansible. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Salve este guia estratégico como `route_table_facts.yml`. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas
Se uma tabela de rotas está associada a alguma sub-rede, ela não pode ser excluída. [Desassocie](#dissociate-a-route-table-from-a-subnet) uma tabela de rota de todas as sub-redes antes de tentar excluí-la.

Você pode excluir a tabela de rotas juntamente com todas as rotas. Abaixo está um guia estratégico de exemplo do Ansible. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Salve este guia estratégico como `route_table_delete.yml`. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)
