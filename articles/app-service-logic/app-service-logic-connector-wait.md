<properties 
   pageTitle="Conector de espera" 
   description="Conector de espera" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Conector de espera
O conector de espera permite que um aplicativo atrase a execução por um período especificado ou até um horário especificado. Em um fluxo, ele pode ser usado para atrasar a execução.

##Usando o conector de espera
Para usar o conector de espera, primeiro você precisa criar uma instância do aplicativo de API do conector de espera. Isso pode ser feito de forma embutida durante a criação de um aplicativo lógico ou com a seleção do aplicativo de API do conector de espera do Azure Marketplace.

##Usando o conector de espera na superfície do designer de aplicativos lógicos
O conector de espera pode ser usado como uma ação. Ele não tem gatilhos.

###Ação
- Clique no conector de espera no painel à direita.

	![Lista de ações][1]
- O conector de espera dá suporte a duas ações: 
	- Atrasar
	- Atrasar até
	 
- Selecione *Atrasar*.

	![Entrada de atraso][2]
- Especifique as entradas para a ação e configure-a.

	![Ação configurada][3]

Parâmetro|Tipo|Descrição do parâmetro
---|---|---
Duração em minutos|inteiro|Duração do atraso em minutos


## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e Monitorar aplicativos de API e conector](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=August15_HO6-->