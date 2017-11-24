---
title: "Como adicionar uma origem do evento do Hub IoT ao ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Este artigo descreve como adicionar uma origem do evento que está conectada a um Hub IoT ao ambiente do Time Series Insights"
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: ed31a0e725d1e0863e9c4695d4eccb324f60678a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Como adicionar uma origem do evento do Hub IoT ao ambiente do Time Series Insights
Este tutorial explica como usar o portal do Azure para adicionar uma origem do evento que leia de um Hub IoT para o ambiente do Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos
- Crie um ambiente do Time Series Insights. Para saber mais, veja [Criar um ambiente do Azure Time Series Insights](time-series-insights-get-started.md) 
- Crie um Hub IoT. Para saber mais sobre Hubs IoT, veja [Criar um hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md)
- O Hub IoT precisa ter eventos de mensagem ativos enviados.
- Crie um grupo de consumidores exclusivo no Hub IoT para o ambiente do Time Series Insight de onde você consumirá. Cada origem do evento do Time Series Insights precisa ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com outros consumidores. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Para obter detalhes, confira o [guia de desenvolvedor do Hub IoT](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize o ambiente de Time Series Insights. Clique em **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

3. No título **Topologia do Ambiente**, clique em **Origens do Evento**.
   ![Origens de Evento + Adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Clique em **+ Adicionar**.

5. Forneça um **Nome de origem do evento** exclusivo para esse ambiente do Time Series Insights, como **event-stream**.

   ![Preencha os três primeiros parâmetros no formulário.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Selecione a **Origem** como **Hub IoT**.

7. Selecione a **opção de Importação** adequada. 
   - Escolha **Usar o Hub IoT de assinaturas disponíveis** quando você já tiver um Hub IoT existente em uma das suas assinaturas. Essa é a abordagem mais fácil.
   - Escolha **Fornecer configurações do Hub IoT manualmente** quando o Hub IoT for externo para suas assinaturas, ou se você deseja escolher as opções avançadas. 

8. Se você tiver selecionado a opção **Usar o Hub IoT de assinaturas disponíveis**, a tabela a seguir explica cada propriedade necessária:

   ![Detalhes da assinatura e do Hub de Eventos](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Propriedade | Descrição |
   | --- | --- |
   | ID da assinatura | Selecione a assinatura na qual esse Hub IoT foi criado.
   | Nome do Hub IoT | Selecione o nome do Hub IoT.
   | Nome da política do Hub IoT | Selecione a política de acesso compartilhado, que pode ser encontrada na guia de configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**.
   | Chave de política do Hub IoT | A chave é preenchida previamente.
   | Grupo de consumidores do Hub IoT | O grupo de consumidores para ler eventos do Hub IoT. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização do evento | O JSON é a única serialização disponível no momento. As mensagens de evento deverão estar no formato a seguir, ou nenhum dado poderá ser lido. |
   | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o Hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Quando deixado em branco, o **tempo de enfileiramento do evento** na origem do evento é usado como o carimbo de data/hora do evento. |

9. Se você tiver selecionado a opção **Fornecer configurações do Hub IoT manualmente**, a tabela a seguir explica cada propriedade necessária:

   | Propriedade | Descrição |
   | --- | --- |
   | ID da assinatura | A assinatura na qual esse Hub IoT foi criado.
   | Grupo de recursos | O nome do grupo de recursos no qual este hub IoT foi criado.
   | Nome do Hub IoT | O nome do seu Hub IoT. Quando você criou o Hub IoT, também deu a ele um nome específico.
   | Nome da política do Hub IoT | A política de acesso compartilhado, que pode ser criada na guia de configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**.
   | Chave de política do Hub IoT | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Digite aqui a chave primária ou secundária.
   | Grupo de consumidores do Hub IoT | O grupo de consumidores para ler eventos do Hub IoT. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização do evento | O JSON é a única serialização disponível no momento. As mensagens de evento deverão estar no formato a seguir, ou nenhum dado poderá ser lido. |
   | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o Hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Quando deixado em branco, o **tempo de enfileiramento do evento** na origem do evento é usado como o carimbo de data/hora do evento. |

10. Selecione **Criar** para adicionar a nova origem do evento.

   ![Clicar em Criar](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Após a criação da origem de evento, a Análise de Séries Temporais iniciará automaticamente o fluxo de dados no seu ambiente.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT
Os grupos de consumidores são utilizados pelos aplicativos para obter dados por pull de Hubs IoT do Azure. Forneça um grupo de consumidores, exclusivo para esse ambiente do Time Series Insights, para ler dados de forma confiável do seu Hub IoT.

Para adicionar um novo grupo de consumidores ao hub IoT, siga estas etapas:
1. No portal do Azure, localize e abra o hub IoT.

2. No título **Mensagens**, selecione **Pontos de extremidade**. 

   ![Adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Selecione o ponto de extremidade **Eventos** e a página **Propriedades** será aberta.

4. Abaixo do título **Grupos de consumidores**, forneça um novo nome exclusivo para o grupo de consumidores. Use esse mesmo nome no ambiente do Time Series Insights ao criar uma nova origem do evento.

5. Selecione **Salvar** para salvar o novo grupo de consumidores.

## <a name="next-steps"></a>Próximas etapas
- [Defina as políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.
- [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
- Acesse seu ambiente no [explorer do Time Series Insights](https://insights.timeseries.azure.com).
