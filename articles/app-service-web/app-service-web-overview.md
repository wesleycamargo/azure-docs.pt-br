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
	ms.date="05/25/2016"
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

Veja alguns recursos importantes do Serviço de Aplicativo que se aplicam aos Aplicativos Web:

- **Plataforma totalmente gerenciada** - Aplicação automática de patch no SO e na estrutura, suporte interno para backup e recuperação de desastre. 

- **Use suas qualificações atuais** - Codifique em sua linguagem, estrutura e ambiente de desenvolvimento favoritos. O Serviço de Aplicativo oferece suporte para .NET, Node.js, Java, PHP e Python.

- **Implantação rápida** - Provisione novos aplicativos e implante o código neles em questão de segundos.

- **Integração contínua** - Configure a [integração contínua e a implantação](../app-service-web/app-service-continous-deployment.md) com o Visual Studio Team Services, o GitHub ou o BitBucket.

- **Ambientes de preparo e teste** ‒ Implemente a [implantação de teste](../app-service-web/web-sites-staged-publishing.md) para verificar o código em um ambiente de pré-produção que é idêntico a seu ambiente de produção. Quando estiver pronto, lance uma nova versão do aplicativo sem tempo de inatividade executando uma operação de permuta.

- **Testes em produção** ‒ Leve as implantações de teste para o próximo patamar e [execute testes A/B](../app-service-web/app-service-web-test-in-production-get-start.md) para verificar o novo código com uma fração configurável do tráfego ativo.

- **Autenticação e autorização** - Proteja um aplicativo contra o acesso não autenticado sem alterações em seu código. Os serviços de autenticação internos protegem seus aplicativos para que sejam acessados por usuários, por clientes que representam os usuários ou por serviços. Os provedores de identidade com suporte incluem Azure Active Directory, Facebook, Twitter, Google e Conta da Microsoft. Para saber mais, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md).

- **Conecte-se a qualquer serviço** - Conecte seu aplicativo aos sistemas corporativos ou plataformas SaaS (software como serviço) em questão de minutos com os [conectores](../connectors/apis-list.md) internos. Escolha entre mais de 50 conectores para os sistemas corporativos, como SAP, Siebel e Oracle, serviços de SaaS corporativo populares, como o Salesforce e Office 365, e serviços de Internet populares, como o Facebook, Twitter e Dropbox.

- **Escala global** - Escale [vertical](../app-service/app-service-scale.md) ou [horizontalmente](../azure-portal/insights-how-to-scale.md) a fim de lidar com qualquer carga de cliente recebida. Selecione manualmente o número e tamanho das VMs ou configure o dimensionamento automático com base na carga ou no cronograma. A infraestrutura de datacenter global da Microsoft hospeda os aplicativos e facilita a replicação dos dados e a hospedagem de serviços em vários locais.

- **Nível empresarial** ‒ O Serviço de Aplicativo foi projetado para compilar e hospedar aplicativos críticos seguros. Compile aplicativos de negócios integrados do Active Directory que se conectam com segurança a recursos locais e hospede-os em uma plataforma de nuvem segura em conformidade com [ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Tudo isso com um [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) de nível corporativo.

- **Azure Marketplace** ‒ Escolha em uma [lista cada vez maior de modelos de aplicativos](https://azure.microsoft.com/marketplace/). Aproveite o melhor da comunidade de aplicativos OSS com a instalação com um clique de pacotes, como WordPress, Joomla e Drupal.

- **WebJobs** - [Execute qualquer programa ou script](../app-service-web/web-sites-create-web-jobs.md) em VMs do Serviço de Aplicativo. Execute trabalhos continuamente, de acordo com uma agenda ou disparados por eventos. O SDK do Azure [WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) simplifica o código que você escreve para integrá-lo a outros serviços do Azure e de terceiros.

- **Conexões híbridas** - Acesse dados locais usando [Conexões híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e as [Redes Virtuais do Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integração com o Visual Studio** - As ferramentas dedicadas no Visual Studio simplificam o trabalho de criação, implantação, consumo, depuração e gerenciamento de aplicativos Web, aplicativos móveis e aplicativos de API.

Além disso, um aplicativo Web pode tirar proveito dos recursos oferecidos por [Aplicativos de API](../app-service-api/app-service-api-apps-why-best-platform.md) (como suporte a CORS) e [Aplicativos Móveis](../app-service-mobile/app-service-mobile-value-prop.md) (como notificações por push). A única diferença entre esses três tipos de aplicativo (API, Web, móveis) consiste no nome e no ícone usados para eles no Portal do Azure. Para saber mais sobre os tipos de aplicativos no Serviço de Aplicativo, confira [Visão geral do Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

Além dos Aplicativos Web no Serviço de Aplicativo, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos Web. Os Aplicativos Web são a melhor opção para a maioria dos cenários. Para a arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric), e se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para saber mais sobre como escolher entre esses serviços do Azure, confira [Comparação entre o Serviço de Aplicativo, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure](choose-web-site-cloud-service-vm.md).

## Introdução

Para começar com a implantação do código de exemplo para um novo aplicativo Web no Serviço de Aplicativo, siga o tutorial [Implantar seu primeiro aplicativo Web no Azure em cinco minutos](app-service-web-get-started.md). Você precisará de uma conta gratuita do Azure.

Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<!---HONumber=AcomDC_0601_2016-->