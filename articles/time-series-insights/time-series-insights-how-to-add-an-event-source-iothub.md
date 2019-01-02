---
title: Adicionar um hub IoT ao Azure Time Series Insights – Como adicionar uma origem do evento de Hub IoT ao Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar uma origem do evento conectada a um Hub IoT ao ambiente do Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: 3e370bd4cebb84d7ee9f607fc6640218ee202bb9
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321264"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem do evento do hub IoT ao seu ambiente do Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem do evento que lê dados de um Hub IoT para o ambiente do Azure Time Series Insights.

> [!NOTE]
> As instruções neste artigo se aplicam tanto aos ambientes da Versão Prévia do Azure Time Series Insights quanto ao Time Series Insights GA.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [ambiente do Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Criar um [Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md).
* O hub IoT precisa ter eventos de mensagem ativos enviados.
* Crie um grupo de consumidores dedicado no hub IoT para o ambiente do Time Series Insights de onde você consumirá. Cada origem do evento do Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com nenhum outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Para obter detalhes, confira o [Guia do desenvolvedor do Hub IoT do Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Aplicativos usam grupos de consumidores para efetuar pull dos dados de Hub IoT do Azure. Forneça um grupo de consumidores dedicado, usado apenas pelo ambiente do Time Series Insights para ler dados de forma confiável de seu hub IoT.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. No portal do Azure, encontre e abra seu hub IoT.

1. No menu, em **Configurações**, selecione **Pontos de extremidade internos** e, em seguida, selecione o ponto de extremidade **Eventos**.

   ![Na página Pontos de Extremidade Internos, selecione o botão Eventos][1]

1. Em **Grupos de consumidores**, insira um nome exclusivo para o grupo de consumidores. Use esse mesmo nome em seu ambiente do Time Series Insights ao criar uma nova origem do evento.

1. Clique em **Salvar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione o seu ambiente de Análise de Séries Temporais.

1. Em **Topologia do Ambiente**, selecione **Origens dos Eventos** e, em seguida, selecione **Adicionar**.

   ![Selecione Fontes de eventos e selecione o botão Adicionar][2]

1. No painel **Nova origem do evento**, em **Nome da origem do evento**, insira um nome que seja exclusivo para esse ambiente do Time Series Insights, como fluxo-de-evento. Por exemplo, digite **fluxo-de-evento**.

1. Para **Origem**, selecione **Hub IoT**.

1. Selecione um valor para **Importar opção**:

   * Se você já tiver um Hub IoT em uma de suas assinaturas, selecione **Usar o Hub IoT de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.
   * Se o hub IoT for externo às suas assinaturas ou se você quiser escolher opções avançadas, selecione **Fornecer configurações do Hub IoT manualmente**.

   ![Selecione as opções no painel Nova origem do evento][3]

1. A tabela a seguir descreve as propriedades necessárias para a opção **Usar Hub IoT de assinaturas disponíveis**:

   ![Painel Nova origem do evento – Propriedades a serem definidas na opção Use o Hub IoT de assinaturas disponíveis][4]

   | Propriedade | DESCRIÇÃO |
   | --- | --- |
   | ID da assinatura | Selecione a assinatura na qual o hub IoT foi criado.
   | Nome do Hub IoT | Selecione o nome do hub IoT.
   | Nome da política do Hub IoT | Selecione a política de acesso compartilhado. Você pode encontrar a política de acesso compartilhado na guia de configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**.
   | Chave de política do Hub IoT | A chave é preenchida previamente.
   | Grupo de consumidores do Hub IoT | O grupo de consumidores que lê eventos do hub IoT. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento deverão estar no formato a seguir ou nenhum dado poderá ser lido. |
   | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |

1. A tabela a seguir descreve as propriedades necessárias para **Fornecer configurações do Hub IoT manualmente**:

   | Propriedade | DESCRIÇÃO |
   | --- | --- |
   | ID da assinatura | A assinatura na qual o hub IoT foi criado.
   | Grupo de recursos | O nome do grupo de recursos no qual o hub IoT foi criado.
   | Nome do Hub IoT | O nome de seu hub IoT. Quando criou o hub IoT, você inseriu um nome para ele.
   | Nome da política do Hub IoT | A política de acesso compartilhado. Você pode criar a política de acesso compartilhado na guia de configurações do hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**.
   | Chave de política do Hub IoT | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço do Azure. Insira aqui a chave primária ou secundária.
   | Grupo de consumidores do Hub IoT | O grupo de consumidores que lê eventos do hub IoT. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento deverão estar no formato a seguir ou nenhum dado poderá ser lido. |
   | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |

1. Adicione o nome do grupo de consumidores do Time Series Insights dedicado que você adicionou ao seu hub IoT.

1. Selecione **Criar**.

   ![Botão Criar][5]

1. Depois que a origem do evento é criada, o Time Series Insights inicia automaticamente os dados de streaming para o seu ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Definir as políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Acesse seu ambiente no [explorer do Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png