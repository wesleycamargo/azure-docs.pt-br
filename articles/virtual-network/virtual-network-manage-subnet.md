---
title: Adicionar, alterar ou excluir uma sub-rede de rede virtual do Azure | Microsoft Docs
description: Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: f8b60a27e760ae74c7f068844fad1ae0d4324366
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou excluir uma sub-rede da rede virtual

Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual. Se você não estiver familiarizado com as redes virtuais, antes de adicionar, alterar ou excluir uma sub-rede recomendamos que você leia [Visão geral da rede virtual do Azure](virtual-networks-overview.md) e [Criar, alterar ou excluir uma rede virtual](virtual-network-manage-network.md). Todos os recursos do Azure implantados em uma rede virtual são implantados em uma sub-rede dentro de uma rede virtual.
 
## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial requer o módulo do Azure PowerShell versão 5.2.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.26 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual à qual deseja adicionar uma sub-rede.
3. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
4. Selecione **+Sub-rede**.
5. Insira os valores dos seguintes parâmetros:
    - **Nome**: o nome deve ser exclusivo na rede virtual.
    - **Intervalo de endereços**: o intervalo deve ser exclusivo dentro do espaço de endereços para a rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede dentro da rede virtual. O espaço de endereçamento deve ser especificado utilizando a notação CIDR (Roteamento Entre Domínios sem Classes). Por exemplo, em uma rede virtual com espaço de endereçamento 10.0.0.0/16, você pode definir um espaço de endereçamento de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, definir uma sub-rede com um intervalo de endereços /29 resulta em três endereços IP utilizáveis na sub-rede. Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é adicionada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Alterar as configurações de sub-rede](#change-subnet-settings).
    - **Grupo de segurança de rede**: é possível associar zero ou um grupo de segurança de rede existente à sub-rede para filtrar o tráfego de rede de entrada e saída para a sub-rede. O grupo de segurança da rede deve existir na mesma assinatura e local da rede virtual. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [como criar um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabela de rotas:** você pode associar zero ou uma tabela de rotas existente à sub-rede a controlar o roteamento de tráfego de rede para outras redes. A tabela de rotas deve existir na mesma assinatura e localização da rede virtual. Saiba mais sobre o [Roteamento do Azure](virtual-networks-udr-overview.md) e [como criar uma tabela de rotas](create-user-defined-route-portal.md)
    - **Pontos de extremidade de serviço:** uma sub-rede pode ter zero ou vários pontos de extremidade do serviço habilitados. Para habilitar um ponto de extremidade de serviço para um serviço, selecione o serviço ou serviços para os quais deseja habilitar os pontos de extremidade de serviço na lista **Serviços**. Para remover um ponto de extremidade de serviço, desmarque o serviço para o qual deseja remover o ponto de extremidade de serviço. Para saber mais sobre os pontos de extremidade do serviço, consulte [Visão geral dos pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md). Depois de habilitar um ponto de extremidade de serviço para um serviço, você também precisa habilitar o acesso à rede para a sub-rede para um recurso criado com o serviço. Por exemplo, se habilitar o ponto de extremidade de serviço para *Microsoft.Storage*, você também deverá habilitar o acesso à rede para todas as contas de Armazenamento do Azure a que deseja conceder acesso à rede. Para obter detalhes sobre como habilitar o acesso à rede para sub-redes para as quais um ponto de extremidade de serviço está habilitado, consulte a documentação do serviço individual para o qual você habilitou o ponto de extremidade de serviço.
6. Para adicionar a sub-rede à rede virtual que você selecionou, selecione **OK**.

**Comandos**

- CLI do Azure: [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alterar as configurações de sub-rede

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede para a qual deseja alterar as configurações.
3. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
4. Na lista de sub-redes, selecione a sub-rede para a qual deseja alterar as configurações. É possível adicionar ou remover as seguintes configurações:

    - **Intervalo de endereços:** se nenhum recurso for implantado dentro da sub-rede, você poderá alterar o intervalo de endereços. Se houver recursos na sub-rede, você deverá mover os recursos para outra sub-rede ou excluí-los da sub-rede primeiro. As etapas necessárias para mover ou excluir um recurso variam de acordo com o recurso. Para saber como mover ou excluir os recursos que estão nas sub-redes, leia a documentação para cada tipo de recurso que deseja mover ou excluir. Consulte as restrições de **Intervalo de endereços** na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet).
    - **Usuários**: é possível controlar o acesso à sub-rede utilizando funções internas ou suas próprias funções personalizadas. Para saber mais sobre como atribuir funções e usuários para acessar a sub-rede, consulte [Utilizar atribuição de função para gerenciar o acesso aos recursos do Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - Para obter informações sobre como alterar o **Grupo de segurança de rede**, **Tabela de rotas**, **Usuários** e **Pontos de extremidade de serviço**, consulte a etapa 5 em [Adicionar uma sub-rede](#add-a-subnet).
5. Selecione **Salvar**.

**Comandos**

- CLI do Azure: [az network vnet subnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Excluir uma sub-rede

Uma sub-rede somente pode ser excluída se não houver recursos na sub-rede. Se houver recursos na sub-rede, será necessário excluir os recursos na sub-rede antes de excluí-la. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir os recursos que estão nas sub-redes, leia a documentação para cada tipo de recurso que deseja excluir.

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede que você deseja excluir.
3. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
4. Na lista de sub-redes, selecione **...**, à direita, para a sub-rede que deseja excluir
5. Selecione **Excluir** e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em sub-redes, sua conta deve ser atribuída à função de [colaborador da rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as permissões apropriadas listadas na tabela a seguir:

|Operação                                                                |   Nome da operação                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Obter Sub-rede da Rede Virtual                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou Atualizar Sub-rede da Rede Virtual      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Excluir Sub-rede da Rede Virtual                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Associar Rede Virtual                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Associar Serviço a uma Sub-rede                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obter Máquinas Virtuais da Sub-rede de Rede Virtual  |
