---
title: "Como adicionar uma origem do evento do Hub de Eventos ao ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Este artigo descreve como adicionar uma origem do evento que está conectada a um Hub de Eventos ao ambiente do Time Series Insights."
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
ms.openlocfilehash: f3a9a1c7e57383925877f674a2e02f931e5c1e3c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Como adicionar uma origem do evento do Hub de Eventos ao ambiente do Time Series Insights

Este tutorial explica como usar o portal do Azure para adicionar uma origem do evento que leia de um Hub de Eventos para o ambiente do Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos
- Crie um ambiente do Time Series Insights. Para saber mais, veja [Criar um ambiente do Azure Time Series Insights](time-series-insights-get-started.md) 
- Criar um Hub de Eventos. Para saber mais sobre os Hubs de Eventos, consulte [Criar um namespace do Hubs de Eventos e um hub de eventos usando o Portal do Azure](../event-hubs/event-hubs-create.md)
- O Hub de Eventos precisa ter eventos de mensagem ativos enviados. Para saber mais, confira [Enviar eventos para Hubs de Eventos do Azure usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores exclusivo no Hub de Eventos para o ambiente do Time Series Insight de onde você consumirá. Cada origem do evento do Time Series Insights precisa ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com outros consumidores. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Observe que também há um limite de 20 grupos de consumidores por Hub de Eventos. Para obter detalhes, confira o [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize seu ambiente Time Series Insights existente. Clique em **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

3. No título **Topologia do Ambiente**, clique em **Origens do Evento**.

   ![Origens de Evento + Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Clique em **+ Adicionar**.

5. Forneça um **Nome de origem do evento** exclusivo para esse ambiente do Time Series Insights, como **event-stream**.

   ![Preencha os três primeiros parâmetros no formulário.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Selecione a **Origem** como **Hub de Eventos**.

7. Selecione a **opção de Importação** adequada. 
   - Escolha **Usar o Hub de Eventos de assinaturas disponíveis** quando você já tiver um Hub de Eventos existente em uma das suas assinaturas. Essa é a abordagem mais fácil.
   - Escolha **Fornecer configurações do Hub de Eventos manualmente** quando o Hub de Eventos for externo para suas assinaturas, ou se você deseja escolher as opções avançadas. 

8. Se você tiver selecionado a opção **Usar o Hub de Eventos de assinaturas disponíveis**, a tabela a seguir explica cada propriedade necessária:

   ![Detalhes da assinatura e do Hub de Eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Propriedade | Descrição |
   | --- | --- |
   | ID da assinatura | Selecione a assinatura na qual esse hub de eventos foi criado.
   | Namespace do Barramento de Serviço | Selecione o namespace do Barramento de Serviço que contém o Hub de Eventos.
   | Nome do Hub de Eventos | Selecione o nome do Hub de Eventos.
   | Nome da política do hub de eventos | Selecione a política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**.
   | Chave de política do hub de eventos | O valor da chave pode ser preenchido previamente.
   | Grupo de consumidores do hub de eventos | O grupo de consumidores para ler eventos do Hub de Eventos. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento. |
   | Formato de serialização do evento | O JSON é a única serialização disponível no momento. As mensagens de evento deverão estar no formato a seguir, ou nenhum dado poderá ser lido. |
   | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o Hub de Eventos. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Quando deixado em branco, o **tempo de enfileiramento do evento** na origem do evento é usado como o carimbo de data/hora do evento. |


9. Se você tiver selecionado a opção **Fornecer configurações do Hub de Eventos manualmente**, a tabela a seguir explica cada propriedade necessária:

   | Propriedade | Descrição |
   | --- | --- |
   | ID da assinatura | A assinatura na qual esse hub de eventos foi criado.
   | Grupo de recursos | O grupo de recursos no qual este hub de eventos foi criado.
   | Namespace do Barramento de Serviço | Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço.
   | Nome do Hub de Eventos | O nome do seu Hub de Eventos. Quando você criou o hub de eventos, também deu a ele um nome específico.
   | Nome da política do hub de eventos | A política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura**.
   | Chave de política do hub de eventos | A chave de Acesso Compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. Digite aqui a chave primária ou secundária.
   | Grupo de consumidores do hub de eventos | O Grupo de Consumidores para ler eventos do Hub de Eventos. É altamente recomendável usar um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização do evento | O JSON é a única serialização disponível no momento. As mensagens de evento deverão estar no formato a seguir, ou nenhum dado poderá ser lido. |
   | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o Hub de Eventos. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Quando deixado em branco, o **tempo de enfileiramento do evento** na origem do evento é usado como o carimbo de data/hora do evento. |


10. Selecione **Criar** para adicionar a nova origem do evento.
   
   ![Clicar em Criar](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Após a criação da origem de evento, a Análise de Séries Temporais iniciará automaticamente o fluxo de dados no seu ambiente.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao Hub de Eventos
Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hubs de Eventos do Azure. Forneça um grupo de consumidores, exclusivo para esse ambiente do Time Series Insights, para ler dados de forma confiável do seu Hub de Eventos.

Para adicionar um novo grupo de consumidores ao Hub de Eventos, execute estas etapas:
1. No portal do Azure, localize e abra o Hub de Eventos.

2. Sob o cabeçalho **Entidades**, selecione **Grupos de consumidores**.

   ![Hub de Eventos - Adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

4. Na página **Grupos de consumidores**, forneça um novo **Nome** exclusivo.  Use esse mesmo nome no ambiente do Time Series Insights ao criar uma nova origem do evento.

5. Selecione **Criar** para criar o novo grupo de consumidores.

## <a name="next-steps"></a>Próximas etapas
- [Definir as políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.
- [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
- Acesse seu ambiente no [explorer do Time Series Insights](https://insights.timeseries.azure.com).
