<properties 
   pageTitle="Usando o Conector de Espera em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure" 
   description="Como criar e configurar o Conector de Espera ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure" 
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
   ms.date="02/10/2016"
   ms.author="rajram"/>

# Comece a usar o Conector de Espera e adicione-o ao seu Aplicativo Lógico
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

O conector de espera permite que um aplicativo atrase a execução por um período especificado ou até um horário especificado. Você pode adicionar o Conector de Espera ao seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico. Quando usado em um Aplicativo Lógico, ele pode ser usado para atrasar a execução.

## Usando o conector de espera
Para usar o conector de espera, primeiro você precisa criar uma instância do aplicativo de API do conector de espera. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do conector de espera do Azure Marketplace.

## Usando o conector de espera na superfície do designer de aplicativos lógicos
O conector de espera pode ser usado como uma ação. Ele não tem gatilhos.

### Ação
- Clique no Conector de Espera no painel à direita:
![Lista de ações][1]
- O conector de espera dá suporte a duas ações: 
	- Atrasar
	- Atrasar até
	 
- Selecione *Atrasar*.
![Entrada de atraso][2]
- Fornecer as entradas para a ação e configurá-la:
![Ação configurada][3]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
Duração em minutos|inteiro|Duração do atraso em minutos


## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0413_2016-->