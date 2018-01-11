---
title: Abrir portas para uma VM usando o Portal do Azure | Microsoft Azure
description: "Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Resource Manager no Portal do Azure"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 08f0af6ecdb45b263d39c3d2d6442f4ed555e3c3
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Como abrir portas para uma máquina virtual com o Portal do Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Você também pode [executar essas etapas usando o Azure PowerShell](nsg-quickstart-powershell.md).

Primeiro, crie o seu Grupo de Segurança de Rede. Selecione um grupo de recursos no portal e escolha **Adicionar**. Em seguida, procure e selecione um **Grupo de segurança de rede**:

![Adicionar um Grupo de Segurança de Rede](./media/nsg-quickstart-portal/add-nsg.png)

Insira um nome para o seu Grupo de Segurança de Rede, selecione ou crie um grupo de recursos e selecione uma localização. Selecione **Criar** quando terminar:

![Criar um grupo de segurança de rede](./media/nsg-quickstart-portal/create-nsg.png)

Selecione o novo Grupo de Segurança de Rede. Selecione 'Regras de segurança de entrada' e escolha o botão **Adicionar** para criar uma regra:

![Adicionar uma regra de entrada](./media/nsg-quickstart-portal/add-inbound-rule.png)

Para criar uma regra que permita o tráfego:

- Selecione o botão **Básico**. Por padrão, a janela **Avançado** fornece algumas opções de configuração adicionais, como para definir um intervalo de porta ou bloco do IP de origem específico.
- Escolha um **Serviço** comum no menu suspenso, como *HTTP*. Você também pode selecionar *Personalizado* para fornecer uma porta específica a ser usada. 
- Se desejar, altere o nome ou a prioridade. A prioridade afeta a ordem na qual as regras são aplicadas - quanto menor o valor numérico, mais cedo a regra é aplicada.
- Quando você estiver pronto, selecione **OK** para criar a regra:

![Criar uma regra de entrada](./media/nsg-quickstart-portal/create-inbound-rule.png)

A etapa final é associar o Grupo de Segurança de Rede com uma sub-rede ou uma interface de rede específica. Vamos associar o Grupo de Segurança de Rede com uma sub-rede. Selecione **Sub-redes** e escolha **Associar**:

![Associar um Grupo de Segurança de Rede a uma sub-rede](./media/nsg-quickstart-portal/associate-subnet.png)

Selecione sua rede virtual e, então, selecione a sub-rede apropriada:

![Associar um Grupo de Segurança de Rede à rede virtual](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Você criou um Grupo de Segurança de Rede, uma regra de entrada que permite o tráfego na porta 80 e o associou a uma sub-rede. Todas as VMs que você conectar a essa sub-rede estarão acessíveis na porta 80.

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Para aplicativos Web altamente disponíveis, você deve colocar suas VMs atrás de um balanceador de carga do Azure. O balanceador de carga distribui o tráfego para VMs, com um Grupo de Segurança de rede que fornece filtragem. Para saber mais, veja [Como balancear a carga de máquinas virtuais Linux no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um NSG (grupo de segurança de rede)?](../../virtual-network/virtual-networks-nsg.md)