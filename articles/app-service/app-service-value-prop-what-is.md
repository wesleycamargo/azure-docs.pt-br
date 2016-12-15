---
title: "Serviço de Aplicativo do Azure para aplicativos Web, aplicativos móveis e de API | Microsoft Docs"
description: "Saiba como o Serviço de Aplicativo do Azure ajuda você a desenvolver, implantar e gerenciar aplicativos móveis e da Web."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, custo do serviço de aplicativo, escala, dimensionável, implantação de aplicativos, implantação de aplicativo do azure, paas, plataforma como serviço, site, site da web, web, aplicativo móvel do azure"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/02/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f361e9c815ae20874fc2e40e39d2c20a415d58f2


---
# <a name="what-is-azure-app-service"></a>O que é o Serviço de Aplicativo do Azure?
*Serviço de Aplicativo* é uma oferta de [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (plataforma como serviço) do Microsoft Azure. Crie aplicativos Web e móveis para qualquer plataforma ou dispositivo. Integre seus aplicativos a soluções SaaS, conecte-se com aplicativos locais e automatize os processos de negócios. O Azure executa os aplicativos em VMs (máquinas virtuais) totalmente gerenciadas, com sua escolha de recursos compartilhados de VM ou VMs dedicadas.

O Serviço de Aplicativo inclui os recursos móveis e da Web que antes eram fornecidos separadamente, como os Sites do Azure e os Serviços Móveis do Azure. Ele também inclui novos recursos para automatizar processos empresariais e hospedagem de APIs de nuvem. Como um único serviço integrado, o Serviço de Aplicativo permite incluir vários componentes (sites, back-ends de aplicativo móvel, APIs RESTful e processos de negócios) em uma única solução.

O vídeo de quatro minutos a seguir fornece uma breve explicação de como o Serviço de Aplicativo se relaciona às ofertas anteriores do Azure e o que há de novo nele.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>Por que usar o Serviço de Aplicativo?
Veja alguns recursos importantes do Serviço de Aplicativo:

* **Várias linguagens e estruturas** -o Serviço de Aplicativo tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Você também pode executar [o Windows PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) nas VMs do Serviço de Aplicativo.
* **Otimização de DevOps** - configure a [implantação e integração contínua](../app-service-web/app-service-continuous-deployment.md) com o Visual Studio Team Services, o GitHub ou BitBucket. Promova atualizações por meio de [ambientes de preparo e teste](../app-service-web/web-sites-staged-publishing.md). Execute [testes A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Gerencie aplicativos no Serviço de Aplicativo usando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [CLI (interface de linha de comando de plataforma cruzada)](../xplat-cli-install.md).
* **Escala global com alta disponibilidade** - escale [verticalmente](../app-service-web/web-sites-scale.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Hospede os aplicativos em qualquer lugar na infraestrutura de datacenter global da Microsoft, e o [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) do Serviço de Aplicativo promete alta disponibilidade.
* **Conexões com plataformas SaaS e dados locais** - escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP, Siebel e Oracle), serviços de SaaS (como Salesforce e Office 365) e serviços de Internet (como Facebook e Twitter). Acesse dados locais usando [Conexões Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes Virtuais do Azure](../app-service-web/web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** - o Serviço de Aplicativo está em [conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).
* **Modelos de aplicativos** - escolha dentre uma lista abrangente de modelos no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem usar um assistente para instalar vários tipos populares de software livre, como WordPress, Joomla e Drupal.
* **Integração do visual Studio** -ferramentas dedicadas no Visual Studio simplificam o trabalho de criar, implantar, depurar e gerenciar.

## <a name="app-types-in-app-service"></a>Tipos de aplicativo no Serviço de Aplicativo
O Serviço de Aplicativo oferece vários *tipos de aplicativo*, cada um dos quais se destina a hospedar um tipo específico de carga de trabalho:

* [**Aplicativos Web**](../app-service-web/app-service-web-overview.md) - para hospedar sites e aplicativos Web.
* [**Aplicativos Móveis**](../app-service-mobile/app-service-mobile-value-prop.md) Para hospedar os back-ends do aplicativo móvel.
* [**Aplicativos da API**](../app-service-api/app-service-api-apps-why-best-platform.md) - Para hospedar as APIs RESTful.
* [**Aplicativos Lógicos**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - Para automatizar os processos de negócios e integrar os sistemas e dados nas nuvens sem escrever código.

A palavra *aplicativo* aqui refere-se aos recursos de hospedagem dedicados à execução de uma carga de trabalho. Usando "aplicativo Web" como exemplo, você provavelmente já está acostumado a pensar em um aplicativo Web como os recursos de computação e o código do aplicativo que, juntos, fornecem funcionalidade a um navegador. Porém, no Serviço de Aplicativo, um *aplicativo Web* consiste nos recursos de computação que o Azure fornece para hospedar o código do aplicativo. 

O aplicativo pode ser composto de vários aplicativos do Serviço de Aplicativo de tipos diferentes. Por exemplo, se seu aplicativo é composto de um front-end da Web e um back-end de API RESTful, você pode:

- Implantar ambos (front-end e API) em um único aplicativo Web  
- Implantar seu código de front-end em um aplicativo Web e seu código de back-end em um aplicativo de API. 



## <a name="app-service-plans"></a>Planos do Serviço de Aplicativo
Os [Planos do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) representam a coleta de recursos físicos usados para hospedar seus aplicativos.

Os Planos do Serviço de Aplicativo definem:

- **Região** (Oeste dos EUA, Leste dos EUA, etc.)
- **Contagem da escala** (uma, duas, três instâncias, etc.)
- **Tamanha da instância** (Pequena, Média, Grande)
- **SKU** (Gratuito, Compartilhado, Básico, Standard, Premium)

Todos os aplicativos atribuídos a um **Plano do Serviço de Aplicativo** compartilham os recursos definidos pelo plano, permitindo que você economize ao hospedar vários aplicativos.

O seu **Plano do Serviço de Aplicativo** pode variar dos SKUs **Gratuito** e **Compartilhado** para os SKUs **Básico**, **Standard** e **Premium**, dando a você mais recursos e funções ao longo do caminho. Depois que seu plano do Serviço de Aplicativo é definido como **Básico** ou superior, também é possível controlar o **tamanho** e dimensionar a contagem de VMs.

O **SKU** e a **Escala** do plano do Serviço de Aplicativo determina o custo e não o número de aplicativos hospedados nele. 

Se precisar de mais escalabilidade e isolamento da rede, execute os aplicativos em um [Ambiente do Serviço de Aplicativo](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Preços
Para saber mais sobre os custos do Serviço de Aplicativo, confira [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Serviço de Aplicativo orientado a teste
[Crie um aplicativo Web, um aplicativo móvel ou um aplicativo lógico de exemplo](http://go.microsoft.com/fwlink/?LinkId=523751) e use-o por 1 hora, sem nenhum cartão de crédito, comprometimento ou inconvenientes.

Ou abra uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/)e experimente um de nossos tutoriais de introdução:

* [Tutorial: criar um aplicativo Web](../app-service-web/app-service-web-get-started.md)
* [Tutorial: criar um aplicativo móvel](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Tutorial: criar um aplicativo de API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Tutorial: criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Dec16_HO2-->


