<properties 
   pageTitle="Rastrear mensagens B2B" 
   description="Este tópico aborda o acompanhamento do processamento B2B" 
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
   ms.date="02/27/2015"
   ms.author="hariag"/>


# Rastrear mensagens B2B

## Informações de acompanhamento B2B
A comunicação B2B envolve processamento de mensagens entre parceiros comerciais. As relações são definidas como contratos entre dois parceiros comerciais. Quando a comunicação é estabelecida, precisa haver uma maneira de monitorar se a comunicação acontece conforme o esperado. Como parte de trazer Aplicativos de API B2B como parte dos Serviços de Aplicativos do Azure, habilitamos dados de rastreamento e os mesmos também são trazidos à tona por meio do Portal do Azure.

## AS2
Depois de ter criado uma instância de um aplicativo de API AS2, navegue até essa instância e vá para a parte de Acompanhamento. Aqui, é possível exibir todas as informações de acompanhamento AS2 e também filtrá-las por meio das folhas de filtro que aparecem.

![][1]

## EDIFACT
Depois de ter criado uma instância de um aplicativo de API EDIFACT, navegue até essa instância e vá para a parte de Acompanhamento. Aqui, é possível exibir todas as informações de acompanhamento EDIFACT e também filtrá-las por meio de filtros surgidos. Além disso, é possível exibir os dados de nível de intercâmbio, dados de nível de grupo e dados de nível de conjunto de transações conforme se entra na visualização.

Se os lotes tiverem sido criados como parte de acordos EDIFACT no aplicativo de API de Gerenciamento do parceiro comercial associado, a parte do envio em lote listará todos esses lotes. Seria possível entrar em um lote para ver a mensagem que constitui a mensagem ativa (se houver) e também as informações de lotes concluídos no passado.

![][2]

## X12
Depois de ter criado uma instância de um aplicativo de API X12, navegue até essa instância e vá para a parte de Acompanhamento. Aqui, é possível exibir todas as informações de acompanhamento X12 e também filtrá-las por meio dos filtros que aparecem. Além disso, é possível exibir os dados de nível de intercâmbio, dados de nível de grupo e dados de nível de conjunto de transações conforme se entra na visualização.

Se os lotes tiverem sido criados como parte de acordos X12 no aplicativo de API de Gerenciamento do parceiro comercial associado, a parte do envio em lote listará todos esses lotes. Seria possível entrar em um lote para ver a mensagem que constitui a mensagem ativa (se houver) e também as informações de lotes concluídos no passado.

X12 e EDIFACT têm visualizações de acompanhamento similares.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!---HONumber=62-->