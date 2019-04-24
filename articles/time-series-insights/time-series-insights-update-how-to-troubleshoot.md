---
title: Diagnosticar e solucionar problemas da versão prévia do Azure Time Series Insights | Microsoft Docs
description: Entenda como diagnosticar e solucionar problemas com a Versão prévia do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 381ecd4681bf1d7abfc8939e256bf84db9bc2172
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60460525"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnosticar e solucionar problemas

Este artigo resume vários problemas comuns que você pode encontrar ao trabalhar com o ambiente da Versão prévia do Azure Time Series Insights. O artigo também descreve possíveis causas e soluções para cada problema.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problema: Não consigo localizar meu ambiente no gerenciador da Versão prévia do Azure Time Series Insights

Esse problema pode ocorrer se você não tem permissões para acessar o ambiente do Time Series Insights. Os usuários precisam de uma função de acesso de nível de leitura para exibir o ambiente do Time Series Insights. Para verificar os níveis de acesso atuais e conceder acesso adicional, visite a seção de Políticas de Acesso a Dados no recurso Time Series Insights no [portal do Azure](https://portal.azure.com/).

  ![Ambiente][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problema: Nenhum dado é visto no gerenciador da Versão prévia do Azure Time Series Insights

Há vários motivos comuns pelos quais você não pode ver seus dados no [Gerenciador da Versão prévia do Azure Time Series Insights](https://insights.timeseries.azure.com/preview).

- Pode ser que a origem do evento não esteja recebendo dados.

    Verifique se a origem do evento, que é um hub de eventos ou um hub IoT, está recebendo dados de suas marcas ou instâncias. Para verificar, acesse a página de visão geral do recurso no portal do Azure.

    ![Painel de insights][2]

- Os dados de origem do evento não estão no formato JSON.

    O Time Series Insights é compatível somente com os dados JSON. Para obter exemplos do JSON, consulte [Formas de JSON com suporte](./how-to-shape-query-json.md).

- A chave da origem do evento não tem uma permissão necessária.

  * Para o Hub IoT, você precisa fornecer a chave com a permissão de **conexão de serviço**.

    ![Configuração][3]

  * Conforme mostrado na imagem anterior, as políticas **iothubowner** e **service** funcionam pois têm a permissão de **conexão de serviço**.
  * Para o hub de eventos, você precisa fornecer a chave que tem a permissão de **Escuta**.
  
    ![Permissões][4]

  * Conforme mostrado na imagem anterior, as políticas **read** e **manage** funcionam pois têm a permissão de **Escuta**.

- O grupo de consumidores fornecido não é exclusivo do Time Series Insights.

    Durante o registro de um hub IoT ou hub de eventos, você especifica o grupo de consumidores que é usado para ler os dados. Não compartilhe o grupo de consumidores. Se o grupo de consumidores for compartilhado, o hub de eventos subjacente desconectará automaticamente um dos leitores de forma aleatória. Forneça um grupo de consumidores exclusivo para o Time Series Insights para leitura.

- A propriedade de ID do Time Series especificada no momento do provisionamento está incorreta, ausente ou nula.

    Esse problema poderá ocorrer se a propriedade de ID do Time Series for configurada incorretamente no momento do provisionamento do ambiente. Para obter mais informações, confira [Melhores práticas para a escolha de uma ID do Time Series](./time-series-insights-update-how-to-id.md). Neste momento, você não pode atualizar um ambiente existente do Time Series Insights para usar uma ID do Time Series diferente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: Alguns dados aparecem, mas outros estão ausentes

Pode ser que você esteja enviando dados sem a ID do Time Series.

- Esse problema pode ocorrer quando você envia eventos sem o campo de ID do Time Series no conteúdo. Para obter mais informações, confira [Formas JSON compatíveis](./how-to-shape-query-json.md).

- Esse problema pode ocorrer porque o seu ambiente está sendo limitado.

    > [!NOTE]
    > No momento, o Time Series Insights é compatível com uma taxa máxima de ingestão de 6 Mbps.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: A configuração de nome da propriedade do Carimbo de data/hora de minha origem do evento não funciona

Verifique se o nome e o valor estão em conformidade com as seguintes regras:

* O nome da propriedade Carimbo de data/hora diferencia maiúsculas de minúsculas.
* O valor da propriedade Carimbo de data/hora que vem da origem do evento, como uma cadeia de caracteres JSON, tem o formato `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Um exemplo dessa cadeia de caracteres é `“2008-04-12T12:53Z”`.

A maneira mais fácil de assegurar que o nome da propriedade Carimbo de data/hora seja capturado e funcione corretamente é usar o gerenciador da Versão prévia do Time Series Insights. No gerenciador da Versão prévia do Time Series Insights, use o gráfico para selecionar um período de tempo após fornecer o nome da propriedade Carimbo de data/hora. Clique com o botão direito do mouse na seleção e escolha a opção **explorar eventos**. O primeiro cabeçalho de coluna é o nome da propriedade Carimbo de data/hora. Ele deve ter `($ts)` ao lado da palavra `Timestamp`, em vez de:

* `(abc)`, que indica que o Time Series Insights lê os valores de dados como cadeias de caracteres.
* Ícone de calendário, que indica que o Time Series Insights lê os valores de dados como datetime.
* `#`, que indica que o Time Series Insights lê os valores de dados como um inteiro.

Se a propriedade Carimbo de data/hora não for especificada explicitamente, o tempo de enfileiramento de um hub IoT ou hub de eventos será usado como o Carimbo de data/hora padrão.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problema: Não consigo editar ou exibir meu modelo do Time Series

- Talvez você esteja acessando um ambiente S1 ou S2 do Time Series Insights.

   Os modelos do Time Series são compatíveis apenas com ambientes PAYG. Para obter mais informações de como acessar o ambiente S1/S2 usando o gerenciador da Versão prévia do Time Series Insights, confira [Visualizar dados no gerenciador](./time-series-insights-update-explorer.md).

   ![Access][5]

- Talvez você não tenha permissões para exibir e editar o modelo.

   Os usuários precisam de acesso de nível de colaborador para editar e exibir o modelo do Time Series. Para verificar os níveis de acesso atuais e conceder acesso adicional, visite a seção de Políticas de Acesso a Dados no recurso Time Series Insights no portal do Azure.

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problema: Nenhuma das minhas instâncias no gerenciador da Versão prévia do Time Series Insights têm um pai

Esse problema pode ocorrer o ambiente não tem uma hierarquia de modelo do Time Series definida. Para obter mais informações, confira [Trabalhar com modelos do Time Series](./time-series-insights-update-how-to-tsm.md).

  ![Modelos do Time Series][6]

## <a name="next-steps"></a>Próximas etapas

- Leia [Trabalhar com modelos do Time Series](./time-series-insights-update-how-to-tsm.md).
- Leia [Formas de JSON compatíveis](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png