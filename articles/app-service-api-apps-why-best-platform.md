<properties 
	pageTitle="O que são Aplicativos de API?" 
	description="Saiba por que o Serviço de Aplicativo do Azure é a melhor plataforma para desenvolver, publicar e hospedar APIs RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tdykstra"/>

# O que são Aplicativos de API?

## Visão geral

O Serviço de Aplicativo do Azure é uma plataforma de computação totalmente gerenciada para desenvolvedores profissionais que oferece um conjunto avançado de recursos para cenários Web, móveis e de integração.  Os aplicativos de API fazem parte do Serviço de Aplicativo e possibilitam que qualquer usuário técnico ou desenvolvedor descubra, hospede, gerencie e monetize conectores de API e SaaS em uma plataforma de nuvem moderna, cheia de recursos, escalonável e globalmente disponível.

## Por que aplicativos de API?

Os aplicativos de API do Serviço de Aplicativo do Azure facilitam o desenvolvimento, publicação, gerenciamento e monetização de APIs.

- **Traga sua API como ela é** - Use ASP.NET, Java, PHP, Node.js ou Python para suas APIs.  Suas APIs podem tirar proveito dos recursos do Serviço de Aplicativo do Azure sem que seja necessário fazer alterações.

- **Conecte seus aplicativos a plataformas populares de SaaS** - O Serviço de Aplicativo do Azure facilita a conexão com plataformas de SaaS populares, que incluem Salesforce, Office 365, Twitter, Facebook, Dropbox e muitas outras.

- **Controle de acesso simples** - As APIs podem expostas apenas a outros aplicativos dentro do seu Serviço de Aplicativo do Azure ou ao público, e você pode adicionar a autenticação pelo Active Directory do Azure ou por serviços de terceiros sem fazer alterações em seu código.

- **Galeria de APIs pública e corporativa** - Compartilhe facilmente APIs com outras equipes da sua organização usando sua própria galeria corporativa privada de APIs.  As APIs também podem ser compartilhadas publicamente para consumo por outros desenvolvedores.

- **Geração automática de SDK** - O Serviço de Aplicativo do Azure pode compilar automaticamente SDKs para várias linguagens, incluindo C#, Java e Javascript, tornando suas APIs disponíveis para várias plataformas.

- **Use o melhor IDE do mercado** - A integração com o [Visual
Studio](/campaigns/visual-studio-2013/) simplifica a criação, depuração, empacotamento e publicação das APIs e permite o gerenciamento do ciclo de vida completo do aplicativo.

<!--removendo por orientação para reduzir o tamanho desta seção
- **Sem ops** - Execute seus aplicativos de API em um ambiente de alta disponibilidade, com aplicação automática de patch.  Aplicativos de API implantados com o Serviço de Aplicativo do Azure são isolados e hospedados em máquinas virtuais dedicadas aos seus aplicativos, garantindo isolamento de segurança e desempenho previsível.

- **Dimensione automaticamente** - o Serviço de Aplicativo do Azure permite que você escale verticalmente ou horizontalmente rapidamente a escala, para lidar com qualquer demanda de carga dos clientes.  Selecione manualmente o número e tamanho das máquinas virtuais ou configure o
[dimensionamento automático](/documentation/videos/auto-scaling-azure-web-sites/)
para ajustar seus servidores com base na carga ou cronograma.
-->

- **Acesse dados locais** - Os aplicativos de API podem consumir dados de seu próprio data center usando [conexões híbridas](integration-hybrid-connection-overview.md) e [VNET](web-sites-integrate-with-vnet.md).

- **Implantação descomplicada** - Configure fluxos de trabalho de implantação e integração contínua com o Visual Studio Online, GitHub, TeamCity, Hudson ou BitBucket, permitindo que você compile, teste e implante automaticamente seu aplicativo de API a cada teste de integração ou verificação de código bem-sucedida.

## Conceitos dos Aplicativos de API ##

- **Galeria de Aplicativos de API** - Selecione dentre uma lista sempre crescente de modelos de aplicativos de API existentes.
- **Conectores** - Um tipo de Aplicativo de API que facilita a conexão a plataformas de SaaS como Salesforce e Office 365.
- **Gateway** - Um aplicativo Web que lida com funções de gerenciamento de API, como a autenticação para todos os aplicativos de API em um grupo de recursos. 
- **Dimensionamento automático** - Os aplicativos de API podem ter sua escala ampliada ou reduzida automaticamente para lidar com qualquer demanda de carga dos clientes.  Selecione manualmente o número e tamanho das máquinas virtuais ou configure o dimensionamento automático para ajustar seus servidores com base na carga ou cronograma.
- **Integração contínua** - Configure fluxos de trabalho de implantação e integração contínua com o VSO, GitHub, TeamCity, Hudson ou BitBucket, permitindo que você compile, teste e implante automaticamente seu aplicativo Web a cada teste de integração ou verificação de código bem-sucedida.

## Introdução

Para começar a usar os aplicativos de API, execute o tutorial [Criar um aplicativo de API](app-service-dotnet-create-api-app.md).

Para saber mais sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](app-service-value-prop-what-is.md).

<!--HONumber=49-->