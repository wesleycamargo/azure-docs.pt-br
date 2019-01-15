---
title: Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais no Azure usando o Ansible
description: Saiba como usar o Ansible para dimensionar um conjunto de dimensionamento de máquinas virtuais com o dimensionamento automático no Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, dimensionar, dimensionamento automático, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 7721dba37131616122f8a5a902e3c63de5c7157f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157047"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais no Azure usando o Ansible
O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar o VMSS (conjunto de dimensionamento de máquinas virtuais) no Azure, da mesma forma como você gerenciaria qualquer outro recurso do Azure. 

Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo. Neste artigo, você criará uma configuração de dimensionamento automático e a associará a um conjunto de dimensionamento de máquinas virtuais existente. Na configuração de dimensionamento automático, é possível configurar uma regra para expandir ou reduzir horizontalmente, conforme desejado.

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Um conjunto de dimensionamento de máquinas virtuais do Azure existente. – Se não tiver um, [Crie conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> O Ansible 2.7 é necessário para executar os guias estratégicos de exemplo contidos neste tutorial. 

## <a name="auto-scale-based-on-a-schedule"></a>Dimensionamento automático com base em um agendamento   
Para habilitar o dimensionamento automático em um conjunto de dimensionamento, primeiro você define um perfil de dimensionamento automático. Esse perfil define a capacidade padrão, mínima e máxima do conjunto de dimensionamento. Esses limites permitem controlar o custo ao não criar instâncias de VM de forma contínua e equilibra o desempenho aceitável com um número mínimo de instâncias que permanecem em um evento de redução. 

É possível expandir e reduzir horizontalmente Conjuntos de Dimensionamento de Máquinas Virtuais, em um agendamento recorrente ou até uma determinada data. Esta seção apresenta um exemplo de guia estratégico do Ansible que cria uma configuração de dimensionamento automático que aumenta o número de instâncias de VM para três em seus conjuntos de dimensionamento às 10h de toda segunda-feira, fuso horário do Pacífico. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Salve este guia estratégico como *vmss-auto-scale.yml*. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Dimensionamento automático com base em dados de desempenho
Se a demanda do seu aplicativo aumentar, a carga nas instâncias de VM em seus conjuntos de dimensionamento aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

É possível expandir e reduzir horizontalmente Conjuntos de Dimensionamento de Máquinas Virtuais com base nos limites de métrica de desempenho, em um agendamento recorrente ou até determinada data. Esta seção apresenta um exemplo de guia estratégico do Ansible que verifica a carga de trabalho nos últimos 10 minutos às 18h de toda segunda-feira, fuso horário do Pacífico, e expande o número de instâncias de VM em seus conjuntos de dimensionamento para quatro ou reduz horizontalmente para uma instância de acordo com as métricas de percentual da CPU. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Salvar este guia estratégico como *vmss-auto-scale-metrics.yml*. Para executar o Guia estratégico do Ansible, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Obter informações para configurações de dimensionamento automático existentes
É possível obter detalhes de qualquer configuração de dimensionamento automático por meio do módulo *azure_rm_autoscale_facts* com o guia estratégico da seguinte maneira:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Desabilitar as configurações de dimensionamento automático
É possível desabilitar a configuração de dimensionamento automático alterando `enabled: true` para `enabled: false` ou excluindo as configurações de dimensionamento automático com o guia estratégico da seguinte maneira:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Exemplo de guia estratégico do Ansible para conjuntos de dimensionamento de máquinas virtuais](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)