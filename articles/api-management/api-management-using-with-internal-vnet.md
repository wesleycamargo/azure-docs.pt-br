---
title: Como usar o Gerenciamento de API do Azure com redes virtuais internas | Microsoft Docs
description: Saiba como instalar e configurar o Gerenciamento de API do Azure na rede virtual interna.
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 456c541be7aadcce494bbd54e97deb6f30d5141b
ms.openlocfilehash: dc9af7e4ef9599886d1be6676f88f71f80c20474


---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>Usar o serviço de Gerenciamento de API do Azure com rede virtual interna
Com as Redes virtuais (VNETs) do Azure, o Gerenciamento de API pode administrar as APIs que não estão acessíveis pela Internet. Várias tecnologias de VPN estão disponíveis para fazer a conexão e o Gerenciamento de API pode ser implantado em dois modos principais dentro da VNET:
* Externo
* Interna

## <a name="overview"> </a>Visão geral
Quando o Gerenciamento de API é implantado em um modo de rede virtual interna, todos os pontos de extremidade de serviço (gateway, portal do desenvolvedor, portal do editor, gerenciamento direto e Git) ficam visíveis apenas de dentro de uma rede virtual à qual você controla o acesso. Nenhum ponto de extremidade de serviço é registrado no servidor DNS público.

Usando o Gerenciamento de API no modo interno, você pode chegar aos seguintes cenários
* Torne as APIs hospedadas em seu datacenter privado seguras e acessíveis por terceiros usando as conexões de VPN Site a Site ou ExpressRoute.
* Habilite cenários de nuvem híbrida, expondo as APIs baseadas em nuvem e as APIs locais por meio de um gateway comum.
* Gerencie suas APIs hospedadas em várias localizações geográficas usando um único ponto de extremidade de Gateway. 

## <a name="enable-vpn"> </a>Criar um Gerenciamento de API na rede virtual interna
O serviço de Gerenciamento de API na rede virtual interna é hospedado por trás de um Balanceador de Carga Interno (ILB). O endereço IP do ILB está no intervalo [RFC1918](http://www.faqs.org/rfcs/rfc1918.html).  

### <a name="enable-vnet-connection-using-azure-portal"></a>Habilitar a conexão de VNET usando o Portal do Azure
Primeiro, crie o serviço de Gerenciamento de API seguindo as etapas em [Criar serviço de Gerenciamento de API][Create API Management service]. Em seguida, configure o Gerenciamento de API a ser implantado dentro de uma rede virtual.

![Menu de configuração de APIM na rede virtual interna][api-management-using-internal-vnet-menu]

Após a implantação bem-sucedida, você deve ver o endereço IP virtual interno do serviço no painel.

![Painel de Gerenciamento de API com a VNET interna configurada][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>Habilitar a conexão de VNET usando cmdlets do Powershell
Você também pode habilitar a conectividade de VNET usando os cmdlets do PowerShell.

* **Criar um serviço de Gerenciamento de API dentro de uma VNET**: use o cmdlet [New-AzureRmApiManagement](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/new-azurermapimanagement) para criar um serviço de Gerenciamento de API do Azure dentro de uma VNET e configurá-lo para usar o tipo de VNET interna.

* **Implantar um serviço de Gerenciamento de API existente dentro de uma VNET**: use o cmdlet [Update-AzureRmApiManagementDeployment](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/update-azurermapimanagementdeployment) para mover um serviço de Gerenciamento de API do Azure existente para uma rede virtual e configurá-lo para usar o tipo de VNET interna.

## <a name="a-nameapim-dns-configurationadns-configuration"></a><a name="apim-dns-configuration"></a>Configuração de DNS
Ao usar o Gerenciamento de API em modo de rede virtual externa, o DNS é gerenciado pelo Azure. Para o modo de rede virtual interna, você precisa gerenciar o seu próprio DNS.

> [!NOTE]
> O serviço de Gerenciamento de API não escuta as solicitações que chegam de endereços IP. Ele responde apenas às solicitações do nome de host configurado em seus pontos de extremidade de serviço (que inclui o gateway, o portal do desenvolvedor, o portal do editor, o ponto de extremidade de gerenciamento direto e git).

### <a name="access-on-default-host-names"></a>Acesso em nomes de host padrão:
Ao criar um serviço de Gerenciamento de API na nuvem pública do Azure, chamado "contoso" por exemplo, os seguintes pontos de extremidade de serviço são configurados por padrão.

>   Gateway/Proxy: contoso.azure-api.net

> Portal do Editor e Portal do desenvolvedor: contoso.portal.azure-api.net

> Ponto de extremidade de gerenciamento direto: contoso.management.azure-api.net

>   GIT: contoso.scm.azure-api.net

Para acessar esses pontos de extremidade do serviço de Gerenciamento de API, você pode criar uma máquina virtual em uma sub-rede conectada à rede virtual na qual o Gerenciamento de API é implantado. Supondo que o endereço IP virtual interno para seu serviço seja 10.0.0.5, você pode fazer o mapeamento dos arquivos de hosts (%UnidadeDoSistema%\drivers\etc\hosts) como se segue:

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

Em seguida pode acessar todos os pontos de extremidade do serviço da máquina virtual criada. Se usar um servidor DNS personalizado em uma rede virtual, você também pode criar registros A de DNS e acessar esses pontos de extremidade de qualquer lugar em sua rede virtual. 

### <a name="access-on-custom-domain-names"></a>Acessar em nomes de domínio personalizados:
Se você não quiser acessar o serviço de Gerenciamento de API com os nomes de host padrão, pode configurar nomes de domínio personalizados para todos os seus pontos de extremidade de serviço como abaixo

![Configurar o domínio personalizado para Gerenciamento de API][api-management-custom-domain-name]

Em seguida, você pode criar registros A no seu servidor DNS para acessar esses pontos de extremidade que só são acessíveis a partir da rede virtual.

## <a name="related-content"> </a>Conteúdo relacionado
* [Problemas comuns de configuração de rede durante a configuração de APIM na VNET][Common Network Configuration Issues]
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Criar registro A no DNS](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues



<!--HONumber=Jan17_HO3-->


