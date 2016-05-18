<properties
   pageTitle="Usando o Codificador JSON do BizTalk em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o Conector do Codificador JSON do BizTalk ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Introdução ao Codificador JSON do BizTalk e à adição dele ao seu Aplicativo Lógico 

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


O conector de Codificação e Decodificação do BizTalk JSON ajuda na interoperabilidade do aplicativo entre dados JSON e XML. Pode converter uma determinada instância JSON para XML e vice-versa.

Você pode adicionar o Codificador JSON do BizTalk a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

## Usando o codificador JSON do BizTalk
Para usar o Codificador de Arquivo Simples JSON, primeiro você precisa criar uma instância do aplicativo de API do Codificador de Arquivo Simples JSON. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do Codificador JSON do BizTalk no Azure Marketplace.

## Usando o Codificador JSON do BizTalk na superfície do designer de Aplicativos Lógicos
Siga as etapas em [Criando um Aplicativo Lógico]. O Codificador JSON do BizTalk pode ser usado como uma ação. Ele não tem gatilhos.

### Ação
- Clique no Codificador JSON do BizTalk no painel à direita

	![Configurações de ação][3]
- Clique em ->

	![Lista de ações][4]
- O codificador JSON do BizTalk oferece suporte a duas ações. Selecione *Xml para JSON*

	![Entrada XML para JSON][5]
- Fornecer as entradas para a ação e configurá-la

	![Codificar e enviar configurado][6]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
XML de entrada|objeto|Conteúdo do Xml de entrada
Remover Envelope externo|string|Sinalizador definido para remover o nó raiz do conteúdo Xml

A ação retorna uma representação json do conteúdo da entrada.

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Criando um Aplicativo Lógico]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0420_2016-->