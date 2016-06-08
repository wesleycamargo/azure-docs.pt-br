<properties 
	pageTitle="O que é o Serviço de Aplicativo do Azure | Microsoft Azure" 
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

*Serviço de Aplicativo* é uma oferta de [PaaS (plataforma como serviço)](https://en.wikipedia.org/wiki/Platform_as_a_service) do Microsoft Azure que permite a criação de aplicativos Web e móveis para qualquer plataforma ou dispositivo. Você pode integrar com facilidade seus aplicativos às soluções SaaS (como o Office 365, Dynamics CRM, Salesforce, Twilio), conectar com facilidade aos aplicativos locais (como SAP, Oracle, Siebel) e automatizar facilmente os processos empresariais atendendo às necessidades rígidas de segurança, confiabilidade e escalabilidade.

O Serviço de Aplicativo inclui os recursos móveis e da Web que antes eram fornecidos separadamente, como os Sites do Azure e os Serviços Móveis do Azure. Ele também inclui novos recursos para automatizar processos empresariais e hospedagem de APIs de nuvem.

## Tipos de aplicativo no Serviço de Aplicativo

O Serviço de Aplicativo oferece os seguintes tipos de aplicativo para executar seus processos de fluxo de trabalho ou código de aplicativo.

- [**Aplicativos Web**](../app-service-web/app-service-web-overview.md) - Para hospedagem de sites e aplicativos Web.

- [**Aplicativos Móveis**](../app-service-mobile/app-service-mobile-value-prop.md) Para hospedagem de back-ends de aplicativo móvel.
   
- [**Aplicativos de API**](../app-service-api/app-service-api-apps-why-best-platform.md) - Para hospedagem de APIs de nuvem.
 
- [**Aplicativos Lógicos**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - Para automatização do acesso e do uso dos dados nas nuvens, sem a necessidade de escrever código.

O Serviço de Aplicativo, por ser um serviço único integrado, facilita a composição de vários tipos de aplicativo em uma única solução.

## Planos do Serviço de Aplicativo e Ambientes

Os [Planos do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) representam os recursos de computação sobre os quais seu aplicativo é executado. Nos tipos de preços mais baixos, seus aplicativos são executados em VMs (máquinas virtuais) compartilhadas. Em tipos mais altos, seus aplicativos são executados em VMs dedicadas. Há vários tamanhos de VM para sua escolha, e você pode alterar seu tipo de preço sem qualquer interrupção na atividade. Se você precisar de mais escalabilidade e isolamento de rede, poderá executar seus aplicativos em um [Ambiente do Serviço de Aplicativo](../app-service-web/app-service-app-service-environment-intro.md).

## Por que usar o Serviço de Aplicativo?

Veja alguns recursos importantes do Serviço de Aplicativo:

- **Plataforma totalmente gerenciada** - Aplicação automática de patch no SO e na estrutura, suporte interno para backup e recuperação de desastre. 

- **Use suas qualificações atuais** - Codifique em sua linguagem, estrutura e ambiente de desenvolvimento favoritos. O Serviço de Aplicativo oferece suporte para .NET, Node.js, Java, PHP e Python.

- **Implantação rápida** - Provisione novos aplicativos e implante o código neles em questão de segundos.

- **Integração contínua** - Configure a [integração contínua e a implantação](../app-service-web/app-service-continous-deployment.md) com o Visual Studio Team Services, o GitHub ou o BitBucket.

- **Ambientes de preparo e teste** ‒ Implemente a [implantação de teste](../app-service-web/web-sites-staged-publishing.md) para verificar o código em um ambiente de pré-produção que é idêntico a seu ambiente de produção. Quando estiver pronto, lance uma nova versão do aplicativo sem tempo de inatividade executando uma operação de permuta.

- **Testes em produção** ‒ Leve as implantações de teste para o próximo patamar e [execute testes A/B](../app-service-web/app-service-web-test-in-production-get-start.md) para verificar o novo código com uma fração configurável do tráfego ativo.

- **Autenticação e autorização** - Proteja um aplicativo contra o acesso não autenticado sem alterações em seu código. Os serviços de autenticação internos protegem seus aplicativos para que sejam acessados por usuários, por clientes que representam os usuários ou por serviços. Os provedores de identidade com suporte incluem Azure Active Directory, Facebook, Twitter, Google e Conta da Microsoft. Para saber mais, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](app-service-authentication-overview.md).

- **Conecte-se a qualquer serviço** - Conecte seu aplicativo aos sistemas corporativos ou plataformas SaaS (software como serviço) em questão de minutos com os [conectores](../connectors/apis-list.md) internos. Escolha entre mais de 50 conectores para os sistemas corporativos, como SAP, Siebel e Oracle, serviços de SaaS corporativo populares, como o Salesforce e Office 365, e serviços de Internet populares, como o Facebook, Twitter e Dropbox.

- **Escala global** - Escale [vertical](../app-service/app-service-scale.md) ou [horizontalmente](../azure-portal/insights-how-to-scale.md) a fim de lidar com qualquer carga de cliente recebida. Selecione manualmente o número e tamanho das VMs ou configure o dimensionamento automático com base na carga ou no cronograma. A infraestrutura de datacenter global da Microsoft hospeda os aplicativos e facilita a replicação dos dados e a hospedagem de serviços em vários locais.

- **Nível empresarial** ‒ O Serviço de Aplicativo foi projetado para compilar e hospedar aplicativos críticos seguros. Compile aplicativos de negócios integrados do Active Directory que se conectam com segurança a recursos locais e hospede-os em uma plataforma de nuvem segura em conformidade com [ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Tudo isso com um [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) de nível corporativo.

- **Azure Marketplace** ‒ Escolha em uma [lista cada vez maior de modelos de aplicativos](https://azure.microsoft.com/marketplace/). Aproveite o melhor da comunidade de aplicativos OSS com a instalação com um clique de pacotes, como WordPress, Joomla e Drupal.

- **WebJobs** - [Execute qualquer programa ou script](../app-service-web/web-sites-create-web-jobs.md) em VMs do Serviço de Aplicativo. Execute trabalhos continuamente, de acordo com uma agenda ou disparados por eventos. O SDK do Azure [WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) simplifica o código que você escreve para integrá-lo a outros serviços do Azure e de terceiros.

- **Conexões híbridas** - Acesse dados locais usando [Conexões híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e as [Redes Virtuais do Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integração com o Visual Studio** - As ferramentas dedicadas no Visual Studio simplificam o trabalho de criação, implantação, consumo, depuração e gerenciamento de aplicativos Web, aplicativos móveis e aplicativos de API.

## Introdução ao Serviço de Aplicativo

[Crie um aplicativo Web, aplicativo móvel ou aplicativo lógico temporário](http://go.microsoft.com/fwlink/?LinkId=523751) imediatamente e de graça, sem a necessidade de cartão de crédito, sem compromissos ou dificuldades.

Ou abra uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/) e experimente um de nossos tutoriais de introdução:

* [Aplicativos Web](https://azure.microsoft.com/documentation/services/app-service/web/)
* [Aplicativos Móveis](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [Aplicativos de API](https://azure.microsoft.com/documentation/services/app-service/api/)
* [Aplicativos Lógicos](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->