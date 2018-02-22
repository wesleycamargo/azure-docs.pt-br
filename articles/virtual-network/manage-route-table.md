---
title: Criar, alterar, ou excluir uma tabela de rotas do Azure | Microsoft Docs
description: Aprenda a criar, alterar ou excluir uma tabela de rotas.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 7edc73f337a72c24fd24d94468ee590b75dfa7df
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou excluir uma tabela de rotas

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão do Azure, poderá criar uma tabela de rotas para fazer isso. Se você não está familiarizado com o roteamento do Azure, leia a [Visão geral do roteamento](virtual-networks-udr-overview.md) e conclua o tutorial [Rotear o tráfego de rede com uma tabela de roteamentos](create-user-defined-route-portal.md) antes de concluir as tarefas neste artigo.

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial requer o módulo do Azure PowerShell versão 5.2.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.26 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para o número de tabelas de rotas que você pode criar por assinatura e local do Azure. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do Portal, selecione **+ Novo**.
2. Selecione **Rede** e, em seguida, selecione **Tabela de rotas**.
3. Insira um **Nome** para a tabela de rotas, selecione sua **Assinatura**, crie um novo **Grupo de recursos** ou selecione um grupo de recursos existente, selecione um **Local** e, em seguida, selecione **Criar**. A opção **Desabilitar propagação de rotas BGP** evita que rotas locais sejam propagadas para uma rede virtual do Azure por meio de BGP. Se sua rede virtual não está conectada a um gateway de rede do Azure (ExpressRoute ou VPN), deixe a opção *Desabilitada*. 

**Comandos**

- CLI do Azure: [az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Exibir tabelas de rotas

Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção. As tabelas de rotas que existem na sua assinatura estão listadas.

**Comandos**

- CLI do Azure: [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Exibir detalhes de uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione na lista a tabela de rotas cujos detalhes você deseja exibir. Em **CONFIGURAÇÕES**, você pode exibir as **Rotas** na tabela de rotas e as **Sub-redes** às quais a tabela de rotas está associada.
3. Para saber mais sobre configurações comuns do Azure, veja as seguintes informações:
    *   [Log de atividade](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Marcas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandos**

- CLI do Azure: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione a tabela de rotas que você deseja alterar. As alterações mais comuns são a [adição](#create-a-route) ou [remoção](#delete-a-route) de rotas e a [associação](#associate-a-route-table-to-a-subnet) ou a [desassociação](#dissociate-a-route-table-from-a-subnet) de tabelas de rotas de sub-redes.

**Comandos**

- CLI do Azure: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. Uma tabela de rotas pode ser associada a zero ou várias sub-redes. Já que tabelas de rotas não são associadas a redes virtuais, você precisa associar uma tabela de rotas a cada sub-rede à qual você deseje associar a tabela de rotas. Todo o tráfego deixando a sub-rede é roteado com base nas rotas que você criou nas tabelas de rota, [as rotas padrão](virtual-networks-udr-overview.md#default) e as rotas propagadas de uma rede local, se a rede virtual está conectada a um gateway de rede virtual do Azure (ExpressRoute, ou então VPN caso o BGP esteja sendo usado com um gateway de VPN). Você só pode associar uma tabela de rotas a sub-redes em redes virtuais que existem no mesmo local e assinatura do Azure que a tabela de rotas.

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
2. Selecione a rede virtual na lista que contém a sub-rede à qual você deseja associar uma tabela de rotas.
3. Selecione **Sub-redes** em **CONFIGURAÇÕES**.
4. Selecione a sub-rede à qual você deseja associar a tabela de rotas.
5. Selecione **tabela de rotas**, selecione a tabela de rotas que você deseja associar à sub-rede e selecione **Salvar**.

**Comandos**

- CLI do Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar uma tabela de rotas de uma sub-rede

Quando você desassocia uma tabela de rota de uma sub-rede, o Azure roteia o tráfego com base em suas [rotas padrão](virtual-networks-udr-overview.md#default).

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
2. Selecione a rede virtual na lista que contém a sub-rede da qual você deseja desassociar uma tabela de rotas.
3. Selecione **Sub-redes** em **CONFIGURAÇÕES**.
4. Selecione a sub-rede da qual você deseja desassociar a tabela de rotas.
5. Selecione **Tabela de rotas**, selecione **Nenhum** e, em seguida, selecione **Salvar**.

**Comandos**

- CLI do Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

Se uma tabela de rotas está associada a alguma sub-rede, ela não pode ser excluída. [Desassocie](#dissociate-a-route-table-from-a-subnet) uma tabela de rota de todas as sub-redes antes de tentar excluí-la.

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione **...** no lado direito da tabela de rotas que você deseja excluir.
3. Selecione **Excluir** e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Criar uma rota

Há um limite para o número de rotas por tabela de rotas que você pode criar por assinatura e local do Azure. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione da lista a tabela de rotas à qual você deseja adicionar uma rota.
3. Selecione **Rotas**, em **CONFIGURAÇÕES**.
4. Selecione **+ Adicionar**.
5. Insira um único **Nome** para a rota dentro da tabela de rotas.
6. Insira o **Prefixo de endereço**, em notação CIDR, para o qual você deseja rotear o tráfego. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se você definir 10.0.0.0/16 como um prefixo em uma rota, ainda poderá definir outra rota com o prefixo de endereço 10.0.0.0/24. O Azure seleciona uma rota para o tráfego com base na correspondência de prefixo mais longa. Para saber mais sobre como o Azure seleciona rotas, veja [Visão geral de roteamento](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Selecione um **Tipo do próximo salto**. Para obter uma descrição detalhada de todos os tipos de próximo salto, veja [Visão geral de roteamento](virtual-networks-udr-overview.md).
8. Insira um endereço IP para **Endereço do próximo salto**. Só é possível inserir um endereço se você seleciona *Dispositivo virtual* para **Tipo do próximo salto**.
9. Selecione **OK**. 

**Comandos**

- CLI do Azure: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Exibir rotas

Uma tabela de rotas contém zero ou várias rotas. Para saber mais sobre as informações listadas ao exibir rotas, veja a [Visão geral de roteamento](virtual-networks-udr-overview.md).

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione da lista a tabela de rotas cujas rotas você deseja exibir.
3. Selecione **Rotas**, em **CONFIGURAÇÕES**.

**Comandos**

- CLI do Azure: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Exibir detalhes de uma rota

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione a tabela de rotas que contém uma rota cujos detalhes você deseja exibir.
3. Selecione **Rotas**.
4. Selecione a rota cujos detalhes você deseja exibir.

**Comandos**

- CLI do Azure: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Alterar uma rota

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione a tabela de rotas que contém uma rota que você deseja alterar.
3. Selecione **Rotas**.
4. Selecione a rota que você deseja alterar.
5. Altere as configurações existentes para suas novas configurações e selecione **Salvar**.

**Comandos**

- CLI do Azure: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Excluir uma rota

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando **Tabelas de rota** aparece nos resultados da pesquisa, selecione essa opção.
2. Selecione a tabela de rotas que contém uma rota que você deseja excluir.
3. Selecione **Rotas**.
4. Na lista de rotas, selecione **...** no lado direito da rota que você deseja excluir.
5. Selecione **Excluir** e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas efetivas para cada adaptador de rede anexado a uma máquina virtual são uma combinação de tabelas de rotas criadas por você, rotas padrão do Azure e todas as rotas propagadas de redes locais por meio do BGP através de um gateway de rede virtual do Azure. Entender as rotas em vigor para um adaptador de rede é útil ao solucionar problemas de roteamento. Você pode exibir as rotas em vigor para qualquer adaptador de rede anexado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, digite o nome de uma máquina virtual cujas rotas em vigor você deseja exibir. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando **Máquinas virtuais** aparecer nos resultados da pesquisa, selecione essa opção e selecione uma máquina virtual da lista.
2. Selecione **Rede** em **CONFIGURAÇÕES**.
3. Selecione o nome de um adaptador de rede.
4. Selecione **Rotas em vigor** em **SUPORTE + SOLUÇÃO DE PROBLEMAS**.
5. Examine a lista de rotas em vigor para determinar se existe a rota correta para o local ao qual você deseja rotear o tráfego. Saiba mais sobre os tipos de próximo salto que você vê nessa lista em [Visão geral do roteamento](virtual-networks-udr-overview.md).

**Comandos**

- CLI do Azure: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Validar o roteamento entre dois pontos de extremidade

Você pode determinar o tipo de próximo salto entre uma máquina virtual e o endereço IP de outro recurso do Azure, um recurso local ou um recurso na Internet. Determinar o roteamento do Azure é útil ao solucionar problemas de roteamento. Para concluir essa tarefa, você deve ter um Observador de Rede existente. Se você não tiver um Observador de Rede existente, crie um completando as etapas em [Criar uma instância do Observador de Rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Na caixa de pesquisa na parte superior do portal, digite *observador de rede* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
2. Selecione **Próximo salto** em **FERRAMENTAS DE DIAGNÓSTICO DE REDE**.
3. Selecione sua **Assinatura** e o **Grupo de recursos** da máquina virtual de origem da qual provém o roteamento que você deseja validar.
4. Selecione a **Máquina virtual** e o **Adaptador de rede** anexado à máquina virtual, bem como o **Endereço IP de origem** atribuído ao adaptador de rede do qual provém o roteamento que você deseja validar.
5. Insira o **endereço IP de destino** para o qual se destina o roteamento que você deseja validar.
6. Selecione **Próximo salto**.
7. Após uma breve espera, serão retornadas informações que dizem a você o tipo de próximo salto e a ID da rota utilizada no roteamento do tráfego. Saiba mais sobre os tipos de próximo salto que você vê retornado em [Visão geral do roteamento](virtual-networks-udr-overview.md).

**Comandos**

- CLI do Azure: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 
 
## <a name="permissions"></a>Permissões

Para executar tarefas em tabelas de rotas e em rotas, sua conta deve ser atribuída à função de [colaborador da rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as permissões apropriadas listadas na tabela a seguir:

|Operação                                                       |   Nome da operação                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/routeTables/read                              |   Obter tabela de rotas                              |
|Microsoft.Network/routeTables/write                             |   Obter ou atualizar tabela de rotas                 |
|Microsoft.Network/routeTables/delete                            |   Excluir tabela de rotas                           |
|Microsoft.Network/routeTables/join/action                       |   Ingressar tabela de rotas                             |
|Microsoft.Network/routeTables/routes/read                       |   Obter rota                                    |
|Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                       |
|Microsoft.Network/routeTables/routes/delete                     |   Excluir rota                                 |
|Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obter tabela de rotas em vigor para um adaptador de rede  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Obtém o próximo salto de uma VM                  |

A operação *Ingressar tabela de rotas* é necessária para associar uma tabela de rotas a uma sub-rede.