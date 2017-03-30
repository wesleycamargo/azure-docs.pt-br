---
title: Adaptadores de rede do Azure | Microsoft Docs
description: "Saiba como criar e excluir adaptadores de rede (NIC) e atribuir endereços IP públicos e privados às NICs. Saiba como anexar e desanexar NICs de máquinas virtuais do Azure."
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c3e5ad8b7c8325049cb53d709673c7c7ad58108f
ms.lasthandoff: 03/22/2017


---

# <a name="network-interfaces"></a>Interfaces de rede

Saiba mais sobre adaptadores de rede (NIC) e como trabalhar com eles. Uma NIC é a interconexão entre uma máquina virtual (VM) do Azure e a rede do software subjacente. A figura a seguir ilustra a funcionalidade que uma NIC oferece:

![Interface de rede](./media/virtual-network-network-interface/nic.png)

Este artigo explica como trabalhar com os conceitos mostrados na figura. Clique em qualquer um dos conceitos a seguir para ir diretamente para essa seção do artigo.

- [Adaptadores de rede](#nics): uma NIC está conectada a uma sub-rede em uma rede virtual do Azure (VNet). Na figura, a **VM1** tem duas NICs anexadas, enquanto a **VM2** tem uma NIC anexada. Cada NIC está conectada com a mesma VNet, mas as sub-redes são diferentes. Esta seção fornece etapas para listar as NICs existentes e criar, alterar e excluir NICs. 
- [Configurações de IP](#ip-configs): cada NIC está associada a uma ou mais configurações de IP. Cada configuração de IP está atribuída a um endereço IP privado. Uma configuração de IP pode estar associada a um endereço IP público. Na figura, **NIC1** e **NIC3** estão associadas a uma configuração de IP, enquanto a **NIC2** está associada a duas configurações de IP. A configuração de IP atribuída NIC1 e NIC3 ter endereços IP públicos atribuídos, ao passo que nenhuma configuração de IP atribuída à NIC2 está associada a um endereço IP público. Esta seção fornece etapas para criar, alterar e excluir as configurações de IP com endereços IP privados atribuídos usando os métodos de atribuição estática e dinâmica. Esta seção também fornece etapas para associar e desassociar os endereços IP públicos em uma configuração de IP. 
- [Grupos de segurança de rede](#nsgs): os grupos de segurança de rede (NSG) contêm uma ou mais regras de segurança de entrada ou saída. As regras controlam o tipo de tráfego de rede que pode fluir de e para um adaptador de rede, uma sub-rede ou ambos. Na figura, **NIC1** e **NIC3** estão associadas a um NSG, já a **NIC2** não está associada. Esta seção apresenta etapas para exibir os NSGs aplicados a uma NIC, adicionar um NSG a uma NIC e remover um NSG de uma NIC.
- [Máquinas virtuais](#vms): dependendo do tamanho, uma VM pode ter várias NICs anexadas a ela, com no mínimo uma NIC. Para saber quantas NICs cada tamanho da VM suporta, confira o artigo tamanhos de VMs do [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables) ou [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables). Nesta seção, você aprenderá a criar VMs com uma ou mais NICs, e como anexar e desanexar NICs em VMs existentes.

Se você não estiver familiarizado com NICs e VMs no Azure, recomendamos que você conclua o exercício [Criação da sua primeira Rede Virtual do Azure](virtual-network-get-started-vnet-subnet.md) antes de ler este artigo. O exercício ajuda a entender melhor as VNets e VMs. 

Este artigo se aplica a NICs e VMs criadas por meio do modelo de implantação do Azure Resource Manager. A Microsoft recomenda criar recursos com o modelo de implantação do Resource Manager, em vez de usar o modelo de implantação clássico. Leia o artigo sobre as [Noções básicas dos modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) se você não estiver familiarizado com as diferenças entre os dois modelos.

As seções restantes deste artigo fornecem etapas para concluir todas as tarefas relacionadas à NIC. Cada seção lista:
- As etapas para concluir a tarefa no portal do Azure. Para concluir as etapas, você deve estar conectado ao [portal do Azure](http://portal.azure.com). Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free), caso ainda não tenha uma.
- Os comandos para concluir a tarefa usando o Azure PowerShell com os links para a referência do comando. Execute as etapas do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) para instalar e configurar o PowerShell. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Os comandos para concluir a tarefa usando a interface de linha de comando (CLI) do Azure, com links para a referência do comando. Para instalar a CLI do Azure, siga as etapas do artigo [Como instalar e configurar a CLI 2.0 do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obter ajuda sobre os comandos da CLI, digite `az <command> -h`.

## <a name="nics"></a>Adaptadores de rede
Conclua as etapas nas seções a seguir para criar, exibir, alterar e excluir configurações e adaptadores de rede:

### <a name="create-nic"></a>Como criar um adaptador de rede

Uma NIC pode ser anexada a uma VM ou existir por conta própria. Para saber como anexar uma NIC a uma VM, leia a seção [Como anexar uma NIC a uma máquina virtual](#vm-attach-nic) deste artigo.

Para criar uma NIC, siga as seguintes etapas:

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **adaptadores de rede** que aparece, clique em **+ Adicionar**.
4. Na folha em que aparece o texto **Criar adaptador de rede**, digite ou selecione valores para as seguintes configurações e clique em **Criar**:
    
    |**Configuração**|**Obrigatório?**|**Detalhes**|
    |---|---|---|
    |**Nome**|Sim|O nome não pode ser alterado depois que a NIC é criada. O nome deve ser exclusivo no grupo de recursos selecionado. Leia o artigo [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obter sugestões de nomes.|
    |**Rede virtual**|Sim|Uma NIC só pode ser conectada a uma VNet que exista na mesma assinatura e local que ela. A VM, na qual a NIC está anexada, também deve existir no mesmo local e assinatura que a NIC. Se não houver nenhuma VNet listada, você deve criar uma. Para criar uma VNet, conclua as etapas descritas no artigo [Rede virtual](virtual-networks-create-vnet-arm-pportal.md). Depois de criar uma NIC, você não pode alterar a VNet na qual ela está conectada.|
    |**Sub-rede**|Sim|Selecione uma sub-rede na VNet selecionada. Depois de criar a NIC, você pode alterar a sub-rede na qual ela está conectada.|
    |**Atribuição de endereço IP privado**|Sim| Depois de criado, um endereço IP privado é atribuído pelo servidor DHCP do Azure à NIC. O servidor DHCP atribui um endereço disponível do intervalo de endereços da sub-rede definido para a sub-rede que você conectou à NIC. **Dinâmico:** o Azure pode atribuir um endereço diferente a uma NIC quando a VM na qual está atribuída é iniciada depois de ter seu estado modificado para parado (desalocada). O endereço permanece o mesmo se a VM é reiniciada sem ter seu estado modificado para parado (desalocada). **Estático:** os endereços estáticos só mudam quando você realiza alguma alteração ou quando a NIC é excluída. Você pode alterar o método de atribuição depois de criar a NIC.|
    |**Grupo de segurança de rede**|Não|Os grupos de segurança de rede permitem controlar o fluxo de entrada e saída de tráfego de rede de uma NIC. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs. Você pode aplicar um ou nenhum grupo de segurança de rede (NSG) em uma NIC. Você também pode escolher entre aplicar um ou nenhum NSG em uma sub-rede na qual a NIC está conectada. Às vezes você receberá resultados inesperados ao aplicar um NSG uma NIC na qual a sub-rede está conectada. Leia o artigo [Solução de problemas de NSGs](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface) para aprender a solucionar problemas de NSGs aplicados às NICs.|
    |**Assinatura**|Sim| A VM anexada à NIC e a VNet com a qual foi conectada devem existir na mesma assinatura.|
    |**Grupo de recursos**|Sim| A NIC pode existir no grupo de recursos iguais ou diferentes que a VM na qual foi anexada ou a VNet na qual foi conectada.|
    |**Localidade**|Sim|A VM anexada à NIC e a VNet com a qual foi conectada devem existir no mesmo local.|

O portal do Azure cria uma configuração de IP primária nomeada **ipconfig1** com um endereço IP privado dinâmico e o associa à NIC criada. Para saber mais sobre as configurações de IP, leia a seção [Configurações de IP](#ip-configs) deste artigo. Ao criar uma NIC, você não pode especificar o nome da configuração de IP criada pelo portal, atribuir um endereço IP privado estático nem atribuir um endereço IP público. Se você criar a NIC usando o PowerShell ou a CLI, você pode especificar o nome da configuração de IP, um endereço IP estático e atribuir um endereço IP público. Depois de criar a NIC, você pode alterar o método de atribuição de endereço IP privado e o endereço IP público associado à NIC. Para alterar as configurações depois de criar uma NIC, siga as etapas da seção [Como alterar uma configuração de IP](#change-ip-config) deste artigo. 

>[!Note]
> O Azure só atribui um endereço MAC à NIC depois que a NIC está anexada a uma VM iniciada pela primeira vez. Não é possível especificar o endereço MAC que o Azure atribui à NIC. O endereço MAC permanece atribuído à NIC até que a mesma seja excluída ou o endereço IP privado atribuído à configuração de IP primária da NIC primária seja alterado. Para saber mais sobre as configurações de IP, leia a seção [Configurações de IP](#ip-configs) deste artigo.

|**Ferramenta**|**Comando**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>Exibição e alteração de adaptadores de rede e configurações

Para exibir e alterar as configurações e adaptadores de rede, siga as seguintes etapas:

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Adaptadores de rede** que aparece, clique na NIC que você deseja exibir ou alterar as configurações.
4. As configurações a seguir são listadas na folha que aparece para a NIC selecionada:
    - **Visão geral:** fornece informações sobre a NIC, como endereços IP atribuídos a ela, a VNet/sub-rede na qual a NIC está conectada e a VM na qual a NIC está anexada (se realmente estiver anexada). A figura a seguir mostra as configurações de visão geral de uma NIC nomeada **mywebserver256**:   ![Visão geral de adaptador de rede](./media/virtual-network-network-interface/nic-overview.png)
    - **Configurações de IP:** uma NIC tem pelo menos uma configuração de IP atribuída a ela, mas pode ter várias. Para saber mais sobre o número máximo de configurações de IP que uma NIC suporta, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Cada configuração de IP tem um endereço IP privado atribuído e também pode ter um endereço IP público atribuído a ela. Para modificar o que é exibido, siga as etapas nas seções [Como adicionar uma configuração de IP secundária a uma NIC](#create-ip-config), [Como alterar uma configuração de IP](#change-ip-config) ou [Como excluir uma configuração de IP](#delete-ip-config) deste artigo.
    - **Servidores DNS:** pelos servidores DHCP do Azure, você pode especificar em qual servidor DNS quer atribuir uma NIC. Escolha entre o servidor DNS interno do Azure ou um servidor DNS personalizado. Para modificar o que é exibido, siga as etapas na seção [Como alterar as configurações de DNS para uma NIC](#dns) deste artigo.
    - **Grupo de segurança de rede (NSG):** exibe se há um NSG associado à NIC. Se houver um NSG associado à NIC, o nome do NSG associado será exibido. Para modificar o que é exibido, siga as etapas na seção [Como associar ou desassociar um NSG de um adaptador de rede](#associate-nsg) deste artigo.
    - **Propriedades:** exibe as configurações principais sobre a NIC para incluir o endereço MAC e a assinatura na qual ela existe. Você pode mover uma NIC a um outro grupo de recursos ou assinatura, desde que também mova todos os recursos relacionados à NIC. Por exemplo, se a NIC estiver anexada a uma VM, você também deve mover a VM e os recursos adicionais relacionados a ela. Para mover uma NIC, leia o artigo [Como mover um recurso para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). O artigo lista os pré-requisitos e ensina a mover recursos usando o portal do Azure, o PowerShell e a CLI do Azure.
    - **Regras de segurança efetiva:** as regras de segurança são listadas se a NIC está anexada a uma VM em execução e há um NSG associado à NIC na qual a sub-rede, ou ambos, está conectada. Para saber mais sobre o que é exibido, leia o artigo [Solução de problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface). Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs.
    - **Rotas efetivas:** as rotas são listadas se a NIC está anexada a uma VM em execução. As rotas são uma combinação das rotas padrão do Azure, das rotas definidas pelo usuário e das rotas BGP que possam existir para a sub-rede com a qual a NIC está conectada. Para saber mais sobre o que é exibido, leia o artigo [Solução de problemas de rotas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).
    - **Configurações comuns do Azure Resource Manager:** para saber mais sobre as configurações comuns do Azure Resource Manager, leia os artigos [Log de atividades](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Controle de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Rótulos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueios](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Script de automação](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para exibir NICs na assinatura; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para exibir as configurações de uma NIC|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) para exibir as NICs nas configurações de assinatura ou de exibição de uma NIC|

### <a name="dns"></a>Como alterar as configurações de DNS para uma NIC

Para alterar as configurações de DNS para uma NIC, siga as seguintes etapas. O servidor DNS é atribuído à VM pelo servidor DHCP do Azure. Para saber mais sobre as configurações de resolução de nome de uma NIC, leia o artigo [Resolução de nomes para VMs](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Para alterar as configurações de uma NIC, conclua as etapas 1 a 3 na seção [Exibição e alteração de adaptadores de rede e configurações](#view-nics) deste artigo.
2. Na folha da NIC selecionada, clique em **Servidores DNS**.
3. Clique em:
    - **Herdar de rede virtual (padrão)**: escolha essa opção para herdar a configuração do servidor DNS definida para a máquina virtual com a qual a NIC está conectada. No nível da VNet, é definido um servidor DNS personalizado ou um servidor DNS fornecido pelo Azure. O servidor DNS fornecido pelo Azure pode resolver nomes para recursos conectados à mesma VNet, mas não para recursos conectados a VNets diferentes.
    - **Personalizado**: você pode configurar seu próprio servidor DNS para resolver nomes entre diversas VNets. Digite o endereço IP do servidor que você deseja usar como servidor DNS. O endereço do servidor DNS especificado é atribuído somente a esta NIC e substitui qualquer configuração de DNS para a VNet com a qual a NIC está conectada.
4. Clique em **Salvar**.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>Como alterar o encaminhamento de IP para uma NIC

O encaminhamento de IP permite que a VM na qual a NIC está anexada:
- Receba o tráfego de rede não destinado a um dos endereços IP atribuídos a qualquer uma das configurações de IP atribuídas à NIC.
- Envie o tráfego de rede com um endereço IP de origem diferente daquele atribuído a uma de suas configurações de IP.

A configuração deve ser habilitada para cada NIC anexada à VM que recebe o tráfego que a VM deve encaminhar. Uma VM pode encaminhar tráfego se houver uma ou mais NICs anexadas a ela. Embora o encaminhamento de IP seja uma configuração do Azure, a VM também deve executar um aplicativo capaz de encaminhar o tráfego, como, por exemplo, firewall, otimização de WAN e aplicativos de balanceamento de carga. Quando uma VM executa aplicativos de rede, a VM é conhecida como um dispositivo de rede virtual (NVA). Você pode exibir uma lista de NVAs prontas para implantar no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). O encaminhamento de IP normalmente é usado com rotas definidas pelo usuário. Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md). 

Para alterar as configurações de encaminhamento de IP de uma NIC, siga as seguintes etapas:

1. Para a NIC que deseja modificar, conclua as etapas 1 a 3 na seção [Exibição e alteração de adaptadores de rede e configurações](#view-nics) deste artigo.
2. Na folha da NIC selecionada, clique em configurações de IP
3. Clique em **habilitado** ou **desabilitado** (configuração padrão) para alterar a configuração.
4. Clique em **Salvar**.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>Como alterar a sub-rede com a qual a NIC está conectada

Você pode alterar a sub-rede, mas não a VNet com a qual a NIC está conectada. Para alterar a sub-rede, todas as configurações de IP associadas à NIC devem ser atribuídas aos endereços IP privados dinâmicos. Para alterar uma a sub-rede com qual a NIC está conectada, siga as seguintes etapas:

1. Para a NIC que deseja conectar a uma sub-rede diferente, conclua as etapas 1 a 3 na seção [Exibição e alteração de adaptadores de rede e configurações](#view-nics) deste artigo.
2. Para a NIC selecionada, clique na folha **Configurações de IP**. Se os endereços IP privados para qualquer uma das configurações de IP listadas têm um endereço IP privado atribuído com o método estático, você deve alterar o método para dinâmico executando as etapas a seguir. Se os endereços estão atribuídos com o método dinâmico, vá para a etapa três:
    - Clique no endereço IP estático para a configuração de IP que você deseja alterar na lista de configurações de IP.
    - Na folha que aparece para configuração de IP, clique em **Dinâmico** para o método de **atribuição**.
    - Clique em **Salvar**.
3. Na lista suspensa da **sub-rede**, selecione a sub-rede que você deseja conectar com a NIC.
4. Clique em **Salvar**. Novos endereços dinâmicos são atribuídos do intervalo de endereços da sub-rede. Se você preferir, você pode atribuir endereços IP estáticos do novo intervalo de endereços da sub-rede.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>Como excluir um adaptador de rede

Você pode excluir uma NIC desde que não esteja anexada a uma VM. Se ela estiver anexada a uma VM, você deve desanexá-la antes de excluir. Para desanexar uma NIC de uma VM, siga as etapas na seção [Como desanexar uma NIC de uma máquina virtual](#vm-detach-nic) deste artigo.

1. Para a NIC que deseja excluir, siga as etapas 1 e 2 da seção [Exibição e alteração de adaptadores de rede e configurações](#view-nics) deste artigo.
2. Clique o botão direito do mouse na NIC que deseja excluir e clique em **Excluir**.
3. Clique em **Sim** para confirmar a exclusão da NIC.

Quando você exclui uma NIC, os endereços MAC ou IP atribuídos a ela são liberados.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>Configurações de IP
Cada NIC tem pelo menos uma configuração de IP, conhecida como configuração **Primária**. Uma NIC também pode ter uma ou mais configurações de IP*secundárias* associadas a ela. Há limites para o número de endereços IP que você pode atribuir a uma NIC. Leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes. Cada configuração de IP:
- Tem um endereço IP privado atribuído a ela pelo método de atribuição estático ou dinâmico. Os endereços IP dinâmicos podem ser alterados se uma VM for iniciada depois de ter seu estado modificado para pausado (desalocada). Endereços IP estáticos só são liberados de uma NIC quando a mesma é excluída.
- Pode ter um recurso de endereço IP público associado a ele.

Os servidores DHCP do Azure atribuem o endereço IP privado para a configuração de IP primária da NIC para a NIC dentro sistema operacional da VM.

Atribuir vários endereços IP a uma NIC é útil em cenários como:
- Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
- Uma VM usada como dispositivo de rede virtual, como um firewall ou balanceador de carga.
- A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Há um número limitado de endereços IP públicos que podem ser usados dentro de uma assinatura e um número limitado de endereços IP privados que podem ser atribuídos a uma NIC. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="create-ip-config"></a>Como adicionar uma configuração de IP secundária a uma NIC

Você pode adicionar quantas configurações de IP forem necessárias a uma NIC, desde que respeite os limites listados no artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Para adicionar uma configuração de IP a uma NIC, siga as seguintes etapas:
 
1. Se quiser adicionar uma configuração de IP em uma NIC, siga as etapas 1 a 3 na seção[Como exibir as configurações de adaptador de rede](#view-nics) deste artigo.
2. Para a NIC selecionada, clique na folha **Configurações de IP**.
3. Clique em **+ Adicionar** na folha de configurações de IP que é exibida.
4. Especifique o seguinte e clique em **OK** para fechar a folha **Adicionar configuração de IP**:

    |**Configuração**|**Obrigatório?**|**Detalhes**|
    |---|---|---|
    |**Nome**|Sim|Deve ser exclusivo para a NIC|
    |**Tipo**|Sim|Como você está adicionando uma configuração de IP a uma NIC existente, e cada NIC deve ter uma configuração de IP primária, a sua única opção é **secundária**.|
    |**Método de atribuição de endereço IP privado**|Sim|Os endereços **dinâmicos** podem ser alterados se a VM for reiniciada depois de ter seu estado modificado para pausado (desalocada). Os endereços **estáticos** não serão liberados até que a NIC seja excluída. Especifique um endereço IP do intervalo de espaço de endereço da sub-rede que não esteja em uso por outra configuração de IP.|
    |Endereço IP público|Não|**Desabilitado:** no momento, nenhum recurso de endereço IP público está associado à configuração de IP. **Habilitado:** selecione um endereço IP público existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md#create).|
5. Adicione manualmente endereços IP privados secundários ao sistema operacional da VM seguindo as instruções do artigo [Como atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Não adicione endereços IP públicos ao sistema operacional da VM.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>Como alterar uma configuração de IP

Siga as seguintes etapas para alterar as configurações de endereços IP públicos e privados de qualquer configuração de IP primária ou secundária:

1. Conclua as etapas 1 a 3 na seção [Exibir as configurações de adaptador de rede](#view-nics) deste artigo para a NIC que você deseja modificar.
2. Para a NIC selecionada, clique na folha **Configurações de IP**.
3. Clique na configuração de IP que você deseja modificar na lista da folha que será aberta para as configurações de IP.
4. Altere as configurações, conforme desejado, usando as informações sobre as configurações na seção [Como adicionar uma configuração de IP](#create-ip-config) deste artigo. Em seguida, clique em **Salvar** para fechar a folha da configuração IP que você selecionou.

>[!NOTE]
>Se a NIC primária tiver várias configurações de IP e você alterar o endereço IP privado da configuração de IP primária, reatribua manualmente todos os endereços IP secundários para a NIC no Windows (não é necessário para Linux). Para atribuir manualmente endereços IP a uma NIC em um sistema operacional, leia o artigo [Como atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Não adicione endereços IP públicos ao sistema operacional da VM.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>Exclusão de uma configuração de IP secundário de uma NIC

Siga as seguintes etapas para excluir uma configuração de IP secundário de uma NIC:

1. Conclua as etapas 1 a 3 na seção [Exibir as configurações de adaptador de rede](#view-nics) deste artigo para a NIC que você deseja modificar.
2. Para a NIC selecionada, clique na folha **Configurações de IP**.
3. Clique o botão direito do mouse na configuração de IP secundário que você deseja excluir e clique em **Excluir**. Se a configuração tem um recurso de endereço IP público associado a ela, o recurso é dissociado da configuração de IP, mas o recurso não é excluído.
4. Feche a folha das **configurações de IP**.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>Grupos de segurança de rede
Um grupo de segurança de rede (NSG) contém uma lista de regras de entrada e saída que permitem ou negam o tráfego de rede para uma NIC. Uma NIC, e a sub-rede que a NIC está conectada, não precisam ter um NSG associado. No entanto, um NSG pode ser associado a uma NIC, à sub-rede que a NIC está conectada ou ambos. Os NSGs com regras aplicáveis a todas as NICs conectadas à sub-rede são geralmente associados a sub-redes. Então, um NSG com as regras mais granulares pode ser aplicado a NICs individuais. Para saber mais sobre grupos de segurança de rede, leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md).

### <a name="associate-nsg"></a>Associação ou desassociação de um NSG de um adaptador de rede

Para associar ou desassociar um NSG a uma NIC, siga as seguintes etapas:

1. Siga as etapas de 1 a 3 na seção [Exibição e alteração de adaptadores de rede e configurações](#view-nics) deste artigo para a NIC que você deseja associar ou desassociar um NSG.
2. Na folha da NIC que você selecionou, clique no **Grupo de segurança de rede**. Uma folha é exibida com **Editar** na parte superior. Se nenhum NSG está associado atualmente à NIC, *nenhum* **Grupo de segurança de rede** é exibido. Se um NSG está associado atualmente a uma NIC, o **Grupo de segurança de rede** *NSG-Nome* (onde NSG-Nome é o nome do NSG atualmente associado à NIC) é exibido.
3. Clique em **Editar**.
4. Clique em **Grupo de Segurança de Rede**. Se nenhum grupo de segurança de rede estiver listado, é porque nenhum existe na sua assinatura. Para criar um NSG, conclua as etapas no artigo [Grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md). 
5. Na folha **Escolher grupo de segurança de rede** que aparece, clique em um NSG existente na lista para associar esse NSG à NIC ou clique em **Nenhum** para desassociar um NSG associado atualmente a uma NIC.
6. Clique em **Salvar**.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)]|

## <a name="vms"></a>Como anexar e desanexar NICs de uma máquina virtual

Você pode anexar um NIC existente a uma VM ao criá-la ou você pode anexar um NIC existente a uma VM existente. Você também pode desanexar uma NIC de uma VM existente que tenha pelo menos duas NICs. Embora o portal crie uma NIC quando você cria uma VM, ele não permite que você:

- Especifique um NIC existente para anexar ao criar a VM
- Crie uma VM com diversos NICs anexados
- Especifique um nome para a NIC (o portal cria a NIC com um nome padrão)
- Especifique que o método de atribuição de endereço IP privado seja estático. O portal atribui automaticamente um endereço IP privado dinâmico, embora seja possível alterar o método de atribuição após o portal criar a NIC.

Você pode usar o PowerShell ou a CLI para criar uma VM ou uma NIC com todos os atributos anteriores, não permitidos pelo portal. Antes de concluir as tarefas nas seções a seguir, considere as seguintes restrições e comportamentos:

- O tamanho da VM deve dar suporte a várias NICs. Para saber quantas NICs cada tamanho da VM suporta, confira o artigo tamanhos de VMs do [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables) ou [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json#size-tables).
- Por padrão, a primeira NIC conectada a uma VM é definida como o NIC *primária*. Todas as outras NICs anexadas à VM estão NICs *secundárias*.
- Por padrão, todo o tráfego de saída da VM é enviado pelo endereço IP atribuído à configuração de IP primária da NIC primária. Logicamente que você pode controlar qual endereço IP é usado para tráfego de saída no sistema operacional da VM.
- No passado, todas as VMs dentro do mesmo conjunto de disponibilidade deviam ter uma única ou diversas NICs. VMs com qualquer número de NICs agora podem existir no mesmo conjunto de disponibilidade. Uma VM só pode ser adicionada a um conjunto de disponibilidade quando ela é criada. Para saber mais sobre os conjuntos de disponibilidade, leia o artigo [Gerenciamento da disponibilidade das máquinas virtuais do Windows no Azure](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Enquanto as NICs anexadas à mesma VM podem ser conectadas a diferentes sub-redes em uma VNet, todas as NICs devem estar conectadas à mesma VNet.
- Você pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer NIC primário ou secundário para um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end.

### <a name="vm-create"></a>Como anexar uma ou mais NICs ao criar uma máquina virtual

Não é possível anexar NICs existentes a uma VM nova nem criar uma VM com várias NICs usando o portal do Azure. Você pode usar os seguintes comandos de CLI do Azure ou do PowerShell para anexar uma ou mais NICs existentes ao criar uma VM:

- **CLI:** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell:** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a> Visualização das NICs anexadas a uma máquina virtual

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de Proprietário, Colaborador ou Colaborador de rede para sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *máquinas virtuais*. Quando **máquinas virtuais** aparecerem nos resultados da pesquisa, clique nelas.
3. Na folha que aparece **máquinas virtuais**, clique no nome da VM para exibir os adaptadores de rede com os quais ela está conectada.
4. Na folha **máquina virtual** que aparece a VM que você selecionou, clique em **adaptadores de rede**.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>Anexar uma NIC a uma máquina virtual existente

A VM que deseja anexar a uma NIC deve dar suporte a várias NICs e estar com o estado pausado (desalocada). Você não pode usar o portal do Azure para anexar NICs a uma VM existente. Você pode usar os comandos da CLI do Azure ou do PowerShell para anexar NICs em VMs:

- **CLI:** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell:** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>Como desanexar uma NIC de uma máquina virtual existente

A VM que você deseja desanexar de uma NIC deve estar com o estado pausado (desalocada) e deve ter ao menos duas NICs anexadas. Você pode desanexar qualquer NIC, mas sempre deve manter ao menos uma NIC anexada à VM. Se você desanexar uma NIC primária, o Azure atribui o atributo primário à NIC anexada restante que está anexada à VM há mais tempo. Você também pode designar qualquer NIC como primária por conta própria. Não é possível desanexar NICs de uma VM, nem definir o atributo principal para uma NIC usando o portal do Azure, no entanto, você pode realizar as duas operações usando a CLI ou o PowerShell. Você pode usar os comandos da CLI do Azure ou do PowerShell para desanexar NICs em VMs:

- **CLI:** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell:** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>Próximas etapas
Leia os artigos a seguir para criar uma VM com várias NICs ou configurações de IP usando scripts:

|**Tarefa**|**Ferramenta**|
|---|---|
|**Criar uma VM com diversos NICs**|[CLI](virtual-network-deploy-multinic-arm-cli.md) e [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**Como criar uma única VM de NIC com vários endereços IP atribuídos a ela**|[CLI](virtual-network-multiple-ip-addresses-cli.md) e [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

