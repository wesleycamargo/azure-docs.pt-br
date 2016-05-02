<properties 
   pageTitle="Arquivar mensagens do Conector AS2 | Serviço de Aplicativo do Microsoft Azure" 
   description="Como arquivar ou armazenar mensagens do Conector AS2 no Serviço de Aplicativo do Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Visão geral de arquivo de mensagens do Conector AS2


[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

O [Conector AS2](app-service-logic-connector-as2.md) expõe a capacidade de arquivar mensagens. O arquivamento armazena a mensagem no **Contêiner de Blob do Azure**, que faz parte das configurações do pacote.

O arquivamento é exposto em dois pontos, para mensagens e confirmações (MDNs):

1. **Gatilho de recebimento/decodificação**: a mensagem é arquivada assim que é recebida pela instância do aplicativo de API 
2. **Ação de codificação/envio**: a mensagem codificada é arquivada após todo o processamento ser concluído e antes de ser enviada ao parceiro 

## Como: recuperar a URL arquivada da mensagem

Navegue até a instância do aplicativo de API do conector AS2 e clique em “Rastrear”. Restrinja as informações de rastreamento usando os parâmetros de filtro. Quando a mensagem aparecer, clique para ver a exibição detalhada. A URL arquivada para a mensagem é exibida neste modo de exibição detalhado: ![][1]

## Como: recuperar uma mensagem arquivada

Use a URL recuperada acima para recuperar a mensagem arquivada do Armazenamento de Blob do Azure.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=AcomDC_0420_2016-->