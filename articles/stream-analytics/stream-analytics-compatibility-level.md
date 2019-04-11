---
title: Reconhecer o nível de compatibilidade para trabalhos do Azure Stream Analytics
description: Saiba como definir um nível de compatibilidade para um trabalho do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recente
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361396"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para trabalhos do Azure Stream Analytics
 
Nível de compatibilidade refere-se aos comportamentos específicos à versão de um serviço do Azure Stream Analytics. O Azure Stream Analytics é um serviço gerenciado, com atualizações de recursos e melhorias de desempenho regulares. Geralmente as atualizações são disponibilizadas automaticamente para os usuários finais. No entanto, alguns recursos novos podem apresentar alterações importantes, tal como alteração no comportamento de um trabalho existente, alterar os processos que consomem dados desses trabalhos, entre outros. Um nível de compatibilidade é usado para representar uma alteração importante introduzida no Stream Analytics. As principais alterações sempre são introduzidas com um novo nível de compatibilidade. 

Nível de compatibilidade garante que os trabalhos existentes são executados sem falhas. Quando você cria um novo trabalho de Stream Analytics, é uma prática recomendada criá-la usando o nível de compatibilidade mais recente. 
 
## <a name="set-a-compatibility-level"></a>Definir um nível de compatibilidade 

O nível de compatibilidade controla o comportamento de tempo de execução de um trabalho do Stream Analytics. Você pode definir o nível de compatibilidade de um trabalho do Stream Analytics usando o portal ou a [criar a chamada à API REST de trabalho](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Atualmente, o Azure Stream Analytics dá suporte a dois níveis de compatibilidade: "1.0" e "1.1". Por padrão, o nível de compatibilidade é definido como "1.0", que foi introduzido durante a disponibilidade geral do Azure Stream Analytics. Para atualizar o valor padrão, navegue até o trabalho de análise do Stream Analytics > selecione a opção **Nível de compatibilidade** na seção **Configurar** e altere o valor. 

Pare o trabalho antes de atualizar o nível de compatibilidade. Não será possível atualizar o nível de compatibilidade se o trabalho estiver em um estado de execução. 

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Quando você atualizar o nível de compatibilidade, o compilador do T-SQL valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Alteração principal no nível de compatibilidade mais recente (1.2)

As principais alterações a seguir são apresentadas no nível de compatibilidade 1.2:

### <a name="geospatial-functions"></a>Funções geoespaciais 

**versões anteriores:** O Azure Stream Analytics usados cálculos geográficos.

**versão atual:** O Azure Stream Analytics permite que você calcular as coordenadas geográficas projetada geométrico. Não há nenhuma alteração na assinatura das funções geoespaciais. No entanto, sua semântica é ligeiramente diferente, permitindo que a computação mais precisa do que antes.

O Azure Stream Analytics dá suporte à indexação de dados de referência geoespacial. Dados de referência que contém elementos geoespaciais podem ser indexados para uma computação de junção mais rápida.

As funções geoespaciais atualizado trazem a expressividade completa do formato do texto WKT (Well Known) geoespacial. Você pode especificar outros componentes geoespaciais que anteriormente não eram suportados com GeoJson.

Para obter mais informações, consulte [atualiza a recursos geoespaciais no Azure Stream Analytics – nuvem e borda de IoT](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução paralela da consulta para fontes de entrada com várias partições 

**versões anteriores:** Consultas de Stream Analytics do Azure exigiam o uso da cláusula PARTITION BY paralelizar o processamento de consulta em partições de origem de entrada.

**versão atual:** Se a lógica de consulta pode ser paralelizada entre partições de origem de entrada, o Azure Stream Analytics cria instâncias de consulta separada e executa cálculos em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração nativa de API em massa com a saída do CosmosDB

**versões anteriores:** O comportamento de upsert era *inserir ou mesclar*.

**versão atual:** Integração nativa de API em massa com a saída do CosmosDB maximiza a taxa de transferência e com eficiência lida com solicitações de limitação.

É o comportamento de upsert *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset ao gravar a saída do SQL

**versões anteriores:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) tipos foram ajustados ao UTC.

**versão atual:** DateTimeOffset não é ajustado.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação estrita de prefixo de funções

**versões anteriores:** Não havia nenhuma validação estrita de prefixos de função.

**versão atual:** O Azure Stream Analytics tem uma validação estrita de prefixos de função. Adicionar um prefixo para uma função interna causa um erro. Por exemplo,`myprefix.ABS(…)` não tem suporte.

Adicionar um prefixo para agregações internas também resulta em erro. Por exemplo, `myprefix.SUM(…)` não tem suporte.

Usando o prefixo "system" para quaisquer resultados de funções definidas pelo usuário em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Não permitir a matriz e objeto como propriedades de chave no adaptador de saída do Cosmos DB

**versões anteriores:** Tipos de matriz e objeto tinham suporte como uma propriedade de chave.

**versão atual:** Não há suporte para tipos de objeto e matriz como uma propriedade de chave.


## <a name="next-steps"></a>Próximas etapas
* [Solucionar problemas de entradas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Integridade de recursos de análise de Stream](stream-analytics-resource-health.md)
