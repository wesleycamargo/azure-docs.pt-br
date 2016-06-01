<properties
	pageTitle="Visão geral de aplicativos Web | Microsoft Azure"
	description="Saiba como o Serviço de Aplicativo do Azure o ajuda a desenvolver e hospedar aplicativos Web"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/16/2016"
	ms.author="tdykstra"/>

# Visão geral de aplicativos Web

*Aplicativos Web do Serviço de Aplicativo* é uma plataforma de computação totalmente gerenciada que é otimizada para hospedagem de sites e aplicativos Web. Essa oferta de [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (plataforma como serviço) do Microsoft Azure permite que você se concentre na lógica de negócios enquanto o Azure cuida da infraestrutura para executar e dimensionar os aplicativos.

Para obter uma visão geral em vídeo de cinco minutos, confira [Aplicativos Web do Serviço de Aplicativo do Azure com Yochay Kiriaty](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/).

## O que é um aplicativo Web no Serviço de Aplicativo?

No Serviço de Aplicativo, um *aplicativo Web* consiste nos recursos de computação que o Azure fornece para hospedar um site ou aplicativo Web.

Os recursos de computação podem estar em VMs (máquinas virtuais) compartilhadas ou dedicadas, dependendo da camada de preços que você escolher. O código do aplicativo é executado em uma VM gerenciada que é isolada de outros clientes.

O código pode estar em qualquer linguagem ou estrutura com suporte no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), como ASP.NET, Node.js, Java, PHP ou Python. Você também pode executar scripts que usam o [PowerShell e outras linguagens de script](web-sites-create-web-jobs.md#acceptablefiles) em um aplicativo Web.

Para obter exemplos de cenários típicos de aplicativos para os quais você pode usar aplicativos Web, confira [Cenários de aplicativos Web](https://azure.microsoft.com/documentation/scenarios/web-app/).

## Por que usar aplicativos Web?

Aqui estão alguns dos principais recursos dos aplicativos Web:

- **Familiar e rápido** ‒ use suas habilidades existentes para escrever código em sua linguagem, estrutura e IDE (ambiente de desenvolvimento integrado) favoritos. Provisione novos aplicativos Web e implante código neles em segundos.

- **Nível empresarial** ‒ os aplicativos Web foram projetados para compilar e hospedar aplicativos críticos seguros. Compile aplicativos de negócios integrados do Active Directory que se conectam com segurança a recursos locais e hospede-os em uma plataforma de nuvem segura em conformidade com [ISO](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [SOC](https://www.microsoft.com/TrustCenter/Compliance/SOC) e [PCI](https://www.microsoft.com/TrustCenter/Compliance/pci). Tudo isso com um [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) de nível corporativo.

- **Escala global** ‒ os aplicativos Web permitem que você ajuste a escala [vertical](../app-service/app-service-scale.md) ou [horizontalmente](../azure-portal/insights-how-to-scale.md) com rapidez para lidar com qualquer carga de cliente de entrada. Selecione manualmente o número e tamanho das VMs ou configure o dimensionamento automático com base na carga ou no cronograma. A infraestrutura de datacenter global da Microsoft hospeda os aplicativos Web e torna mais fácil replicar dados e hospedar serviços em vários locais.

- **Azure Marketplace** ‒ selecione de uma [lista de modelos de aplicativos Web](https://azure.microsoft.com/marketplace/) crescente. Aproveite o melhor da comunidade de aplicativos OSS com a instalação com um clique de pacotes, como Wordpress, Joomla e Drupal.

- **Integração contínua** ‒ configure a fluxos de trabalho de [integração e implantação contínuas](app-service-continous-deployment.md) com o Visual Studio Team Services, o GitHub ou o BitBucket. Crie, teste e implante o aplicativo Web automaticamente em cada check-in de código bem-sucedido ou em testes de integração.

- **Ambientes de preparo e teste** ‒ implemente a [implantação de teste](web-sites-staged-publishing.md) para verificar o código em um ambiente de pré-produção que é idêntico a seu ambiente de produção. Quando estiver pronto, lance uma nova versão do aplicativo sem tempo de inatividade executando uma operação de permuta.

- **Testes em produção** ‒ leve as implantações de teste para o próximo patamar e [execute testes A/B](app-service-web-test-in-production-get-start.md) para verificar o novo código com uma fração configurável do tráfego ativo.

- **Trabalhos Web** ‒ [execute qualquer programa ou script](web-sites-create-web-jobs.md) em VMs de aplicativos Web. Execute trabalhos continuamente, de acordo com uma agenda ou disparados por eventos. O [SDK WebJobs](websites-dotnet-webjobs-sdk-get-started.md) do Azure simplifica o código que você escreve para integração com outros serviços do Azure, como Filas de armazenamento, blobs, tabelas e filas do Barramento de Serviço e tópicos.

- **Conexões híbridas** ‒ acesse dados locais usando [conexões híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [VNET](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integração do Visual Studio** ‒ ferramentas dedicadas no Visual Studio simplificam o trabalho de criação, implantação, consumo, depuração e gerenciamento de aplicativos Web. Para obter mais informações, consulte [Anunciando o SDK 2.8.1 do Azure para .NET](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Além disso, um aplicativo Web pode tirar proveito dos recursos oferecidos por [Aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md) (como suporte a CORS) e [Aplicativos Móveis](../app-service-mobile/app-service-mobile-value-prop.md) (como notificações por push). O inverso também é verdadeiro: você pode usar um aplicativo de API ou um aplicativo móvel para hospedar um aplicativo Web e tirar proveito dos recursos de aplicativos Web, como o dimensionamento automático e a implantação de teste. A única diferença entre esses três tipos de aplicativo (API, Web, móveis) consiste no nome e no ícone usados para eles no Portal do Azure. Para saber mais sobre os tipos de aplicativos no Serviço de Aplicativo, confira [Visão geral do Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

## Introdução

Para começar com a implantação do código de exemplo para um novo aplicativo Web no Serviço de Aplicativo, siga o tutorial [Implantar seu primeiro aplicativo Web no Azure em cinco minutos](app-service-web-get-started.md). Você precisará de uma conta gratuita do Azure.

Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<!---HONumber=AcomDC_0525_2016-->