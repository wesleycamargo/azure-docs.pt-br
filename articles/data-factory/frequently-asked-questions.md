---
title: 'Azure Data Factory: perguntas frequentes | Microsoft Docs'
description: Obtenha resposta a perguntas frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: dd2475d5940927c65cae4d12b9697cbe535b69a8
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-data-factory-faq"></a>Perguntas frequentes sobre o Azure Data Factory
Este artigo se aplica à versão 2 do serviço do Azure Data Factory. Fornece respostas para perguntas frequentes sobre o Data Factory.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira as [perguntas frequentes sobre o Data Factory versão 1](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure? 
O Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado que automatiza a movimentação e a transformação dos dados. Assim como uma fábrica que usa equipamentos para transformar matérias-primas em produtos, o Azure Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato. 

Usando o Azure Data Factory, você pode criar fluxos de trabalho conduzidos por dados para mover dados entre armazenamentos de dados locais e na nuvem. E você pode processar e transformar dados usando serviços de computação como tempo de execução de integração do SSIS (SQL Server Integration Services), Azure Data Lake Analytics e Azure HDInsight. 

Com o Data Factory, você pode executar o processamento de dados em um serviço de nuvem baseado no Azure ou em seu próprio ambiente de computação auto-hospedado, como SSIS, SQL Server ou Oracle. Depois de criar um pipeline que executa a ação que você precisa, você pode agendá-lo para ser executado periodicamente (por exemplo, por hora, diariamente ou semanalmente) ou disparar o pipeline em uma ocorrência de evento. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>O que é diferente na versão 2?
O Azure Data Factory versão 2 foi criado com base no serviço de transformação e na movimentação de dados do Azure Data Factory original, se expandindo a um conjunto mais amplo de cenários de integração de dados em nuvem. O Azure Data Factory Versão 2 oferece os seguintes recursos:

- Fluxos de controle e escala
- Implantação e execução de pacotes SSIS no Azure

Após o lançamento da versão 1, reconhecemos que os clientes precisam criar cenários de integração de dados híbridos complexos que exijam a movimentação de dados e o processamento local, na nuvem e nas VMs (máquinas virtuais) em nuvem. Esses requisitos trazem a necessidade de transferir e processar dados dentro de ambientes de rede virtual seguros e de aumentar com poder de processamento sob demanda.

À medida que os pipelines de dados se tornam uma parte mais importante da estratégia de análise de negócios, percebemos que essas atividades exigem agendamento flexível para dar suporte a cargas de dados incrementais e execuções disparadas por eventos. À medida que aumenta a complexidade, o mesmo acontece com o requisito para o serviço dar suporte a paradigmas de fluxo de trabalho comuns que incluem ramificação, loop e processamento condicional.

Com a versão 2, também é possível migrar os pacotes SSIS existentes para a nuvem. Esta ação retira e desloca SSIS como um serviço do Azure gerenciado no Data Factory, que utiliza um novo recurso de tempo de execução de integração. Rodando um tempo de execução de integração SSIS na versão 2, você pode executar, gerenciar, monitorar e criar pacotes do SSIS na nuvem.

### <a name="control-flows-and-scale"></a>Fluxos de controle e escala 
Para dar suporte aos fluxos e padrões de integração diversos no moderno data warehouse, o Data Factory habilitou um novo modelo de pipeline de dados flexível que não está mais vinculado aos dados de série temporal. Com esta versão, você pode modelar condicionais, ramificar no fluxo de controle de um pipeline de dados e transmitir explicitamente parâmetros dentro e entre esses fluxos.

Agora você tem liberdade para modelar qualquer estilo de fluxo exigido para a integração de dados, que pode ser expedido sob demanda ou repetido conforme uma agenda. Alguns fluxos comuns que estão habilitados agora, mas que não eram possíveis anteriormente, são:   

- Fluxos de controle:
    - Encadear atividades em uma sequência em um pipeline.
    - Ramificar atividades em um pipeline.
    - parâmetros
        - Definir parâmetros no nível do pipeline e passar argumentos enquanto você invoca o pipeline sob demanda ou de um gatilho.
        - As atividades podem consumir os argumentos passados para o pipeline.
    - Passagem de estado personalizada
        - As saídas da atividade incluindo o estado podem ser consumidas por uma atividade posterior no pipeline.
    - Contêineres de looping
        - For-each 
- Fluxos baseados em gatilho:
    - Os pipelines podem ser acionados sob demanda ou por hora.
- Fluxos delta:
    - Use parâmetros e defina seus limites para cópia de delta ao mover tabelas de referência ou dimensão de um relational store local ou na nuvem para carregar os dados no lake. 

Para obter mais informações, consulte [Tutorial: fluxo de controle](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implantar pacotes do SSIS no Azure 
Se desejar mover suas cargas de trabalho de SSIS, você poderá criar um Data Factory versão 2 e provisionar um tempo de execução de integração do SSIS do Azure. O tempo de execução de integração do SSIS do Azure é um cluster totalmente gerenciado de VMs do Azure (nós) dedicadas à execução dos seus pacotes de SSIS na nuvem. Para obter instruções passo a passo, consulte o tutorial [Implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 

### <a name="sdks"></a>SDKs
Se você for um usuário avançado e estiver procurando por uma interface programática, a versão 2 fornecerá um conjunto avançado de SDKs que você pode usar para criar, gerenciar ou monitorar pipelines usando seu IDE favorito.

- **SDK .NET**: o SDK .NET está atualizado para a versão 2. 
- **PowerShell**: os cmdlets do PowerShell são atualizados para a versão 2. Os cmdlets da versão 2 têm *DataFactoryV2* no nome. Por exemplo, *Get-AzureRmDataFactoryV2*. 
- **Python SDK**: esse SDK é novo na versão 2.
- **API REST**: a API REST é atualizada para a versão 2.  

Os SDKs que são atualizados para a versão 2 não são retrocompatíveis com clientes na versão 1. 

### <a name="monitoring"></a>Monitoramento
Atualmente, a versão 2 oferece suporte ao monitoramento de data factories usando apenas SDKs. O portal ainda não é compatível com monitoramento de data factories da versão 2. 

## <a name="what-is-integration-runtime"></a>O que é o tempo de execução de integração?
O tempo de execução de integração é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre vários ambientes de rede:

- **Movimentação de dados**: mover dados entre armazenamentos de dados em uma rede pública e armazenamentos de dados em uma rede privada (rede privada local ou virtual). Ele fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados de alto desempenho e escalonáveis.
- **Expedição de atividades**: expedir e monitorar atividades de transformação executadas em uma variedade de serviços de computação, como Azure HDInsight, Azure Machine Learning, Banco de Dados SQL do Azure, SQL Server e muitos mais.
- **Executar pacotes SSIS**: executa nativamente pacotes SSIS em um ambiente de computação do Azure gerenciado.

Você pode implantar uma ou mais instâncias do tempo de execução de integração conforme necessário para movimentar e transformar os dados. O tempo de execução de integração pode ser executado na rede pública do Azure ou em uma rede privada (local, Rede Virtual do Azure ou VPC (nuvem privada virtual) do Amazon Web Services). 

Para obter mais informações, consulte [Tempo de execução de integração no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite do número de tempos de execução de integração?
Não há nenhum limite rígido quanto ao número de instâncias de tempo de execução de integração que você pode ter em um data factory. Entretanto, há um limite de número de núcleos de VM que o tempo de execução de integração pode usar por assinatura para a execução de pacotes do SSIS. Para saber mais, confira [Limites do Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>Quando devo usar a versão 2, em vez da versão 1? 
Se você for novo no Azure Data Factory, inicie diretamente com a versão 2. Se você já estiver usando a versão 1, recrie seus data factories na versão 2.

> [!WARNING]
> A versão 2 do Data Factory está em versão prévia e não em GA (disponibilidade geral). Portanto, ela não se enquadra nos mesmos compromissos de Contrato de Nível de Serviço (SLA) da versão 1 do Data Factory, que está disponível para o público geral. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Quais são os conceitos de nível superior da versão 2?
Uma assinatura do Azure pode ter uma ou mais instâncias (ou data factories) do Azure Data Factory. O Azure Data Factory contém quatro componentes principais que trabalham juntos como uma plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para movimentação e transformação dos dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingerem dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados. O benefício é que você pode usar um pipeline para gerenciar as atividades como um conjunto, em vez de gerenciar cada atividade individualmente. Você pode encadear as atividades em um pipeline para operá-las em sequência ou pode operá-las de modo independente, em paralelo.

### <a name="activity"></a>Atividade
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, é possível usar uma atividade de *Cópia* para copiar dados de um armazenamento de dados para outro armazenamento de dados. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle.

### <a name="data-sets"></a>Conjuntos de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense desta maneira: um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão para conectar-se à conta de Armazenamento do Azure. E um conjunto de dados de Blob do Azure especifica o contêiner de blob e a pasta que contém os dados.

Serviços vinculados têm duas finalidades no Data Factory:

- Para representar um *armazenamento de dados* que inclui, mas não é limitado a, uma instância do SQL Server local, uma instância de banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de Blobs do Azure. Para obter uma lista de repositórios de dados com suporte, consulte [Atividade de cópia no Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode hospedar a execução de uma atividade. Por exemplo, a atividade do Hive do HDInsight é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, consulte [Transformar dados no Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Gatilhos
Gatilhos representam as unidades de processamento que determinam quando a execução de um pipeline é iniciada. Existem diferentes tipos de gatilhos para diferentes tipos de eventos. Para a versão prévia, há suporte para o gatilho do agendador de relógio. 


### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da uma execução do pipeline. Normalmente, você instancia uma execução do pipeline passando argumentos para os parâmetros definidos no pipeline. Você pode passar os argumentos manualmente ou dentro da definição do gatilho.

### <a name="parameters"></a>parâmetros
Os parâmetros são pares chave-valor em uma configuração somente leitura. Você define os parâmetros em um pipeline e passa os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um gatilho ou de um pipeline que você executa manualmente. As atividades no pipeline consomem os valores de parâmetro.

Um conjunto de dados é um parâmetro fortemente tipado e uma entidade que você pode reutilizar ou consultar. Uma atividade pode referenciar conjuntos de dados e consumir as propriedades estabelecidas na definição do conjunto de dados.

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. Também é uma entidade que você pode reutilizar ou consultar.

### <a name="control-flows"></a>Fluxos de controle
Fluxos de controle coordenam as atividades de pipeline, incluindo encadeamento de atividades em uma sequência, ramificação, parâmetros que você define no nível do pipeline e argumentos passados conforme você invoca o pipeline sob demanda ou de um gatilho. Fluxos de controle também incluem transmissão de estado personalizada e contêineres de looping (ou seja, iteradores for-each).


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços Data Factory?
Para os detalhes de preço do Azure Data Factory, consulte [Detalhes de preço do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="what-regions-support-azure-data-factory-version-2"></a>Quais regiões oferecem suporte ao Azure Data Factory versão 2?
No momento, você pode criar data factories da versão 2 nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. No entanto, um data factory pode usar o tempo de execução de integração em outra região para mover dados entre armazenamentos de dados, expedir atividades para serviços de computação ou expedir pacotes do SSIS. Para saber mais, confira [Locais do Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso me manter informado sobre o Data Factory?
Para obter as informações mais atualizadas sobre o Azure Data Factory, vá para os seguintes sites:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page da documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Análise técnica aprofundada 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>É possível executar pipelines da versão 1 e da versão 2 lado a lado?
Nº Os data factories das versões 2 e 1 não podem conter entidades (por exemplo, serviços vinculados, conjuntos de dados ou pipelines) da outra versão.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>Ainda preciso definir conjuntos de dados na versão 2?
O conjunto de dados não é mais uma entidade obrigatória para a maioria das atividades. Ele é necessário para Cópia, Machine learning, pesquisa, validação e atividades personalizadas que usem o esquema e outras informações de metadados no conjunto de dados para a transformação. O restante das atividades não precisa mais de conjuntos de dados.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>Posso encadear duas atividades sem um conjunto de dados na versão 2?
Sim. É possível encadear atividades na versão 2 sem a necessidade de conjuntos de dados. O encadeamento é feito usando a propriedade **dependsOn** na definição de JSON do pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Todas as atividades da versão 1 são compatíveis com a versão 2? 
Sim, todas as atividades de versão 1 têm suporte na versão 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Como faço para agendar um pipeline da versão 2? 
Você pode usar o disparador do agendador para agendar um pipeline da versão 2. O gatilho usa uma agenda de calendário do relógio e você pode usá-la para agendar pipelines periodicamente ou usando os padrões recorrentes com base em calendário (por exemplo, semanalmente nas segundas-feiras, às 6 horas, e nas quintas-feiras, às 21 horas). Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Posso passar parâmetros para uma execução de pipeline na versão 2?
Sim, os parâmetros são conceito de primeira classe de nível superior na versão 2. Você pode definir parâmetros no nível do pipeline e passar argumentos durante a execução do pipeline sob demanda ou usando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>É possível definir valores padrão para os parâmetros do pipeline? 
Sim. Você pode definir valores padrão para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade em um pipeline pode consumir argumentos passados para uma execução de pipeline? 
Sim. Cada atividade no pipeline pode consumir o valor do parâmetro passado para o pipeline e ser executada com o constructo `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída de uma atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida em uma atividade subsequente com o constructo `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como tratar normalmente o valores nulos em uma saída de atividade? 
Você pode usar o constructo `@coalesce` nas expressões para manipular valores nulos normalmente. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>Posso usar repetição e o tempo limite no nível de atividade na versão 2?
Sim. Para controlar a execução das atividades na versão 2 da mesma maneira que na versão 1, você pode configurar repetição e tempo limite no nível da atividade. 

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um data factory da versão 2, consulte os tutoriais a seguir:

- [Início rápido: criar um data factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: copiar os dados na nuvem](tutorial-copy-data-dot-net.md)

