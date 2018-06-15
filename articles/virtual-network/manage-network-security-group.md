---
title: Criar, alterar ou excluir um Grupo de Segurança de Rede do Azure | Microsoft Docs
description: Saiba como criar, alterar ou excluir um Grupo de Segurança de Rede.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 22cf62f201b21f3035687b7f0f2ff07dc94f1a29
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658665"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Criar, alterar ou excluir um Grupo de Segurança de Rede

As regras de segurança em grupos de segurança de rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e fora das sub-redes da rede virtual e dos adaptadores de rede. Se você não estiver familiarizado com os grupos de segurança de rede, consulte a [Visão geral do Grupo de Segurança de Rede](security-overview.md) para saber mais sobre eles e conclua o tutorial [Filtrar o tráfego de rede](tutorial-filter-network-traffic.md) para ganhar experiência sobre grupos de segurança de rede.

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial requer o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.28 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta que você realizou o logon, ou conectou ao Azure, deve estar atribuída à função do [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída a ações adequadas listadas em [Permissões](#permissions).

## <a name="work-with-network-security-groups"></a>Trabalhar com grupos de segurança de rede

Você pode criar, [exibir todos](#view-all-network-security-groups), [exibir os detalhes](#view-details-of-a-network-security-group), [alterar](#change-a-network-security-group) e [excluir](#delete-a-network-security-group) um Grupo de Segurança de Rede. Você também pode [associar ou desassociar](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) um Grupo de Segurança de Rede de um adaptador de rede ou sub-rede.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Há um limite para o número de grupos de segurança de rede que podem ser criados por local e assinatura do Azure. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do Portal, selecione **+ Criar um recurso**.
2. Selecione **Redes** e selecione **Grupo de Segurança de Rede**.
3. Insira um **Nome** para o Grupo de Segurança de Rede, selecione sua **Assinatura**, crie um novo **Grupo de recursos** ou escolha um grupo de recursos existente, escolha um **Local** e, em seguida, selecione **Criar**.

**Comandos**

- CLI do Azure: [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Exibir todos os grupos de segurança de rede

Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção. Os grupos de segurança de rede que existem na sua assinatura são listados aqui.

**Comandos**

- CLI do Azure: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Exibir os detalhes de um Grupo de Segurança de Rede

1. Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
2. Selecione na lista o Grupo de Segurança de Rede cujos detalhes você deseja exibir. Em **CONFIGURAÇÕES**, é possível exibir as **Regras de segurança de entrada** e **Regras de segurança de saída**, os **Adaptadores de rede** e **Sub-redes** aos quais o Grupo de Segurança de Rede está associado. Você também pode habilitar ou desabilitar os **Logs de diagnóstico** e exibir as **Regras de segurança em vigor**. Para saber mais, consulte [Logs de diagnóstico](virtual-network-nsg-manage-log.md) e [Exibir as regras de segurança em vigor](diagnose-network-traffic-filter-problem.md).
3. Para saber mais sobre as configurações comuns do Azure listadas, veja as seguintes informações:
    *   [Log de atividade](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Marcas](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandos**

- CLI do Azure: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Criar um Grupo de Segurança de Rede

1. Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
2. Selecione o Grupo de Segurança de Rede que você deseja alterar. As alterações mais comuns são [adicionar](#create-a-security-rule) ou [remover](#delete-a-security-rule) as regras de segurança e [Associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede ou adaptador de rede](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Comandos**

- CLI do Azure: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede ou adaptador de rede

Para associar ou desassociar um Grupo de Segurança de Rede de um adaptador de rede, consulte [Associar ou desassociar um Grupo de Segurança de Rede de um adaptador de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Para associar ou desassociar um Grupo de Segurança de Rede de uma sub-rede, consulte [Alterar as configurações de sub-rede](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Excluir um Grupo de Segurança de Rede

Se um Grupo de Segurança de Rede estiver associado a sub-redes ou adaptadores de rede, ele não poderá ser excluído. [Desassocie](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) um Grupo de Segurança de Rede de todas as sub-redes e adaptadores de rede antes de tentar excluí-lo.

1. Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
2. Selecione o Grupo de Segurança de Rede que você deseja excluir da lista.
3. Selecione **Excluir** e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Trabalhar com regras de segurança

Um Grupo de Segurança de Rede contém zero ou mais regras de segurança. Você pode criar, [exibir todas](#view-all-security-rules), [exibir os detalhes](#view-details-of-a-security-rule), [alterar](#change-a-security-rule) e [excluir](#delete-a-security-rule) uma regra de segurança.

### <a name="create-a-security-rule"></a>Criar uma regra de segurança

Há um limite para o número de regras por Grupo de Segurança de Rede que podem ser criadas por local e assinatura do Azure. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
2. Selecione na lista o Grupo de Segurança de Rede ao qual você deseja adicionar uma regra de segurança.
3. Selecione **Regras de segurança de entrada** em **CONFIGURAÇÕES**. Várias regras existentes são listadas. Algumas delas podem não ter sido adicionadas por você. Quando um Grupo de Segurança de Rede é criado, várias regras de segurança padrão são criadas nele. Para saber mais, consulte [regras de segurança padrão](security-overview.md#default-security-rules).  Não é possível excluir as regras de segurança padrão, porém você pode substituí-las por regras com prioridade mais alta.
4. <a name = "security-rule-settings"></a>Selecione **+ Adicionar**.  Selecione ou adicione valores para as configurações a seguir e selecione **OK**:
    
    |Configuração  |Valor  |Detalhes  |
    |---------|---------|---------|
    |Fonte     | Selecione **Qualquer**, **Endereços IP** ou **Marcação de Serviço**.        | Se você selecionar **Endereços IP**, precisará especificar os **Endereços IP/intervalos de CIDR de origem**. Você pode especificar um único valor ou uma lista separada por vírgulas com vários valores. Um exemplo de vários valores é 10.0.0.0/16, 192.188.1.1. Há limites para o número de valores que podem ser especificados. Consulte [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes. Se você selecionar **Marcação de Serviço**, será preciso selecionar uma marcação de serviço. Uma marcação de serviço é um identificador predefinido para uma categoria de endereços IP. Para saber mais sobre marcações de serviço disponível e o que cada uma delas representa, consulte [Marcações de serviço](security-overview.md#service-tags)        |
    |Intervalos de portas de origem     | Especifique uma única porta, como 80, um intervalo de portas, como por exemplo, 1024-65535 ou uma lista separada por vírgulas de portas individuais e/ou intervalos de porta, como 80, 1024-65535. Insira um asterisco para permitir o tráfego em qualquer porta. | As portas e intervalos especificam qual tráfego de portas é permitido ou negado pela regra. Há limites para o número de portas que podem ser especificadas. Consulte [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.  |
    |Destino     | Selecione **Qualquer**, **Endereços IP** ou **Rede Virtual**.        | Se você selecionar **Endereços IP**, precisará especificar os **Endereços IP/intervalos de CIDR de destino**. Semelhante à **Origem** e aos **Endereços IP/intervalos de CIDR de origem**, você pode especificar um único endereço, vários endereços ou intervalos de endereços, e também há limites para o número de endereços que podem ser especificados. Selecionar **Rede Virtual**, que é uma marcação de serviço, significa que o tráfego é permitido em todos os endereços IP no espaço de endereços da rede virtual.        |
    |Intervalos de portas de destino     | Especifique um único valor ou uma lista separada por vírgulas com vários valores. | Semelhante aos **Intervalos de porta de origem**, você pode especificar uma única porta ou várias portas e intervalos de portas e também há limites para o número de portas que podem ser especificadas. |
    |Protocolo     | Selecione **Qualquer**, **TCP** ou **UDP**.        |         |
    |Ação     | Selecione **Permitir** ou **Negar**.        |         |
    |Prioridade     | Insira um valor entre 100-4096 que seja exclusivo para todas as regras de segurança dentro do Grupo de Segurança de Rede. |As regras são processadas em ordem de prioridade. Quanto menor o número, maior a prioridade. É recomendável deixar uma lacuna entre os números de prioridade ao criar regras, como 100, 200 e 300. Deixar essas lacunas torna mais fácil adicionar regras no futuro que podem precisar ter prioridade maior ou menor do que as regras existentes.         |
    |NOME     | Um nome exclusivo para a regra dentro do Grupo de Segurança de Rede.        |  O nome pode ter até 80 caracteres. Ele precisa começar com uma letra ou número, terminar com uma letra, número ou sublinhado e pode conter apenas letras, números, sublinhados, pontos ou hifens.       |
    |DESCRIÇÃO     | Uma descrição opcional.        |         |

    Não é possível especificar um [grupo de segurança de aplicativo](#work-with-application-security-groups) para as configurações de **Origem** ou de **Destino** usando o portal. Entretanto, é possível utilizar a CLI do Azure ou o PowerShell. As configurações para **Regras de segurança de saída** são semelhantes, portanto, elas não são abordadas separadamente.

**Comandos**

- CLI do Azure: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Exibir todas as regras de segurança

Um Grupo de Segurança de Rede contém zero ou várias regras. Para saber mais sobre as informações listadas ao exibir regras, consulte a [Visão geral de Grupo de Segurança de Rede](security-overview.md).

1. Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
2. Selecione na lista o Grupo de Segurança de Rede cujas regras você deseja exibir.
3. Selecione **Regras de segurança de entrada** ou **Regras de segurança de saída** em **CONFIGURAÇÕES**.

A lista contém as regras que você criou e as [regras de segurança padrão](security-overview.md#default-security-rules) do Grupo de Segurança de Rede.

**Comandos**

- CLI do Azure: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Exibir os detalhes de uma regra de segurança

1. Na caixa de pesquisa na parte superior do portal, digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
2. Selecione na lista o Grupo de Segurança de Rede cujos detalhes da regra de segurança você deseja exibir.
3. Selecione **Regras de segurança de entrada** ou **Regras de segurança de saída** em **CONFIGURAÇÕES**.
4. Selecione a regra cujos detalhes você deseja exibir. Para obter uma explicação detalhada de todas as configurações, consulte [configurações de regra de segurança](#security-rule-settings).

**Comandos**

- CLI do Azure: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Alterar uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Altere as configurações conforme desejado e, em seguida, selecione **Salvar**. Para obter uma explicação detalhada de todas as configurações, consulte [configurações de regra de segurança](#security-rule-settings).

**Comandos**

- CLI do Azure: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Excluir uma regra de segurança

1. Conclua as etapas em [Exibir detalhes de uma regra de segurança](#view-details-of-a-security-rule).
2. Selecione **Excluir** e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>Trabalhar com grupos de segurança de aplicativo

Um grupo de segurança de aplicativo contém zero ou mais adaptadores de rede. Para saber mais, consulte [grupos de segurança de aplicativo](security-overview.md#application-security-groups). Não é possível trabalhar com grupos de segurança de aplicativo no portal, mas você pode usar o PowerShell ou a CLI do Azure. Todos os adaptadores de rede em um grupo de segurança do aplicativo precisam existir na mesma rede virtual. O primeiro adaptador de rede adicionado a um grupo de segurança de aplicativo determina em qual rede virtual todos os adaptadores de rede posteriores precisam estar. Para saber como adicionar um adaptador de rede a um grupo de segurança de aplicativo, consulte [Adicionar um adaptador de rede a um grupo de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Criar um grupo de segurança de aplicativo

- CLI do Azure: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Exibir todos os grupos de segurança de aplicativo

- CLI do Azure: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Exibir detalhes de um grupo de segurança de aplicativo específico

- CLI do Azure: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Alterar um grupo de segurança de aplicativo

Embora seja possível alterar algumas configurações como marcas e permissões para um grupo de segurança de aplicativo existente, você não pode alterar seu nome ou local.

- CLI do Azure: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: nenhum cmdlet do PowerShell.

### <a name="delete-an-application-security-group"></a>Excluir um grupo de segurança de aplicativo

Não será possível excluir um grupo de segurança de aplicativo se ele tiver algum adaptador de rede associado. Remova todos os adaptadores de rede do grupo de segurança de aplicativo alterando as configurações de adaptador de rede ou excluir os adaptadores de rede. Para ver mais detalhes, consulte [Adicionar ou remover um adaptador de rede de grupos de segurança de aplicativo](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou [Excluir um adaptador de rede](virtual-network-network-interface.md#delete-a-network-interface).

**Comandos**

- CLI do Azure: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Permissões

Para executar tarefas em grupos de segurança de rede, regras de segurança e grupos de segurança de aplicativo, sua conta precisa estar atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as permissões apropriadas listadas na tabela a seguir:

### <a name="network-security-groups"></a>Grupos de segurança de rede

| Ação                                                        |   NOME                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/ruleTables/read                             |   Obter Grupo de Segurança de Rede                                          |
| Microsoft.Network/ruleTables/write                            |   Criar ou atualizar um Grupo de Segurança de Rede                             |
| Microsoft.Network/ruleTables/delete                           |   Excluir Grupo de Segurança de Rede                                       |
| Microsoft.Network/ruleTables/join/action                      |   Associar um grupo de segurança de rede a uma interface de sub-rede ou rede |
| Microsoft.Network/ruleTables/rules/read                       |   Obter regra                                                            |
| Microsoft.Network/ruleTables/rules/write                      |   Criar ou atualizar regra                                               |
| Microsoft.Network/ruleTables/rules/delete                     |   Excluir regra                                                         |
| Microsoft.Network/networkInterfaces/effectiveruleTable/action |   Grupo de Segurança de Rede Obter adaptador de rede em vigor              |
| Microsoft.Network/networkWatchers/nextHop/action              |   Obtém o próximo salto de uma VM                                         |

### <a name="application-security-groups"></a>Grupos de segurança do aplicativo

| Ação                                                                     | NOME                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Entrar uma configuração IP a um grupo de segurança do aplicativo|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Entrar em uma regra de segurança aos grupos de segurança de aplicativo    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obter um grupo de segurança de aplicativo                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Criar ou atualizar um grupo de segurança de aplicativo           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Excluir um grupo de segurança de aplicativo                     |

## <a name="next-steps"></a>Próximas etapas

- Criar uma rede ou grupo de segurança do aplicativo usando [PowerShell](powershell-samples.md) ou os scripts de exemplo da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais