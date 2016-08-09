<properties 
	pageTitle="O que são aplicativos de API do BizTalk e conectores" 
	description="Saiba mais sobre Aplicativos de API, Conectores e Aplicativos de API do BizTalk" 
	services="logic-apps" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="07/28/2016" 
	ms.author="mandia"/>

# O que são aplicativos de API do BizTalk e conectores

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Um *Conector* é um tipo de Aplicativo de API que se concentra na conectividade. Conectores, como qualquer outro Aplicativo de API, são utilizados de aplicativos Web, aplicativos móveis e aplicativos lógicos. Os conectores tornam fácil conectar-se a serviços existentes e ajudam a gerenciar a autenticação, fornecem monitoramento, análise e muito mais.

Qualquer desenvolvedor pode criar seus próprios aplicativos de API e implantá-los em particular. No futuro, os desenvolvedores podem compartilhar e monetizar seus aplicativos de API personalizada criada por meio do Marketplace.

![Marketplace de aplicativos de API](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

Para agilizar os desenvolvedores que criam soluções, a equipe do Azure adicionou vários conectores ao marketplace para atender a vários cenários comuns. Além disso, para estender o alcance para cenários de integração complexos e avançados, uma série de recursos Premium e BizTalk também está disponível.

Há diferentes "Camadas" de serviço disponíveis. Todos os níveis incluem todos os conectores e aplicativos de API, incluindo sua funcionalidade completa.

[Preços de Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) descreve esses níveis de serviços e também lista o que está incluído nesses níveis. As seções a seguir descrevem as diversas categorias de aplicativos de API do BizTalk e conectores.


## Conectores híbridos 
Os conectores híbridos estendem ainda mais o alcance na empresa com a conectividade para [SAP](app-service-logic-connector-sap.md), [Oracle](app-service-logic-connector-oracle.md), [DB2](app-service-logic-connector-db2.md), [Informix](app-service-logic-connector-informix.md) e WebSphere MQ.

## Serviços EDI e EAI
A criação de aplicativos críticos para os negócios exige mais do que apenas a conectividade. Baseado na base da plataforma de integração líder do setor da Microsoft - BizTalk Server - os aplicativos de API do BizTalk fornecem recursos de integração avançada que podem ser ajustados em aplicativos Web, móveis e de lógica com facilidade. Alguns desses recursos de integração incluem [Validar](app-service-logic-xml-validator.md), [Extrair](app-service-logic-xpath-extract.md), [Transformar](app-service-logic-transform-xml-documents.md), [Codificadores](app-service-logic-connector-jsonencoder.md), [Gerenciamento de Parceiros Comerciais](app-service-logic-connector-tpm.md) e suporte para formatos EDI como [X12](app-service-logic-connector-x12.md), [EDIFACT](app-service-logic-connector-edifact.md) e [AS2](app-service-logic-connector-as2.md).

Recursos adicionais: [Conectores B2B e aplicativos de API](app-service-logic-b2b-connectors.md) [Criar um processo B2B](app-service-logic-create-a-b2b-process.md) [Criar um contrato de parceiros comerciais](app-service-logic-create-a-trading-partner-agreement.md) [Controlar suas mensagens B2B](app-service-logic-track-b2b-messages.md)


## Regras
As regras de negócios encapsulam as políticas e as decisões que controlam processos de negócios. Normalmente, as regras são dinâmicas e mudam com o tempo por diferentes motivos, incluindo planos de negócios, regulamentos entre muitos outros. [Regras do BizTalk](app-service-logic-use-biztalk-rules.md) permite desassociar essas políticas do código de aplicativo e simplificar e agilizar o processo de alteração.

## Lista de conectores e aplicativos de API
Consulte [Conectores e lista de aplicativos da API](app-service-logic-connectors-list.md) para obter uma lista completa dos conectores e API aplicativos presentes em cada categoria, incluindo conectores padrão, BizTalk EAI, conectores Premium etc.
 

<!---HONumber=AcomDC_0803_2016-->