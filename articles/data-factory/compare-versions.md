---
title: "Comparar versões 1 e 2 do Azure Data Factory | Microsoft Docs"
description: "Este artigo compara as versões 1 e 2 do Azure Data Factory."
services: data-factory
documentationcenter: 
author: kromerm
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/20/2017
ms.author: makromer
ms.openlocfilehash: f55348e9974de17c6d7e704e185f1ea9b21ff66e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="compare-azure-data-factory-versions-1-and-2"></a>Comparar versões 1 e 2 do Azure Data Factory
Este artigo compara a versão 2 (V2) com a versão 1 (V1) do Azure Data Factory. Para obter uma introdução à V1 do serviço, consulte [Data Factory versão 1 - Introdução](v1/data-factory-introduction.md)e para a V2, consulte [Data Factory versão 2 - Introdução](introduction.md).

## <a name="feature-comparison"></a>Comparação de recursos
A tabela a seguir compara os recursos da V1 e da V2. 

| Recurso | V1 | V2 | 
| ------- | --------- | --------- | 
| CONJUNTOS DE DADOS | Uma exibição nomeada de dados que fazem referência aos dados que você deseja usar em suas atividades, como entradas e saídas. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no armazenamento de Blobs dos quais a atividade deve ler os dados.<br/><br/>**Disponibilidade** define o modelo de divisão da janela de processamento do conjunto de dados (por exemplo, por hora ou diária etc.). | Os conjuntos de dados são os mesmos na V2. No entanto, você não precisa definir agendas de **disponibilidade** para os conjuntos de dados. Você pode definir um recurso de gatilho que pode agendar pipelines de um paradigma de agendador de relógio. Para saber mais, confira [Gatilhos](concepts-pipeline-execution-triggers.md#triggers) e [Conjuntos de dados](concepts-datasets-linked-services.md). | 
| Serviços vinculados | Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. | O mesmo que o Data Factory V1, mas com uma nova propriedade **connectVia**, a fim de usar o ambiente de computação de Integration Runtime do Data Factory V2. Para saber mais, consulte [Tempos de execução de integração](concepts-integration-runtime.md) e [Propriedades de serviço vinculadas para o Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties). |
| Pipelines | Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Você usou startTime, endTime, isPaused para agendar e executar pipelines. | Pipelines ainda são grupos de atividades para execução nos dados. No entanto, o agendamento das atividades no pipeline foi separado em novos recursos de gatilho. Pense em pipelines no Data Factory V2 mais como "unidades de fluxo de trabalho" que você agenda separadamente por meio de gatilhos. <br/><br/>Os pipelines não têm "períodos" de execução de tempo no Data Factory V2. Os conceitos de startTime, endTime e isPaused do Data Factory V1 não estão mais presentes no Data Factory V2. Para saber mais, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md) e [Pipelines e atividades](concepts-pipelines-activities.md). |
| Atividades | Atividades definem ações a serem executadas em seus dados dentro de um pipeline. Há suporte para atividades de movimentação de dados (atividade de cópia) e atividades de transformação de dados (como Hive, Pig e MapReduce). | No Data Factory V2, as atividades ainda são ações definidas dentro de um pipeline. O V2 apresenta novas [atividades de fluxo de controle](concepts-pipelines-activities.md#control-activities). Você pode usar essas atividades no fluxo de controle (loop e ramificação). Atividades de movimentação de dados e de transformação de dados com suporte na V1 também têm suporte na V2. Você pode definir as atividades de transformação sem usar conjuntos de dados na V2. |
| Movimentação de dados híbridos e distribuição de atividades | Anteriormente conhecido como [Gateway de gerenciamento de dados](v1/data-factory-data-management-gateway.md), havia suporte para a movimentação de dados entre o local e a nuvem. Agora, isso é chamado de Integration Runtime.| O Gateway de gerenciamento de dados agora é chamado de Integration Runtime auto-hospedado. Ele fornece a mesma funcionalidade da V1. <br/><br/> O Integration Runtime do Azure-SSIS na V2 também dá suporte à implantação e execução de pacotes SSIS (SQL Server Integration Services) na nuvem. Para obter detalhes, confira o artigo [Integration runtime](concepts-integration-runtime.md).|
| parâmetros | ND | Parâmetros são pares de chave-valor de configurações somente leitura definidos em pipelines. Você pode passar argumentos para os parâmetros ao executar manualmente o pipeline. Se você estiver usando um gatilho de agendador, o gatilho também poderá passar valores para os parâmetros. As atividades no pipeline consomem os valores de parâmetro.  |
| Expressões | O Data Factory V1 permite que você use funções e variáveis de sistema em consultas de seleção de dados e propriedades de atividade/conjunto de dados. | No Data Factory V2, você pode usar expressões em qualquer lugar em um valor de cadeia de caracteres JSON. Para saber mais, confira [Expressões e funções na V2](control-flow-expression-language-functions.md).|
| Execuções de pipeline | ND | Uma instância única da uma execução do pipeline. Por exemplo, digamos que você tem um pipeline que é executado às 8h, 9h e 10h. Haveria três execuções de pipeline separadas nesse caso. Cada execução de pipeline tem uma ID de execução de pipeline exclusiva, que é um GUID que define exclusivamente essa execução do pipeline específica. As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros definidos nos pipelines. |
| Execuções de atividade | ND | Uma instância de uma execução de atividade em um pipeline. | 
| Execuções de gatilho | ND | Uma instância de execução de gatilho. Para saber mais, confira [Gatilhos](concepts-pipeline-execution-triggers.md). |
| Agendamento | O agendamento tem base em horários de início/término do pipeline e na disponibilidade do conjunto de dados. | Gatilho de agendador ou execução por meio do agendador externo. Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md). |

As seções a seguir fornecem mais informações sobre recursos da versão 2: 

## <a name="control-flow"></a>Controlar fluxo  
Para dar suporte aos fluxos e padrões de integração diversos no moderno data warehouse, o Data Factory V2 habilitou um novo modelo de pipeline de dados flexível que não está mais vinculado aos dados de série temporal. Alguns dos fluxos comuns que agora estão disponíveis que antes não eram possíveis incluem:   

### <a name="chaining-activities"></a>Encadeando atividades
Na versão 1, você precisava configurar saída de uma atividade como entrada de outra atividade para encadeá-las. Na V2, você pode encadear atividades em uma sequência dentro de um pipeline. Você pode usar a propriedade **dependsOn** em uma definição de atividade para encadeá-la com uma atividade de upstream. Para saber mais e obter um exemplo, confira os artigos [Pipelines e atividades](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) e [Ramificação e encadeamento atividades](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Atividades de ramificação
Agora, na V2, é possível ramificar as atividades dentro de um pipeline. A [atividade If-Condition](control-flow-if-condition-activity.md) fornece a mesma funcionalidade que uma instrução `if` fornece em linguagens de programação. Ela avalia um conjunto de atividades quando a condição é avaliada como `true` e outro conjunto de atividades quando a condição é avaliada como `false`. Para obter um exemplo de ramificação de atividades, consulte o tutorial [Ramificação e encadeamento de atividades](tutorial-control-flow.md).

### <a name="parameters"></a>parâmetros 
É possível definir parâmetros no nível do pipeline e passar argumentos enquanto você invoca o pipeline sob demanda ou de um gatilho. As atividades podem consumir os argumentos passados para o pipeline. Para saber mais, consulte [Pipelines e gatilhos](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Passagem de estado personalizada
As saídas da atividade incluindo o estado podem ser consumidas por uma atividade posterior no pipeline. Por exemplo, na definição de JSON de uma atividade, você pode acessar a saída da atividade anterior usando a seguinte sintaxe: `@activity('NameofPreviousActivity').output.value`. Esse recurso permite a compilação de fluxos de trabalho nos quais os valores podem passar pelas atividades.

### <a name="looping-containers"></a>Contêineres de looping
A [atividade ForEach](control-flow-for-each-activity.md) define um fluxo de controle repetitivo no seu pipeline. Essa atividade é usada para iterar em uma coleção e executa atividades especificadas em um loop. A implementação dessa atividade em loop é semelhante à estrutura em loop Foreach nas linguagens de programação. 

A atividade [Until](control-flow-until-activity.md) fornece a mesma funcionalidade que uma estrutura de loop do-until fornece em linguagens de programação. Ela executa um conjunto de atividades em um loop até que a condição associada à atividade seja avaliada como verdadeira. Especifique um valor de tempo limite para a atividade Until no Data Factory.  

### <a name="trigger-based-flows"></a>Fluxos baseados em gatilhos
Os pipelines podem ser disparados sob demanda ou conforme um horário. O artigo [pipelines e gatilhos](concepts-pipeline-execution-triggers.md) tem as informações detalhadas sobre gatilhos. 

### <a name="invoke-a-pipeline-from-another-pipeline"></a>Invocar um pipeline de outro pipeline
A atividade [Executar atividade de pipeline](control-flow-execute-pipeline-activity.md) permite que um pipeline do Data Factory invoque outro pipeline.

### <a name="delta-flows"></a>Fluxos delta
Um caso de uso importante nos padrões de ETL são as "carregamentos delta", ou somente o carregamento de dados que foram alterados desde a última iteração de um pipeline. Novos recursos na versão 2, como [atividade de pesquisa](control-flow-lookup-activity.md), planejamento flexível e fluxo de controle, permitem esse caso de uso de uma maneira natural. Para obter um tutorial com instruções passo a passo, confira [Tutorial: cópia incremental](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Outras atividades de fluxo de controle
Confira outras atividades de fluxo de controle com suporte do Data Factory V2: 

Atividade de controle | Descrição
---------------- | -----------
[ForEachActivity](control-flow-for-each-activity.md) | A atividade ForEach define um fluxo de controle repetitivo no seu pipeline. Essa atividade é usada para iterar em uma coleção e executa atividades especificadas em um loop. A implementação dessa atividade em loop é semelhante à estrutura em loop Foreach nas linguagens de programação.
[WebActivity](control-flow-web-activity.md) | A atividade da Web pode ser usada para chamar um ponto de extremidade REST personalizado de um pipeline do Data Factory. Você pode passar conjuntos de dados e serviços vinculados a serem consumidos e acessados pela atividade. 
[Atividade de pesquisa](control-flow-lookup-activity.md) | A atividade de pesquisa pode ser usada para ler ou procurar um registro/nome de tabela/valor de qualquer fonte externa. Essa saída pode referenciada pelas atividades com êxito. 
[Atividade de obtenção de metadados](control-flow-get-metadata-activity.md) | A atividade GetMetadata pode ser usada para recuperar metadados de todos os dados no Azure Data Factory. 
[Atividade de espera](control-flow-wait-activity.md) | O pipeline espera (pausa) durante o período especificado.

## <a name="deploy-ssis-packages-to-azure"></a>Implantar pacotes do SSIS no Azure 
Se você quiser mover suas cargas de trabalho de SSIS (SQL Server Integration Services) para a nuvem, crie um data factory versão 2 e provisione um IR (Integration Runtime) do SSIS do Azure. O IR de SSIS do Azure é um cluster totalmente gerenciado das VMs do Azure (nós) dedicados à execução dos seus pacotes de SSIS na nuvem. Quando você provisiona o IR do SSIS do Azure, você pode usar as mesmas ferramentas que costuma usar para implantar pacotes SSIS em um ambiente de SSIS local. Por exemplo, você pode usar o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar pacotes do SSIS nesse tempo de execução no Azure. Para obter as instruções passo a passo, confira o tutorial [Implantar os pacotes do SQL Server Integration Services no Azure](tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Agendamento flexível
No Data Factory V2, você não precisa definir agendas de disponibilidade do conjunto de dados. Você pode definir um recurso de gatilho que pode agendar pipelines de um paradigma de agendador de relógio. Você também pode passar parâmetros para pipelines de um gatilho para um modelo de agendamento/execução flexível. Os pipelines não têm "períodos" de execução de tempo no Data Factory V2. Os conceitos de startTime, endTime e isPaused do Data Factory V1 não estão mais presentes no Data Factory V2. Veja como compilar e, depois, agendar um pipeline no Data Factory V2: [Execução e gatilhos de pipeline](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Suporte para mais armazenamentos de dados
A V2 dá suporte à cópia de dados entre mais armazenamentos de dados do que a V1. Para obter uma lista de armazenamentos de dados com suporte, consulte os seguintes artigos:

- [V1 - armazenamentos de dados com suporte](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [V2 - armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Suporte a cluster do Spark sob demanda
A V2 oferece suporte à criação de um cluster do HDInsight Spark sob demanda. Para criar um cluster do Spark sob demanda, especifique o tipo de cluster como Spark em sua definição de serviço vinculado do HDInsight sob demanda. Em seguida, configure a atividade do Spark em seu pipeline para usar esse serviço vinculado. Em tempo de execução, quando a atividade é executada, o serviço do Data Factory cria automaticamente o cluster do Spark para você. Para obter mais informações, consulte os seguintes artigos:

- [Atividade do Spark na V2](transform-data-using-spark.md)
- [Serviço vinculado do Azure HDInsight sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Atividades personalizadas
Na V1, o código da Atividade DotNet (Personalizada) é implementado por meio da criação de um projeto de biblioteca de classes .NET, com uma classe que implementa o método Executar da interface IDotNetActivity. Portanto, o código personalizado deve ser escrito no .Net Framework 4.5.2 e ser executado em nós do Pool do Lote do Azure baseado no Windows. 

Na Atividade Personalizada da V2, não é necessário implementar uma interface .Net. Agora, é possível executar diretamente comandos, scripts e código personalizado compilado como um executável. 

Para saber mais, consulte [Diferença entre a atividade personalizada na V1 e na V2](transform-data-using-dotnet-custom-activity.md#difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1).

## <a name="sdks"></a>SDKs
A Versão 2 do Data Factory fornece um conjunto mais completo de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines.

- **SDK do .NET**: o SDK do .NET está atualizado para a V2. 
- **PowerShell**: os cmdlets do PowerShell são atualizados para a V2. Os cmdlets do V2 têm **DataFactoryV2** no nome. Por exemplo: Get-AzureRmDataFactoryV2. 
- **SDK do Python**: esse SDK é novo no V2.
- **API REST**: a API REST é atualizada para a V2. 

Os SDKs que foram atualizados para a V1 não são retrocompatíveis com clientes na versão 1. 

## <a name="authoring-experience"></a>Experiência de criação
O Data Factory V1 permite que você crie pipelines usando o Editor do Data Factory no Portal do Azure. Atualmente, o Data Factory V2 dá suporte apenas ao modo programático (SDK do .NET, API REST, PowerShell, Python etc.) para criação de data factories. Ainda não há suporte para a interface do usuário.  O Data Factory V1 também dá suporte à criação de SDK, REST e PowerShell.

## <a name="monitoring-experience"></a>Experiência de monitoramento
Na V2, você também pode monitorar os data factories usando o [Azure Monitor](monitor-using-azure-monitor.md). Os novos cmdlets do PowerShell dão suporte ao monitoramento de [tempos de execução de integração](monitor-integration-runtime.md). A V1 e a V2 oferecem suporte ao monitoramento visual por meio do aplicativo de monitoramento, que pode ser iniciado no Portal do Azure.


## <a name="next-steps"></a>Próximas etapas
Saiba como criar um data factory seguindo as instruções passo a passo nos seguintes Guias de Início Rápido: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST](quickstart-create-data-factory-rest-api.md). 
