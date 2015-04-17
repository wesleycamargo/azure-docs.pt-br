<properties 
	pageTitle="O que são Conectores e Aplicativos de API do BizTalk" 
	description="Saiba mais sobre Aplicativos de API, Conectores e Aplicativos de API do BizTalk" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# O que são Conectores e Aplicativos de API do BizTalk

O produto de Serviços de Aplicativos do Azure é construído sobre um princípio de extensibilidade e conectividade comuns entre os Aplicativos da API.  Um *Connector* é um tipo de Aplicativo de API que se concentra na conectividade.  Conectores, como qualquer outro Aplicativo de API, são utilizados de aplicativos Web, aplicativos móveis e aplicativos lógicos.  Os conectores tornam fácil conectar-se a serviços existentes e ajudam a gerenciar a autenticação, fornecem monitoramento, análise e muito mais.

Qualquer desenvolvedor pode criar seus próprios Aplicativos de API e implantá-los de maneira privada, e, no futuro, poderá compartilhar e comercializá-los por meio do marketplace. 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

Para agilizar os desenvolvedores que criam soluções com Serviços de Aplicativos do Azure, a equipe do Microsoft Azure adicionou vários conectores ao marketplace para atender a vários cenários comuns.  Além disso, para estender o alcance dos Serviços de Aplicativos para cenários de integração complexos e avançados, uma série de recursos Premium e BizTalk também está disponível.

[Lista de Aplicativos de API e Conectores no Serviço de Aplicativo do Microsoft Azure](app-service-logic-connectors-list.md)


## Conectores de protocolo
O Serviços de Aplicativos nasceu da Web e favorece a comunicação usando HTTP e compartilhamento de metadados por meio de formatos abertos, como o Swagger.  Obviamente, as empresas precisam se comunicar através de uma variedade de protocolos.  Os conectores de protocolo podem preencher a lacuna e tornar a comunicação com serviços que usam os serviços FTP, SFTP, POP3/IMAP, SMTP e SOAP tão fácil quanto efetuar uma chamada HTTP.  

[Conectores de protocolo no Serviço de Aplicativo do Microsoft Azure](app-service-logic-protocol-connectors.md)


## Conectores SaaS
Conectores SaaS do Serviços de Aplicativos proporcionam uma maneira completa de conectar aplicativos Web, móveis e lógicos a alguns dos principais nomes em SaaS atuais, incluindo Office 365, SalesForce, Sugar CRM, OneDrive, DropBox, Marketo, inclusive Facebook e muito mais.

[Conectores sociais no Serviço de Aplicativo do Microsoft Azure](app-service-logic-social-connectors.md)

[Aplicativo + Conectores de Serviços de Dados no Serviço de Aplicativo do Microsoft Azure](app-service-logic-data-connectors.md)


## Conectores Premium 
Os conectores Premium estendem o alcance de Serviços de Aplicativos ainda mais na empresa, com conectividade para SAP, Siebel, Oracle, DB2 e muito mais.

[Conectores empresariais no Serviço de Aplicativo do Microsoft Azure](app-service-logic-enterprise-connectors.md)


## Aplicativos de API do BizTalk
A criação de aplicativos críticos de negócios exige mais do que apenas a conectividade.  Baseado na base da plataforma de integração líder do setor da Microsoft - BizTalk Server - os aplicativos de API do BizTalk fornecem recursos de integração avançada que podem ser ajustados em aplicativos Web, móveis e de lógica com facilidade.  Inclui a criação de lote e a desmontagem de lote, VETR (validar, extrair, transformar e encaminhar) e suporte para formatos EDI como X12, EDIFACT e AS2.

[Conectores B2B e aplicativos de API no Serviço de Aplicativo do Microsoft Azure](app-service-logic-b2b-connectors.md)<br/>

[Integração de aplicativos de API do BizTalk no Serviço de Aplicativo do Microsoft Azure](app-service-logic-integration-connectors.md)

<!--HONumber=49-->