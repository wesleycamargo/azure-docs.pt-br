---
title: Acompanhar mensagens B2B em seus aplicativos lógicos no Serviço de Aplicativo do Azure | Microsoft Docs
description: Este tópico aborda o acompanhamento do processamento B2B
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajram
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 04/20/2016
ms.author: rajram

---
# Rastrear mensagens B2B
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## Informações de acompanhamento B2B
A comunicação B2B envolve processamento de mensagens entre parceiros comerciais. As relações são definidas como contratos entre dois parceiros comerciais. Quando a comunicação é estabelecida, precisa haver uma maneira de monitorar se a comunicação acontece conforme o esperado.

Implementamos o rastreamento de mensagens para os seguintes cenários B2B: AS2, EDIFACT e X12.

## AS2
Depois de ter criado uma instância de um aplicativo de API AS2, navegue até essa instância e selecione **Acompanhamento**. Aqui você poderá exibir e filtrar todas as informações de acompanhamento AS2:

![][1]

## EDIFACT
Depois de ter criado uma instância de um aplicativo de API EDIFACT, navegue até essa instância e selecione **Acompanhamento**. Aqui você poderá exibir e filtrar todas as informações de acompanhamento EDIFACT. Além disso, é possível exibir os dados de nível de intercâmbio, dados de nível de grupo e dados de nível de conjunto de transações conforme em uma única visualização.

Se os lotes tiverem sido criados como parte de acordos EDIFACT no aplicativo de API de Gerenciamento do Parceiro Comercial associado, a seção do Envio em lote lista todos esses lotes. Você pode selecionar um lote para ver a mensagem ativa (se houver) e também as informações para preencher:

![][2]

## X12
Depois de ter criado uma instância de um aplicativo de API X12, navegue até essa instância e selecione **Acompanhamento**. Aqui você pode exibir e filtrar todas as informações de acompanhamento X12. Além disso, é possível exibir os dados de nível de intercâmbio, dados de nível de grupo e dados de nível de conjunto de transações conforme em uma única visualização.

Se os lotes tiverem sido criados como parte de acordos X12 no aplicativo de API de Gerenciamento do Parceiro Comercial associado, a seção do Envio em lote lista todos esses lotes. Você pode selecionar um lote para ver a mensagem ativa (se houver) e também as informações dos lotes concluídos.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!---HONumber=AcomDC_0803_2016-->