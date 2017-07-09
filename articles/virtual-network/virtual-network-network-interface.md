---
title: Criar, alterar ou excluir interfaces de rede do Azure | Microsoft Docs
description: "Aprenda o que são NICs (interfaces de rede) e como criá-las, alterar suas configurações e excluí-las."
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 871d469ada9857af6d6fe70f1dd38385fb18e312
ms.contentlocale: pt-br
ms.lasthandoff: 06/02/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>Criar, alterar ou excluir as interfaces de rede

Saiba como criar, alterar as configurações e excluir NICs (interfaces de rede). Uma NIC permite que uma VM (máquina virtual) do Azure comunique-se com a Internet, com o Azure e com recursos locais. Ao criar uma VM usando o portal do Azure, o portal criará uma NIC com as configurações padrão para você. Em vez disso, você pode escolher criar NICs com configurações personalizadas e adicionar uma ou mais VMs ao você criá-las. Também convém alterar as configurações padrão da NIC para as NICs existentes. Este artigo explica como criar NICs com configurações personalizadas, alterar as configurações existentes da NIC, como atribuição de filtro de rede (grupos de segurança de rede), atribuição de sub-rede, configurações do servidor DNS e encaminhamento de IP, e excluir NICs.

Se você precisa adicionar, alterar ou remover endereços IP para uma NIC, leia o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Se você precisa adicionar ou remover NICs de VMs, leia o artigo [Adicionar ou remover NICs](virtual-network-network-interface-vm.md).


## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Examine o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites de NICs.
- Faça logon no portal do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando comandos do PowerShell para concluir tarefas neste artigo, instale e configure o Azure PowerShell executando as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, instale e configure a CLI do Azure executando as etapas no artigo [Como instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`.

## <a name="create-nic"></a>Criar uma NIC
Ao criar uma VM usando o portal do Azure, o portal criará uma NIC com as configurações padrão para você. Se você preferir especificar todas as configurações da NIC, poderá criar uma NIC com configurações personalizadas e anexá-la a uma VM ao criar uma VM. Você também pode criar uma NIC e adicioná-la a uma VM existente. Para saber como criar uma VM com uma NIC existente ou adicionar ou remover NICs de VMs existentes, leia o artigo [Adicionar ou remover NICs](virtual-network-network-interface-vm.md). Antes de criar uma NIC, você deve ter uma VNet (rede virtual) existente no mesmo local e assinatura em que você criar uma NIC. Para saber como criar uma VNet, leia o artigo [Criar uma VNet](virtual-networks-create-vnet-arm-pportal.md).

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **adaptadores de rede** que aparece, clique em **+ Adicionar**.
4. Na folha **Criar interface de rede** que é exibida, digite ou selecione valores para as seguintes configurações e clique em **Criar**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |Nome|Sim|O nome deve ser exclusivo no grupo de recursos selecionado. Ao longo do tempo, você provavelmente terá várias NICs em sua assinatura do Azure. Leia o artigo [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obter sugestões ao criar uma convenção de nomenclatura para facilitar o gerenciamento de várias NICs. O nome não pode ser alterado depois que a NIC é criada.|
    |Rede virtual|Sim|Selecione uma VNet à qual conectar a NIC. Uma NIC só pode ser conectada a uma VNet que exista na mesma assinatura e local que ela. Depois de criar uma NIC, você não pode alterar a VNet na qual ela está conectada. A VM adicionada à NIC também deve existir no mesmo local e assinatura que a NIC.|
    |Sub-rede|Sim|Selecione uma sub-rede na VNet selecionada. Depois de criar a NIC, você pode alterar a sub-rede na qual ela está conectada.|
    |Atribuição de endereço IP privado|Sim| Escolha entre os seguintes métodos de atribuição: **Dinâmico:** ao selecionar essa opção, o Azure atribui automaticamente um endereço disponível do espaço de endereços da sub-rede selecionada. O Azure pode atribuir um endereço diferente a uma NIC quando a VM na qual ela está é iniciada depois de ter seu estado modificado para parado (desalocado). O endereço permanece o mesmo se a VM é reiniciada sem ter seu estado modificado para parado (desalocada). **Estático:** ao selecionar essa opção, você deve atribuir manualmente um endereço IP disponível de dentro do espaço de endereço da sub-rede selecionada. Endereços estáticos não mudam até que você os altere ou a NIC seja excluída. Você pode alterar o método de atribuição depois de criar a NIC. O servidor DHCP do Azure atribui esse endereço à NIC no sistema operacional da VM.|
    |Grupo de segurança de rede|Não| Deixe definido como **Nenhum**, selecione um NSG (grupo de segurança de rede) existente ou crie um NSG. NSGs permitem que filtrar o tráfego de rede que entra e sai de uma NIC. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs. Para criar um NSG, ler o artigo [Criar um NSG](virtual-networks-create-nsg-arm-pportal.md). Você pode aplicar zero ou um NSG a uma NIC. Você também pode escolher entre aplicar um ou nenhum NSG em uma sub-rede na qual a NIC está conectada. Aplicar um NSG a uma NIC e à sub-rede à qual a NIC está conectada às vezes gera resultados inesperados. Leia o artigo [Solução de problemas de NSGs](virtual-network-nsg-troubleshoot-portal.md#a-namensgaview-effective-security-rules-for-a-network-security-group-nsg) para solucionar problemas de NSGs aplicados a NICs e sub-redes.|
    |Assinatura|Sim|Selecione uma das suas [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) do Azure. A VM anexada à NIC e a VNet com a qual foi conectada devem existir na mesma assinatura.|
    |Grupo de recursos|Sim|Selecione um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) existente ou crie um. Uma NIC pode existir no mesmo grupo ou em um grupo de recursos diferente da VM à qual ela foi anexada ou a VNet à qual você a conectou.|
    |Local|Sim|A VM anexada à NIC e a VNet que você conectou a ela devem existir no mesmo [local](https://azure.microsoft.com/regions), também chamado de região.|

O portal não oferece a opção de atribuir um endereço IP público à NIC quando você a cria, embora atribua um endereço IP público a uma NIC quando você cria uma VM usando o portal. Para saber como adicionar um endereço IP público à NIC após criá-la, leia o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Se você quiser criar uma NIC com um endereço IP público, use a CLI ou o PowerShell para criar a NIC.

>[!Note]
> O Azure só atribui um endereço MAC à NIC depois que a NIC está anexada a uma VM iniciada pela primeira vez. Não é possível especificar o endereço MAC que o Azure atribui à NIC. O endereço MAC permanece atribuído à NIC até que a mesma seja excluída ou o endereço IP privado atribuído à configuração de IP primária da NIC primária seja alterado. Para saber mais sobre endereços IP e configurações de IP, consulte o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>Exibir configurações de NIC

Você pode exibir e alterar a maioria das configurações de uma NIC.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique na NIC que você deseja exibir ou alterar as configurações.
4. As configurações a seguir são listadas na folha que aparece para a NIC selecionada:
    - **Visão geral:** apresenta informações sobre a NIC, como os endereços IP atribuídos a ela, a VNet/sub-rede à qual a NIC está conectada e a VM à qual a NIC está anexada (se estiver anexada a uma). A imagem a seguir mostra as configurações de visão geral de uma NIC denominada **mywebserver256**:   ![visão geral da interface de rede](./media/virtual-network-network-interface/nic-overview.png) Você pode mover uma NIC para um grupo de recursos ou assinatura diferente clicando em (**alterar**) ao lado de **Grupo de recursos** ou **Nome da assinatura**. Se você mover a NIC, mova todos os recursos relacionados à NIC. Se a NIC estiver anexada a uma VM, por exemplo, você também deverá mover a VM e outros recursos relacionados à VM. Para mover uma NIC, leia o artigo [Como mover um recurso para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure.
    - **Configurações de IP:** endereços IP públicos e privados são atribuídos a uma ou mais configurações IP para uma NIC. Para saber mais sobre o número máximo de configurações IP que uma NIC aceita, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Cada configuração de IP tem um endereço IP privado atribuído e também pode ter um endereço IP público atribuído a ela. Para adicionar, alterar ou excluir as configurações de IP da NIC, conclua as etapas na seção [Adicionar uma configuração de IP secundária a uma NIC](virtual-network-network-interface-addresses.md#create-ip-config), [Alterar uma configuração de IP](virtual-network-network-interface-addresses.md#change-ip-config) ou [Excluir uma configuração de IP](virtual-network-network-interface-addresses.md#delete-ip-config) do artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Encaminhamento de IP e a atribuição de sub-rede também são configurados nesta seção. Para saber mais sobre essas configurações, consulte a seção [Habilitar/desabilitar encaminhamento de IP](#ip-forwarding) ou [Alterar atribuição de sub-rede](#subnet) deste artigo.
    - **Servidores DNS:** pelos servidores DHCP do Azure, você pode especificar em qual servidor DNS quer atribuir uma NIC. A NIC pode herdar a configuração da VNet à qual a NIC está conectada ou ter uma configuração personalizada que substitua a configuração para a VNet à qual ela está conectada. Para modificar o que é exibido, siga as etapas na seção [Alterar servidores DNS](#dns) deste artigo.
    - **NSG (grupo de segurança de rede):** exibe que NSG está associado à NIC (se houver algum). Um NSG contém regras de entrada e saída para filtrar o tráfego de rede para a NIC. Se houver um NSG associado à NIC, o nome do NSG associado será exibido. Para modificar o que é exibido, siga as etapas na seção [Como associar ou desassociar um NSG de um adaptador de rede](#associate-nsg) deste artigo.
    - **Propriedades:** exibe principais configurações sobre a NIC, incluindo seu endereço MAC (em branco, se a placa de rede não estiver anexada a uma VM) e a assinatura em que ela existe.
    - **Regras de segurança efetiva:** as regras de segurança são listadas se a NIC está anexada a uma VM em execução e há um NSG associado à NIC na qual a sub-rede, ou ambos, está conectada. Para saber mais sobre o que é exibido, leia o artigo [Solução de problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md#a-namensgaview-effective-security-rules-for-a-network-security-group-nsg). Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs.
    - **Rotas efetivas:** as rotas são listadas se a NIC está anexada a uma VM em execução. As rotas são uma combinação das rotas padrão do Azure, quaisquer UDRs (rotas definidas pelo usuário) e quaisquer rotas BGP que possam existir para a sub-rede à qual a NIC está conectada. Para saber mais sobre o que é exibido, leia o artigo [Solução de problemas de rotas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Para saber mais sobre o padrão do Azure e UDRs, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).
    - **Configurações comuns do Azure Resource Manager:** para saber mais sobre as configurações comuns do Azure Resource Manager, leia os artigos [Log de atividades](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Rótulos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para exibir NICs na assinatura; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para exibir as configurações de uma NIC|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) para exibir as NICs nas configurações de assinatura ou de exibição de uma NIC|

## <a name="dns"></a>Alterar os servidores DNS

O servidor DNS é atribuído pelo servidor DHCP do Azure para a NIC no sistema operacional da VM. O servidor DNS atribuído qualquer que seja a configuração de servidor DNS para uma NIC. Para saber mais sobre as configurações de resolução de nome de uma NIC, leia o artigo [Resolução de nomes para VMs](virtual-networks-name-resolution-for-vms-and-role-instances.md). A NIC pode herdar as configurações de VNet ou usar suas próprias configurações exclusivas que substituem a configuração para a VNet.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique na NIC que você deseja exibir ou alterar as configurações.
4. Na folha para a NIC selecionada, clique em **Servidores DNS** em **CONFIGURAÇÕES**.
5. Clique em:
    - **Herdar de rede virtual (padrão)**: escolha essa opção para herdar a configuração do servidor DNS definida para a máquina virtual com a qual a NIC está conectada. No nível da VNet, é definido um servidor DNS personalizado ou um servidor DNS fornecido pelo Azure. O servidor DNS fornecido pelo Azure pode resolver nomes de host para recursos conectados à mesma VNet. FQDN deve ser usado para resolver recursos conectados a VNets diferentes.
    - **Personalizado**: você pode configurar seu próprio servidor DNS para resolver nomes entre diversas VNets. Digite o endereço IP do servidor que você deseja usar como servidor DNS. O endereço do servidor DNS especificado é atribuído somente a esta NIC e substitui qualquer configuração de DNS para a VNet com a qual a NIC está conectada.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>Habilitar e desabilitar encaminhamento de IP

O encaminhamento de IP permite que a VM na qual a NIC está anexada:
- Receba o tráfego de rede não destinado a um dos endereços IP atribuídos a qualquer uma das configurações de IP atribuídas à NIC.
- Envie o tráfego de rede com um endereço IP de origem diferente daquele atribuído a uma das configurações de IP da NIC.

A configuração deve ser habilitada para cada NIC anexada à VM que recebe o tráfego que a VM deve encaminhar. Uma VM pode encaminhar tráfego se houver uma ou mais NICs anexadas a ela. Embora o encaminhamento de IP seja uma configuração do Azure, a VM também deve executar um aplicativo capaz de encaminhar o tráfego, como, por exemplo, firewall, otimização de WAN e aplicativos de balanceamento de carga. Quando uma VM executa aplicativos de rede, a VM é conhecida como um dispositivo de rede virtual (NVA). Você pode exibir uma lista de NVAs prontas para implantar no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). O encaminhamento de IP normalmente é usado com rotas definidas pelo usuário. Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Interfaces de rede** que aparece, clique na NIC para a qual você deseja habilitar ou desabilitar o encaminhamento de IP.
4. Na folha da NIC selecionada, clique em **Configurações de IP** na seção **CONFIGURAÇÕES**.
5. Clique em **habilitado** ou **desabilitado** (configuração padrão) para alterar a configuração.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>Alterar atribuição de sub-rede

Você pode alterar a sub-rede, mas não a VNet com a qual a NIC está conectada.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique na NIC que você deseja exibir ou alterar as configurações.
4. Clique em **Configurações de IP** em **CONFIGURAÇÕES** na folha da NIC que você selecionou. Se algum endereço IP privado para qualquer uma das configurações de IP listadas tiver **(Estático)** ao lado, altere o método de atribuição de endereço IP para dinâmico executando as etapas a seguir. Todos os endereços IP privados devem ser atribuídos com o método de atribuição dinâmica a alterar a atribuição de sub-rede para a NIC. Se os endereços forem atribuídos com o método dinâmico, siga para a etapa cinco. Se algum endereço for atribuído com o método de atribuição estático, conclua as seguintes etapas para alterar o método de atribuição para dinâmico:
    - Clique na configuração de IP para a qual você deseja alterar o método de atribuição de endereço IP na lista de configurações de IP.
    - Na folha que aparece para configuração de IP, clique em **Dinâmico** para o método de **atribuição**.
    - Clique em **Salvar**.
5. Na lista suspensa da **sub-rede**, selecione a sub-rede que você deseja conectar com a NIC.
6. Clique em **Salvar**. Novos endereços dinâmicos são atribuídos do intervalo de endereços da sub-rede para a nova sub-rede. Depois de atribuir a NIC para uma nova sub-rede, você poderá atribuir um endereço IP estático do novo intervalo de endereços de sub-rede, se preferir. Para saber mais sobre como adicionar, alterar ou remover endereços IP para uma NIC, leia o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>Excluir uma NIC

Você pode excluir uma NIC desde que não esteja anexada a uma VM. Se ele estiver anexado a uma VM, primeiro coloque a VM no estado parado (desalocado) e então desanexe a NIC da VM antes de excluir a NIC. Para desanexar uma NIC de uma VM, conclua as etapas na seção [Desanexar uma NIC de uma máquina virtual](virtual-network-network-interface-vm.md#vm-remove-nic) do artigo [Adicionar ou remover interfaces de rede](virtual-network-network-interface-vm.md). A exclusão de uma VM desanexa todas as NICs anexadas a ela, mas não exclui as NICs.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Clique o botão direito do mouse na NIC que deseja excluir e clique em **Excluir**.
4. Clique em **Sim** para confirmar a exclusão da NIC.

Quando você exclui uma NIC, os endereços MAC ou IP atribuídos a ela são liberados.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Próximas etapas
Para criar uma VM com várias NICs ou endereços IP, leia os seguintes artigos:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma única VM da NIC com vários endereços IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

