<properties 
	pageTitle="Serviço de Aplicativo do Azure para aplicativos Web e aplicativos móveis | Microsoft Azure" 
	description="Saiba como o Serviço de Aplicativo do Azure ajuda você a desenvolver, implantar e gerenciar aplicativos móveis e da Web." 
	keywords="serviço de aplicativo, serviço de aplicativo do azure, custo do serviço de aplicativo, escala, escalonável, implantação de aplicativo, implantação de aplicativo do azure, paas, plataforma como serviço"
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# O que é o Serviço de Aplicativo do Azure?

O *Serviço de Aplicativo* é uma oferta de [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (plataforma como serviço) do Microsoft Azure. Crie aplicativos Web e móveis para qualquer plataforma ou dispositivo. Integre seus aplicativos a soluções SaaS, conecte-se com aplicativos locais e automatize os processos de negócios. O Azure executa os aplicativos em VMs (máquinas virtuais) totalmente gerenciadas, com sua escolha de recursos compartilhados de VM ou VMs dedicadas.

O Serviço de Aplicativo inclui os recursos móveis e da Web que antes eram fornecidos separadamente, como os Sites do Azure e os Serviços Móveis do Azure. Ele também inclui novos recursos para automatizar processos empresariais e hospedagem de APIs de nuvem. Como um único serviço integrado, o Serviço de Aplicativo permite incluir vários componentes (sites, back-ends de aplicativo móvel, APIs RESTful e processos de negócios) em uma única solução.

O vídeo de quatro minutos a seguir fornece uma breve explicação de como o Serviço de Aplicativo se relaciona às ofertas anteriores do Azure e o que há de novo nele.

\+[AZURE.VIDEO app-service-history-lesson] 

## Por que usar o Serviço de Aplicativo?

Veja alguns recursos importantes do Serviço de Aplicativo:

- **Várias linguagens e estruturas** -o Serviço de Aplicativo tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Você também pode executar [o Windows PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) nas VMs do Serviço de Aplicativo.

- **Otimização de DevOps** - configure a [implantação e integração contínua](../app-service-web/app-service-continous-deployment.md) com o Visual Studio Team Services, o GitHub ou BitBucket. Promova atualizações por meio de [ambientes de preparo e teste](../app-service-web/web-sites-staged-publishing.md). Execute [testes A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Gerencie aplicativos no Serviço de Aplicativo usando o [Azure PowerShell](../powershell-install-configure.md) ou a [CLI (interface de linha de comando de plataforma cruzada)](../xplat-cli-install.md).
 
- **Escala global com alta disponibilidade** - escale [verticalmente](../app-service-web/web-sites-scale.md) ou [horizontalmente](../azure-portal/insights-how-to-scale.md) de forma manual ou automática. Hospede os aplicativos em qualquer lugar na infraestrutura de datacenter global da Microsoft, e o [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) do Serviço de Aplicativo promete alta disponibilidade.

- **Conexões com plataformas SaaS e dados locais** - escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP, Siebel e Oracle), serviços de SaaS (como Salesforce e Office 365) e serviços de Internet (como Facebook e Twitter). Acesse dados locais usando [Conexões Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes Virtuais do Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Segurança e conformidade** - o Serviço de Aplicativo está em [conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).

- **Modelos de aplicativos** - escolha dentre uma lista abrangente de modelos de aplicativos no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem usar um assistente para instalar vários tipos populares de software livre, como WordPress, Joomla e Drupal.

- **Integração do Visual Studio** - ferramentas dedicadas no Visual Studio simplificam o trabalho de criação, implantação e depuração.

## Tipos de aplicativo no Serviço de Aplicativo

O Serviço de Aplicativo oferece vários *tipos de aplicativo*, cada um dos quais se destina a hospedar um tipo específico de carga de trabalho:

- [**Aplicativos Web**](../app-service-web/app-service-web-overview.md) - para hospedagem de sites e aplicativos Web.

- [**Aplicativos Móveis**](../app-service-mobile/app-service-mobile-value-prop.md) - para hospedagem de back-ends de aplicativos móveis.
   
- [**Aplicativos de API**](../app-service-api/app-service-api-apps-why-best-platform.md) - para hospedagem de APIs de nuvem.
 
- [**Aplicativos Lógicos**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - para automatização do acesso e do uso de dados entre nuvens, sem a necessidade de escrever código.

A palavra *aplicativo* aqui refere-se aos recursos de hospedagem dedicados à execução de uma carga de trabalho. Usando "aplicativo Web" como exemplo, você provavelmente já está acostumado a pensar em um aplicativo Web como os recursos de computação e o código do aplicativo que, juntos, fornecem funcionalidade a um navegador. Porém, no Serviço de Aplicativo, um *aplicativo Web* consiste nos recursos de computação que o Azure fornece para hospedar o código do aplicativo. Se o aplicativo é composto de um front-end da Web e um back-end de API RESTful, você pode implantar ambos em um aplicativo Web ou pode implantar o código de front-end em um aplicativo Web e o código de back-end em um aplicativo de API. O aplicativo pode ser composto de vários aplicativos do Serviço de Aplicativo de tipos diferentes.

## Planos de serviço de aplicativo

Os [Planos do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) especificam o tipo de recursos de computação em que os aplicativos são executados. Se a sua expectativa for um tráfego leve, utilize VMs (máquinas virtuais) compartilhadas. Para cargas maiores, selecione um dos vários tamanhos de VMs dedicadas. Vários aplicativos do Serviço de Aplicativo podem compartilhar o mesmo plano e eles são escalados verticalmente e horizontalmente junto com o plano.

Se precisar de mais escalabilidade e isolamento da rede, execute os aplicativos em um [Ambiente do Serviço de Aplicativo](../app-service-web/app-service-app-service-environment-intro.md).

## Preços

Para saber mais sobre os custos do Serviço de Aplicativo, confira [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

## Introdução ao Serviço de Aplicativo

[Crie um aplicativo Web, um aplicativo móvel ou um aplicativo lógico temporário](http://go.microsoft.com/fwlink/?LinkId=523751) imediatamente e de graça, sem a necessidade de cartão de crédito, sem compromissos ou dificuldades.

Ou abra uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/) e experimente um de nossos tutoriais de introdução:

* [Tutorial: criar um aplicativo Web](../app-service-web/app-service-web-get-started.md)
* [Tutorial: criar um aplicativo móvel](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Tutorial: criar um aplicativo de API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Tutorial: criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0706_2016-->