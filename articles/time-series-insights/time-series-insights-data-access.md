---
title: Configurar a segurança para acessar e gerenciar o Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a segurança e as permissões como políticas de gerenciamento de acesso e políticas de acesso de dados para proteger o Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364792"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Conceder acesso a dados em um ambiente de Análise de Séries Temporais usando o portal do Azure

Os ambientes de Análise de Séries Temporais possuem dois tipos independentes de políticas de acesso:

* Políticas de acesso de gerenciamento
* Políticas de acesso de dados

Os dois tipos de políticas concedem às entidades de segurança (usuários e aplicativos) do Azure Active Directory várias permissões em um ambiente específico. As entidades de segurança (usuários e aplicativos) devem pertencer ao active directory (conhecido como locatário do Azure) associado à assinatura que contém o ambiente.

As políticas de acesso de gerenciamento concedem permissões relacionadas à configuração do ambiente, como
*   Criação e exclusão do ambiente, origens de evento, conjuntos de dados de referência e
*   Gerenciamento das políticas de acesso de dados.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e salvar consultas compartilhadas e perspectivas associadas ao ambiente.

Os dois tipos de políticas permitem uma separação clara entre o acesso ao gerenciamento do ambiente e o acesso aos dados dentro do ambiente. Por exemplo, é possível configurar um ambiente de modo que o proprietário/criador do ambiente seja removido do acesso aos dados. Além disso, usuários e serviços que têm permissão para ler dados do ambiente podem não receber permissão de acesso à configuração do ambiente.

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>Próximas etapas

* Saiba [como adicionar uma fonte de evento do Hub de Eventos ao ambiente do Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Exibir seu ambiente no [explorador do Time Series Insights](https://insights.timeseries.azure.com).
