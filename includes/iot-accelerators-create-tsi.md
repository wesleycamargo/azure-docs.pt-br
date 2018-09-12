---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8c114ed137089e70899e601ebdc1d4d39f562601
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382843"
---
## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Você precisa criar um grupo de consumidores dedicado no hub IoT para telemetria de fluxo para o Time Series Insights. Uma fonte de evento no Time Series Insights deve ter o uso exclusivo de um grupo de consumidores do Hub IoT.

As etapas a seguir usam a CLI do Azure no Azure Cloud Shell para criar o grupo de consumidores:

1. O hub IoT é um dos vários recursos criados quando você implantou o acelerador de solução de Simulação de Dispositivo. Execute o seguinte comando para localizar o nome do hub IoT - lembre-se de usar o nome do seu acelerador de solução:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    O hub IoT é o recurso do tipo **Microsoft.Devices/IotHubs**.

1. Adicione um grupo de consumidores chamado **devicesimulationtsi** ao hub. No comando a seguir, use o nome do acelerador de solução e de hub:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Agora, você pode fechar o Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Crie um novo ambiente do Time Series Insights

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) é um serviço totalmente gerenciado de análise, armazenamento e visualização para o gerenciamento de dados de série temporal em escala IoT na nuvem. Para criar um novo ambiente do Time Series Insights:

1. Entre no [Portal do Azure](http://portal.azure.com/).

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Time Series Insights**:

    ![Novo Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. Para criar seu ambiente do Time Series Insights no mesmo grupo de recursos do acelerador de solução, use os valores na tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome do ambiente | A captura de tela a seguir usa o nome **Contoso-TSI**. Ao concluir esta etapa, escolha seu próprio nome exclusivo. |
    | Assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Grupo de recursos | **contoso-simulation**. Use o nome do seu acelerador de solução. |
    | Local padrão | Este exemplo usa **Leste dos EUA**. Crie seu ambiente na mesma região que seu acelerador de simulação de dispositivo. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Criar Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Adicionar o ambiente do Time Series Insights ao mesmo grupo de recursos do acelerador de solução significa que ele é excluído quando você exclui o acelerador de solução.

1. Clique em **Criar**. Pode levar alguns minutos para o ambiente ser criado.

## <a name="create-event-source"></a>Criar uma origem de eventos

Crie uma nova fonte de evento para se conectar ao hub IoT. Use o grupo de consumidores criado nas etapas anteriores. A fonte de evento do Time Series Insights requer um grupo de consumidores dedicado que não esteja em uso por outro serviço.

1. No portal do Azure, navegue até o novo Ambiente do Time Series.

1. À esquerda, clique em **Origens de Eventos**:

    ![Exibir Origens do Evento](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Clique em **Adicionar**:

    ![Adicionar Origem do Evento](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Para configurar seu hub IoT como uma nova origem do evento, use os valores da tabela a seguir:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome da Origem do Evento | A captura de tela a seguir usa o nome **contoso-iot-hub**. Ao concluir esta etapa, use seu próprio nome exclusivo. |
    | Fonte | **Hub IoT** |
    | Importar opção | **Usar o Hub IoT nas assinaturas disponíveis** |
    | ID da assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Nome do Hub IoT | **contoso-simulation7d894**. Use o nome do hub IoT do seu acelerador de solução de simulação de dispositivo. |
    | Nome da política do hub IoT | **iothubowner** |
    | Chave de política do hub IoT | Este campo é preenchido automaticamente. |
    | Grupo de consumidores do hub IoT | **devicesimulationtsi** |
    | Formato de serialização do evento | **JSON** |
    | Nome da propriedade timestamp | Deixar em branco |

    ![Criar Origem do Evento](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

> [!NOTE]
> Você pode [conceder acesso a usuários adicionais](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) para o Time Series Insights explorer.