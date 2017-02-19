---
title: Guia do desenvolvedor do Azure Governamental | Microsoft Docs
description: Este artigo compara recursos e fornece diretrizes de como desenvolver aplicativos para o Azure Governamental.
services: azure-government
cloud: gov
documentationcenter: 
author: sachamicrosoft
manager: zakramer
ms.assetid: 6e04e9aa-1a73-442c-a46c-2e4ff87e58d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/29/2015
ms.author: jharve
translationtype: Human Translation
ms.sourcegitcommit: e0e313f2f3a077f45ba5935363c251bba89bc524
ms.openlocfilehash: c20736554425204f47fed96f8618f66002623259


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

## <a name="available-features-and-services-in-azure-government"></a>Recursos e serviços disponíveis no Azure Governamental
Os recursos e serviços a seguir do Azure Governamental estão disponíveis nas regiões Gov. EUA - Iowa e Gov. EUA - Virgínia:

* Máquinas Virtuais
* Conjuntos de Escala de Máquina Virtual
* Serviço de Contêiner
* Contas do Lote
* Coleções de Aplicativo Remoto
* Conjuntos de disponibilidade
* Rede Virtual
* Balanceador de carga
* Gateway de Aplicativo
* Gateway de Rede Virtual
* Gateways de rede local
* Tabelas de rotas
* Perfis do Gerenciador de Tráfego
* Circuitos da Rota Expressa
* Grupos de segurança de rede
* Interfaces de rede
* Endereços IP públicos
* Conexões
* Contas de armazenamento
* StorSimple Manager
* Serviço de Aplicativo
* Serviços de mídia
* Banco de Dados SQL
* SQL Data Warehouse
* SQL Server Stretch Database
* Cache Redis
* Pools elásticos do Banco de Dados SQL
* SQL Server
* Log Analytics
* Hubs de Eventos
* Namespaces do Barramento de Serviço
* Azure Active Directory
* Multi-Factor Authentication
* Rights Management
* Contas de automação
* Marketplace

Outros serviços estão disponíveis e mais serviços são adicionados continuamente. Para obter a lista mais atual de serviços, confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/#services), uma página que exibe os serviços disponíveis em cada região.

Atualmente, o Gov. EUA - Iowa e o Gov. EUA - Virgínia são os datacenters que dão suporte ao Azure Governamental. Para conhecer datacenters atuais e serviços disponíveis, confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/#services).

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

Para a autenticação do Azure Resource Manager por meio do Azure Active Directory, veja [Authenticating Azure Resource Manager Requests](https://msdn.microsoft.com/library/azure/dn790557.aspx) (Autenticando solicitações do Azure Resource Manager).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Azure Governamental, confira os seguintes recursos:

* [Inscrever-se para obter uma avaliação](https://azuregov.microsoft.com/trial/azuregovtrial)
* [Adquirindo e acessando o Azure Governamental](http://azure.com/gov)
* [Visão geral do Azure Governamental](/azure-government-overview)
* [Blog do Azure Governamental](http://blogs.msdn.com/b/azuregov/)
* [Conformidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/)



<!--HONumber=Jan17_HO1-->


