---
title: Criar, alterar ou excluir redes virtuais do Azure | Microsoft Docs
description: Saiba como criar, alterar ou excluir redes virtuais.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>Criar, alterar ou excluir redes virtuais

Saiba como criar e excluir VNets (redes virtuais) e alterar as configurações, como servidores DNS e espaços de endereço IP para VNets existentes. Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Para cada VNet, você pode:
- Escolha o espaço de endereço para atribuir. Um espaço de endereço consiste em um ou mais intervalos de endereços definidos usando a notação CIDR, como 10.0.0.0/16.
- Escolha usar o servidor DNS fornecido pelo Azure ou seu próprio servidor DNS para cada VNet. Todos os recursos conectados à VNet são atribuídos a esse servidor DNS para resolver nomes dentro da VNet.
- Segmente a VNet em sub-redes, cada uma com seu próprio intervalo de endereços, no espaço de endereços da VNet. Para saber como criar, alterar e excluir sub-redes, leia o artigo [Adicionar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md).

Esse artigo explica como criar, alterar e excluir VNets criadas por meio do modelo de implantação do Azure Resource Manager.
 
## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso você não esteja familiarizado com VNets, recomendamos que realize o exercício [Criação da sua primeira Rede Virtual do Azure](virtual-network-get-started-vnet-subnet.md) antes de ler este artigo. O exercício ajuda a entender melhor as VNets.
- Examine o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites de VNets.
- Faça logon no portal do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se você usar comandos do Azure PowerShell para concluir as tarefas neste artigo, primeiro [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos cmdlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se você usar comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, primeiro [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`.


## <a name="create-vnet"></a>Criar uma rede virtual

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. No portal do Azure, clique em **+Novo**. Na folha **Novo** que aparece, clique em **Rede**. Na folha **Rede** que aparece, clique em **Rede virtual.**
3. Na folha **Rede virtual** exibida, deixe *Resource Manager* selecionado na caixa **Selecionar um modelo de implantação** e clique em **Criar**.
4. Na folha **Criar rede virtual**, digite ou selecione valores para as seguintes configurações e clique em **Criar**:
    - **Nome**: o nome deve ser exclusivo no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) em que você optar por criar a VNet. O nome não pode ser alterado depois da criação da VNet. Você pode criar várias VNets ao longo do tempo. Leia o artigo [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obter sugestões de nomenclatura para facilitar o gerenciamento de diversas VNets.
    - **Espaço de endereço**: especifique na notação CIDR. O espaço de endereço definido pode ser público ou privado (RFC 1918). Independentemente de você definir um espaço de endereço público ou privado, o espaço de endereço fica acessível somente de dentro da VNet, de VNets interconectadas e quaisquer redes locais que você tenha conectado à VNet. Não é possível adicionar os seguintes espaços de endereço:
        - 224.0.0.0/4 (Multicast)
        - 255.255.255.255/32 (Broadcast)
        - 127.0.0.0/8 (Loopback)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (DNS interno)
    
      Embora você só possa definir um espaço de endereço ao criar a VNet, pode adicionar mais espaços de endereço após a criação da VNet. Para saber como fazer isso, conclua as etapas na seção [Adicionar ou remover espaços de endereço](#add-address-spaces) deste artigo.

      >[!WARNING]
      >Se as VNets tiverem espaços de endereço sobrepostos com outras VNets ou redes locais, elas não poderão ser conectadas juntas. Considere outras VNets ou redes locais às quais você possa querer conectar a VNet no futuro antes de definir um espaço de endereço.
      >
      >
    
    - **Nome da sub-rede:** o nome deve ser exclusivo dentro da VNet. O nome não pode ser alterado depois da criação da sub-rede. O portal requer que você defina uma sub-rede ao criar uma VNet, mesmo que uma VNet não precise ter nenhuma sub-rede. O portal só permite que você defina uma sub-rede ao criar a VNet, embora você possa adicionar mais sub-redes à VNet criada depois que a VNet é criada. Para adicionar uma sub-rede em uma VNet, leia a seção [Criar sub-rede](virtual-network-manage-subnet.md#create-subnet) do artigo [Criar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md). Você pode criar uma VNet com várias sub-redes usando a CLI ou o PowerShell.

      >[!TIP]
      >Diferentes sub-redes geralmente são criadas para filtrar ou controlar o roteamento de tráfego entre elas. Antes de definir sub-redes, considere como você pode desejar filtrar e rotear o tráfego entre as sub-redes. Para saber mais sobre a filtragem de tráfego entre sub-redes, leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md). O Azure faz o roteamento entre as sub-redes automaticamente, mas você pode substituir as rotas padrão do Azure. Para saber como fazer isso, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).
      >

    - **Intervalo de endereços de sub-rede:** deve estar dentro do **Espaço de endereço** inserido para a VNet. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, uma VNet com um intervalo de endereços de sub-rede de /29 tem apenas três endereços IP utilizáveis. Se você planeja algum dia conectar uma VNet a um Gateway de VPN, uma sub-rede de gateway deve ser criada. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é criada. Para saber como alterar um intervalo de endereços de sub-rede, leia a seção [Alterar sub-rede](#change-subnet) do artigo [Adicionar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md).
    - **Assinatura:** selecione uma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Uma VNet não pode abranger assinaturas, embora possa ser conectada a VNets em outras assinaturas usando um Gateway de VPN do Azure ou emparelhamento de VNet. Os recursos do Azure que você conecta à VNet devem existir na mesma assinatura.
    - **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existente ou crie um novo. Os recursos do Azure que você conecta à VNet podem existir em grupos de recursos iguais ou diferentes.
    - **Local:** selecione um [local](https://azure.microsoft.com/regions/) do Azure, também chamado de região. Uma VNet não pode abranger locais do Azure, embora uma VNet em um único local possa ser conectada a uma VNet em outro local usando um Gateway de VPN do Azure. Os recursos do Azure que você conecta à VNet devem existir no mesmo local.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Exibir configurações e redes virtuais

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **Redes virtuais**, clique nessa opção.
3. Na folha **Redes virtuais** que aparece, clique na VNet cujas configurações você deseja exibir.
4. As configurações a seguir são listadas na folha que aparece para a VNet selecionada:
    - **Visão geral:** apresenta informações sobre a VNet, como seu espaço de endereço e servidores DNS. A figura a seguir mostra as configurações de visão geral de uma VNet chamada **MyVNet**:
    
        ![Visão geral da interface de rede](./media/virtual-network-manage-network/vnet-overview.png)

      Você pode mover uma VNet para um grupo de recursos ou assinatura diferente nessa folha. Para saber como mover uma VNet, leia o artigo [Mover recursos para um outro grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure. Todos os recursos conectados à VNet também devem se mover com a VNet. 
    - **Espaço de endereço:** os espaços de endereço atribuídos à VNet são listados. Para saber como adicionar e remover espaços de endereço, conclua as etapas na seção [Adicionar ou remover espaços de endereço](#address-spaces) deste artigo.
    - **Dispositivos conectados:** quaisquer recursos conectados à VNet são exibidos. No exemplo mostrado na figura anterior, três interfaces de rede e um balanceador de carga são conectados à VNet. Quaisquer novos recursos criados e conectados à VNet são listados. Se você excluir um recurso conectado à VNet, ele não será mais exibido na lista.
    - **Sub-redes:** uma lista de sub-redes que existem na VNet. Para saber como adicionar e remover sub-redes, leia as seções [Adicionar sub-rede](virtual-network-manage-subnet.md#create-subnet) e [Excluir sub-rede](virtual-network-manage-subnet.md#delete-subnet) do artigo [Adicionar, alterar ou excluir sub-redes](virtual-network-manage-subnet.md).
    - **Servidores DNS:** você pode especificar se o servidor DNS interno do Azure ou o servidor DNS personalizado fornece a resolução de nome para dispositivos conectados à VNet. Ao criar uma VNet usando o portal do Azure, os servidores DNS do Azure são usados para resolução de nomes em uma VNet por padrão. Para modificar os servidores DNS, conclua as etapas na seção [Adicionar, alterar ou remover Servidores DNS](#dns-servers) deste artigo. 
    - **Emparelhamentos:** se houver emparelhamentos existentes na assinatura, eles serão listados aqui. Você pode exibir configurações para emparelhamentos existentes, ou criar, alterar ou excluir emparelhamentos. Para saber mais sobre emparelhamentos, leia o artigo [Visão geral de emparelhamento](virtual-network-peering-overview.md).
    - **Propriedades:** exibe configurações sobre a VNet, incluindo a ID do recurso da VNet e a assinatura em que ela existe.
    - **Diagrama:** o diagrama apresenta uma representação visual de todos os dispositivos conectados à VNet com algumas informações importantes sobre os dispositivos. Você pode clicar em qualquer um dos dispositivos para gerenciá-lo diretamente por essa exibição.
    - **Configurações comuns do Azure:** para saber mais sobre as configurações comuns do Azure, leia os artigos [Log de atividades](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Marcas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandos**

|**Ferramenta**|**Comando**|
|---|---|
|CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>Adicionar e remover espaços de endereço

Você pode adicionar espaços de endereço e remover espaços de endereço de uma VNet. Os espaços de endereço devem ser especificados na notação CIDR e não podem se sobrepor na mesma VNet. Os espaços de endereço definidos podem ser públicos ou privados (RFC 1918). Independentemente de você definir espaços de endereço públicos ou privados, os espaços de endereço em uma VNet ficam acessíveis somente de dentro da VNet, de VNets interconectadas e quaisquer redes locais que você tenha conectado à VNet. Não é possível adicionar os seguintes espaços de endereço:
    - 224.0.0.0/4 (Multicast)
    - 255.255.255.255/32 (Broadcast)
    - 127.0.0.0/8 (Loopback)
    - 169.254.0.0/16 (Link-local)
    - 168.63.129.16/32 (DNS interno)

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **Redes virtuais**, clique nessa opção.
3. Na folha **Redes virtuais** que aparece, clique na rede virtual à/da qual você queira adicionar/remover um espaço de endereço.
4. Na folha que aparece para a VNet que você selecionou, clique em **Espaço de endereço** na seção **CONFIGURAÇÕES**.
5. Conclua uma das seguintes opções na folha que aparece com espaços de endereço:
    - **Adicionar um espaço de endereço:** digite o novo espaço de endereço na caixa. O espaço de endereço não pode se sobrepor a um espaço de endereço existente definido para a VNet.
    - **Remover um espaço de endereço:** clique com o botão direito do mouse em um espaço de endereço, então clique em **Remover**. Se existir uma sub-rede no espaço de endereços, você não poderá remover o espaço de endereços. Para que você possa remover um espaço de endereço, todas as sub-redes que existem no espaço de endereço (e quaisquer recursos conectados às sub-redes) devem ser excluídas.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|Somente Resource Manager|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Adicionar, alterar ou remover servidores DNS

Todas as VMs conectadas à VNet registram-se com os servidores DNS especificados para a VNet e usam o servidor DNS para a resolução de nomes. Cada NIC (interface de rede) em uma VM pode ter suas próprias configurações do servidor DNS. Se uma NIC tiver suas próprias configurações de servidor DNS, elas substituirão as configurações do servidor DNS para a VNet. Para saber mais sobre as configurações de DNS da NIC, leia o artigo [Configurações e tarefas da interface de rede](virtual-network-network-interface.md#dns). Para saber mais sobre a resolução de nomes para VMs e instâncias de função de Serviços de Nuvem, leia o artigo [Resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **Redes virtuais**, clique nessa opção. 
3. Na folha **Redes virtuais** que aparece, clique na rede virtual à qual você deseja adicionar as configurações de DNS.
4. Na folha que aparece para a VNet selecionada, clique em **Servidores DNS** na seção **CONFIGURAÇÕES**.
5. Selecione uma das opções a seguir na folha que aparece com servidores DNS:
    - **Padrão (fornecido pelo Azure):** todos os nomes de recursos e endereços IP privados são registrados automaticamente para os servidores DNS do Azure. Você pode resolver nomes entre quaisquer recursos conectados à mesma VNet. Você não pode usar essa opção para resolver nomes entre VNets. Para resolver nomes entre VNets, você deve usar um servidor DNS personalizado.
    - **Personalizado:** você pode adicionar um ou mais servidores, até o limite do Azure para uma VNet. Para saber mais sobre os limites do servidor DNS, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Você tem as seguintes opções:
        - **Adicionar um endereço**: adiciona o servidor à sua lista de Servidores DNS da rede virtual e registra o servidor DNS no Azure. Se você tiver registrado anteriormente um servidor DNS com o Azure, poderá selecioná-lo da lista que aparece. 
        - **Remover um endereço:** clique no **X** ao lado do servidor que você deseja remover. Excluir o servidor remove-o apenas da lista dessa VNet. O servidor DNS permanece registrado no Azure para suas outras VNets usarem. 
        - **Reordenar endereços do servidor DNS**: é importante verificar se os servidores DNS estão listados na ordem correta para seu ambiente. As listas de servidores DNS não funcionam em round robin. Elas são usadas na ordem em que foram especificadas. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS, não importa se ele está funcionando corretamente ou não. Remova todos os servidores DNS listados e, em seguida, adicione-os de volta na ordem que desejar.
        - **Alterar um endereço**: destaque o servidor DNS na lista, então digite o novo nome.
6. Clique em **Salvar**.
7. Reinicie as VMs conectadas à VNet para que as novas configurações do servidor DNS sejam atribuídas a elas. As VMs continuam a usar suas configurações de DNS atuais até que elas sejam reiniciadas.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Excluir uma rede virtual

Você poderá excluir uma VNet somente se não houver recursos conectados a ela. Se houver recursos conectados a qualquer sub-rede na VNet, primeiro exclua os recursos conectados a todas as sub-redes na VNet. As instruções sobre como excluir um recurso variam de acordo com o recurso. Para saber como excluir recursos conectados a sub-redes, leia a documentação para cada tipo de recurso que você deseja excluir. Para excluir uma VNet, conclua as seguintes etapas:

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **redes virtuais**, clique nessa opção.
3. Na folha **Redes virtuais** exibida, clique na VNet que você deseja excluir.
4. Confirme se não há dispositivos conectados à VNet clicando em **Dispositivos conectados** na seção **CONFIGURAÇÕES** da folha exibida para a VNet selecionada. Se houver dispositivos conectados, primeiro exclua-os antes de excluir a VNet.  Se não houver dispositivos conectados, clique em **Visão geral** na folha.
5. Clique no ícone **Excluir** na parte superior da folha. 
6. Clique em **Sim** para confirmar a exclusão da VNet.


**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Próximas etapas

- Para criar uma VM e conectá-la a uma VNet, leia o artigo [Criar uma VNet e conectar VMs](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
- Para filtrar o tráfego de rede entre as sub-redes em uma VNet, leia o artigo [Criar grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
- Para emparelhar uma VNet com outra, leia o artigo [Criar um emparelhamento de rede virtual](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription).
- Para conhecer as opções para conectar uma VNet a uma rede local, leia o artigo [Sobre o gateway de rede](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams).

