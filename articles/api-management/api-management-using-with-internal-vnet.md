---
title: Como usar o Gerenciamento de API do Azure com redes virtuais internas | Microsoft Docs
description: Saiba como instalar e configurar o Gerenciamento de API do Azure na rede virtual interna
services: api-management
documentationcenter: 
author: vladvino
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: cf062cfcbbb2454adf20a06c31c81a60f6f5719f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Usar o serviço de Gerenciamento de API do Azure com rede virtual interna
Com as VNETs (Redes Virtuais) do Azure, o Gerenciamento de API pode gerenciar as APIs que não estão acessíveis pela Internet. Várias tecnologias de VPN estão disponíveis para fazer a conexão. O Gerenciamento de API pode ser implantado em dois modos principais dentro de uma rede virtual:
* Externo
* Interna


Quando o Gerenciamento de API é implantado em um modo de rede virtual interna, todos os pontos de extremidade de serviço (gateway, portal do Desenvolvedor, portal do Azure, gerenciamento direto e Git) ficam visíveis apenas de dentro de uma rede virtual cujo acesso você controla. Nenhum dos pontos de extremidade de serviço é registrado no servidor DNS público.

Usando o Gerenciamento de API no modo interno você pode chegar aos seguintes cenários:
* Torne as APIs hospedadas em seu datacenter privado seguras e acessíveis por terceiros externamente usando as conexões VPN Site a Site ou ExpressRoute do Azure.
* Habilite cenários de nuvem híbrida expondo as APIs baseadas em nuvem e as APIs locais por meio de um gateway comum.
* Gerencie suas APIs hospedadas em várias localizações geográficas usando um único ponto de extremidade de gateway. 


## <a name="prerequisites"></a>pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ **Uma assinatura ativa do Azure**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Uma instância de Gerenciamento de API do Azure**. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Criar um Gerenciamento de API em uma rede virtual interna
O serviço de Gerenciamento de API em uma rede virtual interna é hospedado atrás de um ILB (balanceador de carga interno).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Habilite uma conexão de rede virtual usando o portal do Azure

1. Navegue até sua instância de Gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com/).
2. Selecione **Rede virtual**.
3. Configure a instância de Gerenciamento de API a ser implantada dentro da rede virtual.

    ![Menu para configurar um Gerenciamento de API do Azure em uma rede virtual interna][api-management-using-internal-vnet-menu]

4. Selecione **Salvar**.

Após a implantação bem-sucedida, você deve ver o endereço IP virtual interno do seu serviço no painel.

![Painel de Gerenciamento de API com uma rede virtual interna configurada][api-management-internal-vnet-dashboard]

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Habilitar uma conexão de rede virtual usando cmdlets do PowerShell
Você também pode habilitar a conectividade de rede virtual usando cmdlets do PowerShell.

* Criar um serviço de Gerenciamento de API dentro de uma rede virtual: use o cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para criar um serviço de Gerenciamento de API do Azure dentro de uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

* Implantar um serviço de Gerenciamento de API existente dentro de uma rede virtual: use o cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) para mover um serviço de Gerenciamento de API existente para uma rede virtual e configurá-lo para usar o tipo de rede virtual interna.

## <a name="apim-dns-configuration"></a>Configuração de DNS
Quando o Gerenciamento de API está no modo de rede virtual externa, o DNS é gerenciado pelo Azure. Para o modo de rede virtual interna, você precisa gerenciar o seu próprio roteamento.

> [!NOTE]
> O serviço de Gerenciamento de API não escuta as solicitações que vêm de endereços IP. Ele só responde às solicitações para o nome de host configurado em seus pontos de extremidade de serviço. Esses pontos de extremidade incluem o gateway, o portal do Desenvolvedor, o portal do Azure, o ponto de extremidade de gerenciamento direto e o Git.

### <a name="access-on-default-host-names"></a>Acesso em nomes de host padrão
Ao criar um serviço de Gerenciamento de API, chamado "contoso", por exemplo, os seguintes pontos de extremidade de serviço são configurados por padrão:

   * Gateway ou proxy: contoso.azure-api.net

   * Portal do Azure e portal do Desenvolvedor: contoso.portal.azure-api.net

   * Ponto de extremidade de gerenciamento direto: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

Para acessar esses pontos de extremidade do serviço de Gerenciamento de API, você pode criar uma máquina virtual em uma sub-rede conectada à rede virtual na qual o Gerenciamento de API está implantado. Supondo que o endereço IP virtual interno para seu serviço seja 10.0.0.5, você pode mapear o arquivo de host %SystemDrive%\drivers\etc\hosts como se segue:

   * 10.0.0.5     contoso.azure-api.net

   * 10.0.0.5     contoso.portal.azure-api.net

   * 10.0.0.5     contoso.management.azure-api.net

   * 10.0.0.5     contoso.scm.azure-api.net

Então você pode acessar todos os pontos de extremidade do serviço da máquina virtual criada. Se você usar um servidor DNS personalizado em uma rede virtual, também poderá criar registros DNS A e acessar esses pontos de extremidade de qualquer lugar em sua rede virtual. 

### <a name="access-on-custom-domain-names"></a>Acessar em nomes de domínio personalizados

   1. Se você não quiser acessar o serviço de Gerenciamento de API com os nomes de host padrão, poderá configurar nomes de domínio personalizados para todos os seus pontos de extremidade de serviço, conforme mostra a imagem abaixo: 

   ![Configurar o domínio personalizado para Gerenciamento de API][api-management-custom-domain-name]

   2. Em seguida, você pode criar registros no seu servidor DNS para acessar os pontos de extremidade que só estão acessíveis pela rede virtual.

## Roteamento do <a name="routing"> </a>
+ Um endereço IP virtual privado de carga balanceada do intervalo de sub-rede será reservado e usado para acessar os pontos de extremidade de serviço Gerenciamento de API na vnet.
+ Um endereço IP público com carga balanceada (VIP) também será reservado para fornecer acesso ao ponto de extremidade de serviço de gerenciamento somente pela porta 3443.
+ Um endereço IP de um intervalo IP de sub-rede (DIP) será usado para acessar recursos na VNET e um VIP (endereço IP público) será usado para acessar recursos fora da VNET.
+ Endereços IP públicos e privados com carga balanceada podem ser encontrados na folha Visão Geral/Essentials no portal do Azure.

## <a name="related-content"> </a>Conteúdo relacionado
Para saber mais, consulte os seguintes artigos:
* [Problemas comuns de configuração de rede ao configurar o Gerenciamento de API do Azure em uma rede virtual][Common network configuration problems]
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criando um registro no DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

