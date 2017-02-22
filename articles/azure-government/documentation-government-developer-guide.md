---
title: Guia do desenvolvedor do Azure Governamental | Microsoft Docs
description: Este artigo compara recursos e fornece diretrizes de como desenvolver aplicativos para o Azure Governamental.
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: 0bab116e977a8490bf0c114c8b829aa1ce07e923
ms.openlocfilehash: 41af18426ce3f8124458a61c3af280c3dd17cfcd


---
# <a name="azure-government-developer-guide"></a>Guia do desenvolvedor do Azure Governamental
O ambiente do Azure Governamental é uma instância física separada do restante da rede da Microsoft. Este guia aborda as diferenças que os desenvolvedores e administradores de aplicativo devem entender para interagir e trabalhar com regiões separadas do Azure.

## <a name="overview"></a>Visão geral
O Azure Governamental é uma instância separada do serviço do Microsoft Azure. Ele atende às necessidades de segurança e conformidade das agências federais dos Estados Unidos, dos governos locais e estaduais, e seus provedores de solução. O Azure Governamental oferece isolamento físico e de rede de implantações não governamentais e fornece triagem do pessoal dos EUA.

A Microsoft fornece várias ferramentas que ajudam os desenvolvedores a criar e implantar aplicativos de nuvem no serviço global do Microsoft Azure ("serviço global") e serviços governamentais do Microsoft Azure.

Quando os desenvolvedores criam e implantam aplicativos nos serviços do Azure Governamental, em oposição ao serviço global, eles precisam saber as principais diferenças entre os dois serviços. As áreas específicas a serem entendidas são: instalar e configurar o ambiente de programação, configurar pontos de extremidade, escrever aplicativos e implantar os aplicativos como serviços no Azure Governamental.

As informações neste documento resumem as diferenças entre os dois serviços. Elas complementam as informações disponíveis sobre o site do [Azure Governamental](http://www.azure.com/gov "Azure Governamental") e a [Biblioteca Técnica do Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") no MSDN. As informações oficiais também podem estar disponíveis em muitos outros locais, como a [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Central de Confiabilidade do Microsoft Azure"), o [Centro de Documentos do Azure](https://azure.microsoft.com/documentation/) e os [Blogs do Azure](https://azure.microsoft.com/blog/ "Blogs do Azure").

Este conteúdo destina-se a parceiros e desenvolvedores que estão implantando o Microsoft Azure Government.

## <a name="guidance-for-developers"></a>Diretrizes para desenvolvedores
A maioria do conteúdo técnico disponível no momento pressupõe que os aplicativos estão sendo desenvolvidos para o serviço global, e não para o Azure Governamental. Por esse motivo, é importante estar ciente das duas principais diferenças em aplicativos que você desenvolve para hospedagem no Azure Governamental.

* Determinados serviços e recursos que estão em regiões específicas do serviço global podem não estar disponíveis no Azure Governamental.
* As configurações de recurso no Azure Governamental podem diferente daquelas no serviço global. Portanto, é importante examinar o código de exemplo, as configurações e as etapas para garantir que você esteja criando e executando no ambiente de Serviços de Nuvem do Azure Governamental.

Atualmente, o Gov. EUA - Iowa e o Gov. EUA - Virgínia são os datacenters que dão suporte ao Azure Governamental. Para conhecer datacenters atuais e serviços disponíveis, confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/services).


## <a name="endpoint-mapping"></a>Mapeamento de ponto de extremidade
Para saber como mapear os pontos de extremidade públicos do Azure e do Banco de Dados SQL para os pontos de extremidade específicos do Azure Governamental, veja a seguinte tabela:

| Nome | Ponto de extremidade do Azure Governamental |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId  | https://management.core.usgovcloudapi.net/ |
| GalleryUrl | https://gallery.usgovcloudapi.net/ |
| ManagementPortalUrl | https://manage.windowsazure.us |
| ServiceManagementUrl | https://management.core.usgovcloudapi.net/ |
| PublishSettingsFileUrl | https://manage.windowsazure.us/publishsettings/index |
| ResourceManagerUrl | https://management.usgovcloudapi.net/ |
| SqlDatabaseDnsSuffix | .database.usgovcloudapi.net |
| StorageEndpointSuffix | core.usgovcloudapi.net |
| ActiveDirectoryAuthority | https://login-us.microsoftonline.com/ |
| GraphUrl | https://graph.windows.net/ |
| GraphEndpointResourceId | https://graph.windows.net/ |
| TrafficManagerDnsSuffix | usgovtrafficmanager.net |
| AzureKeyVaultDnsSuffix | vault.usgovcloudapi.net |
| AzureKeyVaultServiceEndpointResourceId | https://vault.usgovcloudapi.net |



## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Azure Governamental, confira os seguintes recursos:

* [Inscrever-se para obter uma avaliação](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Adquirindo e acessando o Azure Governamental](http://azure.com/gov)
* [Visão geral do Azure Governamental](/azure-government-overview)
* [Blog do Azure Governamental](http://blogs.msdn.microsoft.com/azuregov/)
* [Conformidade do Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)



<!--HONumber=Feb17_HO3-->


