---
title: "Endereços IP públicos do Azure | Microsoft Docs"
description: "Aprenda a criar, modificar e excluir endereços IP públicos."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5274ffe641768e0725623c2acf38d432a97eea9f
ms.lasthandoff: 04/03/2017


---

# <a name="public-ip-addresses"></a>Endereços IP públicos

Saiba mais sobre endereços IP públicos e como criá-los, alterá-los e excluí-los. Um endereço IP público é um recurso com suas próprias definições configuráveis. A atribuição de um endereço IP público a outros recursos do Azure permite:
- Conectividade de entrada com a Internet para recursos como VMs (Máquinas Virtuais) do Azure, Conjunto de Dimensionamento de Máquinas Virtuais do Azure, Gateway de VPN do Azure e Azure Load Balancers voltados para a Internet.
- Conectividade de saída com a Internet que não seja NAT (endereço de rede convertido). Por exemplo, uma VM pode fazer uma comunicação de saída com a Internet sem um endereço IP público atribuído a ela, mas seu endereço é um endereço de rede convertido pelo Azure. Para saber mais sobre conexões de saída usando recursos do Azure, leia o artigo [Entendendo as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Este artigo explica como trabalhar com endereços IP públicos. Este artigo aplica-se a recursos implantados por meio do modelo de implantação do Azure Resource Manager. Embora os endereços IP públicos possam ser atribuídos a recursos implantados pelo modelo de implantação clássico, os endereços são aplicados de modo diferente do que são por meio do Resource Manager. Leia o artigo sobre as [noções básicas dos modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) se você não estiver familiarizado com as diferenças entre os dois modelos.

As seções restantes deste artigo listam as etapas para concluir todas as tarefas relacionadas ao endereço IP público. Cada seção lista:
- As etapas para concluir a tarefa no portal do Azure. Para concluir as etapas, você deve estar conectado ao [portal do Azure](http://portal.azure.com). Inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free), caso ainda não tenha uma.
- Os comandos para concluir a tarefa usando o Azure PowerShell com os links para a referência do comando. Execute as etapas do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) para instalar e configurar o PowerShell. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Os comandos para concluir a tarefa usando a interface de linha de comando (CLI) do Azure, com links para a referência do comando. Para instalar a CLI do Azure, siga as etapas do artigo [Como instalar e configurar a CLI 2.0 do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obter ajuda com os comandos da CLI, digite `az <command> -h`.

Os endereços IP públicos têm um encargo nominal. Para ver os preços, acesse a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses). Há limites para o número de endereços IP públicos que você pode usar em uma assinatura. Para ver os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

Conclua as etapas nas seções a seguir para criar, alterar ou excluir recursos de endereço IP público:

## <a name="create"></a>Criar um endereço IP público

Para criar um endereço IP público, conclua as seguintes etapas:
1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que recebeu permissões (no mínimo) para a função de Colaborador de Rede para sua assinatura. Leia o artigo [Funções internas para o Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
3. Clique em **+ Adicionar** na folha **Endereço IP Público** que é exibida.
4. Insira ou selecione valores para as seguintes configurações na folha **Criar endereço IP público** que é exibida e clique em **Criar**:

    |**Configuração**|Obrigatório?|**Detalhes**|
    |---|---|---|
    |**Nome**|Sim|O nome deve ser exclusivo no grupo de recursos que você seleciona.|
    |**Atribuição de endereço IP**|Sim|**Dinâmico:** endereços dinâmicos são atribuídos somente depois que o endereço IP público é associado a uma NIC conectada a uma VM e a VM é iniciada pela primeira vez. Os endereços dinâmicos poderão ser alterados se a VM à qual a NIC está conectada for interrompida (desalocada). O endereço permanecerá o mesmo se a VM for reinicializada ou interrompida (mas não desalocada). **Estático:** endereços estáticos são atribuídos quando o endereço IP público é criado. Os endereços estáticos não são alterados, mesmo se a VM for colocada no estado interrompido (desalocado). O endereço é liberado apenas quando a NIC é excluída. Você pode alterar o método de atribuição depois que a NIC é criada.|
    |**Tempo limite de ociosidade (minutos)**|Não|Por quantos minutos manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep alive.|
    |**Rótulo do nome DNS**|Não|Deve ser exclusivo mo local do Azure em que você cria o nome (em todas as assinaturas e todos os clientes). O serviço DNS público do Azure registra automaticamente o nome e o endereço IP para que você possa se conectar a um recurso com o nome. O Azure acrescenta *location.cloudapp.azure.com* (em que location é o local que você seleciona) ao nome que você fornece para criar o nome DNS totalmente qualificado. |
    |**Assinatura**|Sim|Deve existir na mesma assinatura que o recurso ao qual você deseja associar o endereço IP público.|
    |**Grupo de recursos**|Sim|Pode existir no mesmo ou em outro grupo de recursos que o recurso ao qual você deseja associar o endereço IP público.|
    |**Localidade**|Sim|Deve existir no mesmo local que o recurso ao qual você deseja associar o endereço IP público.|

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change"></a>Alterar configurações ou excluir um endereço IP público

Para alterar ou excluir um endereço IP público, conclua as seguintes etapas:

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que recebeu permissões (no mínimo) para a função de Colaborador de Rede para sua assinatura. Leia o artigo [Funções internas para o Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
3. Na folha **Endereços IP públicos** que é exibida, clique no nome do endereço IP público do qual deseja alterar as configurações, ou excluir.
4. Na folha exibida para o endereço IP público, escolha uma das opções a seguir, dependendo da ação que deseja: excluir ou alterar o endereço IP público.
    - **Excluir:** para excluir o endereço IP público, clique em **Excluir** na seção **Visão Geral** da folha. Se o endereço estiver associado a uma configuração de IP, ele não poderá ser excluído. Se, no momento, o endereço estiver associado a uma configuração, clique em **Desassociar** para desassociar o endereço da configuração de IP.
    - **Alterar:** clique em **Configuração**. Altere as configurações usando as informações na etapa 4 da seção [Criar um endereço IP público](#create) deste artigo. Para alterar a atribuição de estático para dinâmico, primeiramente você deve desassociar o endereço IP público da configuração de IP à qual ele está associado. Você pode alterar o método de atribuição para dinâmico e clicar em **Associar** para associar o endereço IP à mesma configuração de IP, a uma configuração diferente ou deixá-lo desassociado. Para desassociar um endereço IP público, na seção **Visão Geral**, clique em **Desassociar**.

>[!WARNING]
>Ao alterar o método de atribuição de estático para dinâmico, você perde o endereço IP que foi atribuído ao endereço IP público. Embora os servidores DNS públicos do Azure mantenham um mapeamento entre endereços estáticos ou dinâmicos e qualquer rótulo de nome DNS (caso tenha definido um), um endereço IP dinâmico pode ser alterado quando a VM é iniciada depois de ter ficado no estado interrompida (desalocada). Para impedir que o endereço seja alterado, atribua um endereço IP estático.

|**Ferramenta**|**Comando**|
|---|---|
|**CLI**|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para atualizar; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) para excluir|
|**PowerShell**|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para atualizar; [Remove-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para excluir|

## <a name="next-steps"></a>Próximas etapas
Atribua endereços IP públicos ao criar os seguintes recursos do Azure:

- Máquinas virtuais [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexão site a site usando um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de Dimensionamento de Máquinas Virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
