---
title: Estados do LiveEvent e faturamento no Azure Media Services|Microsoft Docs
description: Este tópico fornece uma visão geral dos estados e faturamento do LiveEvent dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585365"
---
# <a name="liveevent-states-and-billing"></a>Estados de LiveEvent e cobrança

Nos Serviços de Mídia do Azure, um LiveEvent começa o faturamento assim que seu estado faz a transição para **Running**. Para interromper o evento ao vivo do faturamento, você precisa interromper o evento ao vivo.

Quando **LiveEventEncodingType** no [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) estiver definido como Standard (Básico), o Serviços de Mídia do Azure desligará automaticamente qualquer LiveEvent que ainda esteja no estado **Em Execução** 12 horas após a entrada o feed é perdido e não há **LiveOutput** em execução. No entanto, você ainda será cobrado pela LiveEvent estava em vez do estado **executando**.

## <a name="states"></a>Estados

O LiveEvent pode estar em um dos seguintes estados.

|Estado|DESCRIÇÃO|
|---|---|
|**Interrompido**| Este é o estado inicial do LiveEvent após a criação (a menos que a autoinicialização tenha sido definida como true.) Não há cobrança nesse estado. Nesse estado, as propriedades do LiveEvent podem ser atualizadas, mas streaming não é permitido.|
|**Iniciando**| O LiveEvent está sendo iniciado e os recursos estão sendo alocados. Não há cobrança nesse estado. Atualizações ou streaming não são permitidos durante esse estado. Se ocorrer um erro, o LiveEvent retornará para o estado Parado.|
|**Executando**| Os recursos do LiveEvent foram alocados, as URLs de entrada e visualização foram geradas e é capaz de receber transmissões ao vivo. Neste ponto, o faturamento está ativo. Você deve chamar explicitamente Parar no recurso LiveEvent para interromper o faturamento adicional.|
|**Parando**| O LiveEvent está sendo interrompido e os recursos estão sendo desprovisionados. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|
|**Excluindo**| O LiveEvent está sendo excluído. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)
