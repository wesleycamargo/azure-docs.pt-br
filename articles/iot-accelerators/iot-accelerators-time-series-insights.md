---
title: Visualizar Dados de Monitoramento Remoto com o Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar um ambiente do Time Series Insights para explorar e analisar os dados de série temporal de sua solução de Monitoramento Remoto.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 10617c129212d8196897af750c02647f0086c8e5
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185883"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Visualizar Dados de Monitoramento Remoto com o Time Series Insights

Um operador pode desejar estender ainda mais a visualização de dados imediata fornecida pela solução de Monitoramento Remoto pré-configurada. Nosso acelerador de solução fornece integração com TSI imediata. Com estas instruções você aprenderá como configurar o Time Series Insights para analisar a telemetria do dispositivo e detectar anomalias.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estas instruções, você precisará do seguinte:

* [Implantar a solução de Monitoramento Remoto pré-configurada](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Você precisará criar um grupo de consumidores exclusivo em seu hub IoT a ser usado para a transmissão de dados para o Time Series Insights.

> [!NOTE]
> Os grupos de consumidores são utilizados pelas aplicações para obter dados do Hub IoT do Azure. Cada grupo de consumidores permite até cinco consumidores de saída. Você deve criar um novo grupo de consumidores para cada cinco coletores de saída, e cada grupo pode ter até 32 consumidores.

1. No Portal do Azure, clique no botão do Cloud Shell.

1. Execute o comando a seguir para criar um novo grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Crie um novo ambiente do Time Series Insights

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização para o gerenciamento de dados de série temporal em escala IoT na nuvem. Ele oferece armazenamento de dados de série temporal massivamente escalonável e permite explorar e analisar bilhões de eventos transmitidos por streaming em todo o mundo em questão de segundos. Use o Time Series Insights para armazenar e gerenciar terabytes de dados de série temporal, explorar e visualizar bilhões de eventos simultaneamente, conduzir análises de causa raiz e comparar diversos sites e ativos.

1. Entre no [Portal do Azure](http://portal.azure.com/).

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Time Series Insights**.

    ![Novo Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Para criar seu ambiente do Time Series Insights, use os valores da tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do ambiente | A captura de tela a seguir usa o nome **contorosrmtsi**. Ao concluir esta etapa, escolha seu próprio nome exclusivo. |
    | Assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Grupo de recursos | **Criar novo**. Estamos usando o nome **ContosoRM**. |
    | Localização | Estamos usando **Leste dos EUA**. Crie seu ambiente na mesma região que sua solução de Monitoramento Remoto. |
    | Sku |**S1** |
    | Capacity | **1** |
    | Fixar no painel | **Sim** |

    ![Criar Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Clique em **Criar**. Pode levar alguns instantes para o ambiente a ser criado.

## <a name="create-event-source"></a>Criar Origem do Evento

Crie uma nova Origem do Evento para se conectar ao hub IoT. Use o grupo de consumidores criado nas etapas anteriores. O Time Series Insights requer que cada serviço tenha um grupo de consumidores exclusivo que não esteja em uso por outro serviço.

1. Navegue até o novo Ambiente do Time Series.

1. À esquerda, selecione **Origens do Evento**.

    ![Exibir Origens do Evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**.

    ![Adicionar Origem do Evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar seu hub IoT como uma nova origem do evento, use os valores da tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da Origem do Evento | A captura de tela a seguir usa o nome **contosorm-iot-hub**. Ao concluir esta etapa, use seu próprio nome exclusivo. |
    | Fonte | **Hub IoT** |
    | Importar opção | **Usar o Hub IoT nas assinaturas disponíveis** |
    | ID da assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Nome do Hub IoT | **contosorma57a6**. Use o nome do seu hub IoT na solução de Monitoramento Remoto. |
    | Nome da política do hub IoT | **iothubowner** Confirme se a política utilizada é uma política de proprietário. |
    | Chave de política do hub IoT | Este campo é preenchido automaticamente. |
    | Grupo de consumidores do hub IoT | **timeseriesinsights** |
    | Formato de serialização do evento | **JSON**     | Nome da propriedade timestamp | Deixar em branco |

    ![Criar Origem do Evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Se você precisar conceder acesso a usuários adicionais para o gerenciador do Time Series Insights, use estas etapas para [conceder o acesso a dados](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

O gerenciador do Time Series Insights é um aplicativo Web que o ajuda a criar visualizações dos dados.

1. Selecione o guia **Visão geral**.

1. Clique em **Ir para o ambiente**, que abrirá o aplicativo Web do gerenciador do Time Series Insights.

    ![Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **Últimas 12 horas** no menu de tempos rápidos e clique em **Pesquisar**.

    ![Pesquisa do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. No painel de termos à esquerda, selecione um valor de Medida de **temperatura** e um valor de Dividir por de **iothub-connection-device-id**.

    ![Consulta do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Clique com o botão direito do mouse no gráfico e selecione **Explorar eventos**.

    ![Eventos do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Os eventos são renderizados na grade em um formato tabular.

    ![Tabela do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Clique no botão de exibição de perspectiva.

    ![Perspectiva do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique em **Adicionar** para criar uma nova consulta na perspectiva.

    ![Adicionar Pesquisa do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Selecione uma hora rápida de **Últimas 12 horas**, uma Medida de **Umidade** e uma opção de Dividir por **iothub-connection-device-id**.

    ![Consulta do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de exibição de perspectiva para exibir seu painel de métricas de dispositivo.

    ![Painel do Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como explorar e consultar dados no gerenciador do Time Series Insights, confira [Gerenciador do Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
