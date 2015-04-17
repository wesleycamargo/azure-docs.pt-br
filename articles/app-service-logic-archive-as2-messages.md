<properties 
   pageTitle="Arquivar mensagens AS2" 
   description="Este tópico aborda o arquivamento de mensagens AS2" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="hariag"/>


# Arquivar mensagens AS2

O **Conector AS2** tem o recurso de arquivar mensagens.  O arquivamento armazena a mensagem no **Contêiner de Blob do Azure**, que faz parte das configurações do pacote. 

O arquivamento é exposto em dois pontos, para mensagens e confirmações (MDNs):

1. **Gatilho de recebimento/decodificação**: a mensagem é arquivada assim que é recebida pela instância do Aplicativo de API 
2. **Ação de codificação/envio**: a mensagem codificada é arquivada após todo o processamento ser concluído e antes de ser enviada ao parceiro 

##Como:  Recuperar a URL arquivada da mensagem

Navegue até a instância do Aplicativo de API do Conector AS2 e clique em 'Tracking'.  Restrinja as informações de acompanhamento usando os parâmetros de filtragem.  Quando a mensagem aparecer, clique para ver sua exibição detalhada.  A URL arquivada da mensagem será exibida nesse modo detalhado.

![][1]  

##Como:  Recuperar uma mensagem arquivada

Use a URL recuperada acima para recuperar a mensagem arquivada do Armazenamento de Blob do Azure.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg

<!--HONumber=49-->