---
title: "Visão geral de aplicativos Web | Microsoft Docs"
description: "Saiba como o Serviço de Aplicativo do Azure o ajuda a desenvolver e hospedar aplicativos Web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>Visão geral de aplicativos Web

*Aplicativos Web do Serviço de Aplicativo do Azure* (ou apenas Aplicativos Web) é um serviço de hospedagem de aplicativos Web, APIs REST e back-ends móveis. Você pode desenvolver usando sua linguagem favorita, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Você pode executar e dimensionar os aplicativos com facilidade em VMs com Windows ou Linux (consulte [Serviço de Aplicativo no Linux](containers/app-service-linux-intro.md)). 

Os Aplicativos Web não acrescentam apenas os recursos do Microsoft Azure ao seu aplicativo, como segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Você pode também aproveitar seus recursos de DevOps, como implantação contínua a partir do VSTS, GitHub, Hub do Docker e outras fontes, gerenciamento de pacote, ambientes de preparo, domínio personalizado e certificados SSL. 

Com o Serviço de Aplicativo, você paga pelos recursos de computação do Azure que usar. Os recursos de computação que você usa são determinados pelo _Plano do Serviço de Aplicativo_ no qual seus aplicativos Web são executados. Para saber mais, confira [Planos do Serviço de Aplicativo nos Aplicativos Web do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

O vídeo de 5 minutos a seguir apresenta os Aplicativos Web do Serviço de Aplicativo do Azure.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>Por que usar aplicativos Web?
Veja alguns recursos importantes dos Aplicativos Web do Serviço de Aplicativo:

* **Várias linguagens e estruturas** - Os Aplicativos Web têm suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Você também pode executar o [PowerShell e outros scripts ou executáveis](web-sites-create-web-jobs.md) como serviços em segundo plano.
* **Otimização de DevOps** - configure a [implantação e integração contínua](app-service-continuous-deployment.md) com o Visual Studio Team Services, o GitHub ou BitBucket, Hub do Docker ou Serviço de Contêiner do Azure. Promova atualizações por meio de [ambientes de preparo e teste](web-sites-staged-publishing.md). Gerencie aplicativos em Aplicativos Web usando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [CLI (interface de linha de comando de plataforma cruzada)](/cli/azure/install-azure-cli).
* **Escala global com alta disponibilidade** - escale [verticalmente](web-sites-scale.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Hospede os aplicativos em qualquer lugar na infraestrutura de datacenter global da Microsoft, e o [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) do Serviço de Aplicativo promete alta disponibilidade.
* **Conexões com plataformas SaaS e dados locais** - escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP), serviços de SaaS (como Salesforce) e serviços de Internet (como Facebook). Acesse dados locais usando [Conexões Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes Virtuais do Azure](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** - o Serviço de Aplicativo está em [conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Autentique usuários com o [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) ou com logon social ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) e [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Crie [Restrições de endereço IP](app-service-ip-restrictions.md) e [gerencie identidades de serviço](app-service-managed-service-identity.md).
* **Modelos de aplicativos** - escolha dentre uma lista abrangente de modelos de aplicativos no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como WordPress, Joomla e Drupal.
* **Integração do visual Studio** -ferramentas dedicadas no Visual Studio simplificam o trabalho de criar, implantar, depurar e gerenciar.
* **API e recursos móveis** - Os Aplicativos Web oferecem suporte imediato a CORS para cenários de API RESTful e simplificam os cenários de aplicativos móveis permitindo a autenticação, a sincronização de dados offline, as notificações por push e muito mais.
* **Código sem servidor** - Execute um trecho de código ou um script sob demanda sem a necessidade de provisionar explicitamente ou gerenciar a infraestrutura, e pague somente pelo tempo de computação usado pelo seu código (consulte [Azure Functions](/azure/azure-functions/)).

Além dos Aplicativos Web no Serviço de Aplicativo, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos Web. Os Aplicativos Web são a melhor opção para a maioria dos cenários.  Para arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para saber mais sobre como escolher entre esses serviços do Azure, confira [Comparação entre o Serviço de Aplicativo do Azure, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo Web.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

