---
title: "Introdução ao Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre o Azure Data Factory, um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2017
ms.author: shlo
ms.openlocfilehash: 7961f998b59991b47e1c22d826bf0a50fcd09099
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2018
---
# <a name="introduction-to-azure-data-factory"></a>Introdução à Fábrica de Dados do Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-introduction.md)
> * [Versão 2 – Versão prévia](introduction.md)

No mundo de Big Data, os dados brutos e não organizados são, muitas vezes, armazenados em sistemas relacionais, não relacionais e outros sistemas de armazenamento. No entanto, os dados brutos em si não possuem o contexto ou significado apropriados para fornecer uma visão adequada para os analistas, cientistas de dados ou responsáveis por decisões de negócio. 

O Big Data requer um serviço que possa orquestrar e operacionalizar processos para refinar esses enormes repositórios de dados brutos em visões de negócio acionáveis. O Azure Data Factory é um serviço de nuvem gerenciado que foi criado para esses projetos híbridos complexos para extrair, transformar e carregar (ETL), extrair, carregar e transformar (ELT) e de integração de dados.

Por exemplo, imagine uma empresa de jogos que coleta petabytes de logs de jogos que são gerados por jogos na nuvem. A empresa deseja analisar esses logs para saber as preferências dos clientes, a faixa demográfica e o comportamento de uso. Ela também deseja identificar as oportunidades de venda adicional e venda cruzada, desenvolver recursos novos e cativantes para estimular o crescimento do negócio, e fornecer uma melhor experiência para os clientes.

Para analisar esses logs, a empresa precisa usar os dados de referência, como as informações sobre o cliente, sobre o jogo e sobre a campanha de marketing, que estão em um armazenamento de dados local. A empresa deseja usar esses dados provenientes do repositório de dados local, combinando-os com os dados de log adicionais que ela possui no repositório de dados na nuvem. 

Para extrair informações, ela espera processar os dados associados usando um cluster Spark na nuvem (Azure HDInsight) e publicar os dados transformados em um data warehouse de nuvem como o SQL Data Warehouse do Azure para gerar um relatório de forma fácil. A empresa deseja automatizar esse fluxo de trabalho, e monitorá-lo e gerenciá-lo diariamente. Ela também deseja executá-lo quando entram arquivos no contêiner de armazenamento de blobs.

O Azure Data Factory é a plataforma que resolve esses cenários de dados. É um *serviço de integração de dados com baseado em nuvem que permite que você crie fluxos de trabalho orientados a dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados*. Usando o Azure Data Factory, é possível criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de diferentes repositórios de dados. Ele pode processar e transformar dados usando serviços de computação como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. 

Além disso, é possível publicar dados de saída para repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). Por fim, por meio do Azure Data Factory, os dados brutos podem ser organizados em armazenamentos de dados e em data lakes importantes para possibilitar melhores decisões corporativas.

![Visão de nível superior do Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que normalmente está disponível (GA), consulte [Introduction to Data Factory version 1](v1/data-factory-introduction.md) (Introdução ao Data Factory versão 1).

## <a name="how-does-it-work"></a>Como ele funciona?
Os pipelines (fluxos de trabalho controlados por dados) no Azure Data Factory normalmente executam as quatro etapas a seguir:

![Quatro etapas de um fluxo de trabalho controlado por dados](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Conectar e coletar

As empresas possuem dados de vários tipos que estão localizados em diferentes fontes locais, na nuvem, estruturadas, não estruturadas e semi-estruturadas, todos chegando em diferentes intervalos e velocidades. 

A primeira etapa ao criar um sistema de geração de informações é conectar todas as diferentes fontes de dados e processamento necessárias, como os serviços de software como serviço (SaaS), bancos de dados, compartilhamentos de arquivos e serviços Web FTP. A etapa seguinte é mover os dados conforme necessário para um local central para que estes sejam processados posteriormente.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. É caro e difícil integrar e manter esses sistemas. Além disso, eles também, muitas vezes, não possuem o monitoramento, os alertas e os controles de nível empresarial oferecidos por um serviço totalmente gerenciado.

Com o Data Factory, você pode usar a [Atividade de Cópia](copy-activity-overview.md) em um pipeline de dados para mover os dados que estão em armazenamentos de dados de origem locais e na nuvem para um armazenamento de dados centralizado na nuvem para análise posterior. Por exemplo, é possível coletar dados no Azure Data Lake Store e transformar os dados posteriormente usando um serviço de computação do Azure Data Lake Analytics. Também é possível coletar dados no armazenamento de blobs do Azure e transformá-los posteriormente usando um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Após os dados estarem presentes num repositório de dados central na nuvem, processe ou transforme os dados coletados usando serviços de computação como o HDInsight Hadoop, Spark, Data Lake Analytics e Machine Learning. Você quer produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis.

### <a name="publish"></a>Publicar
Após os dados brutos terem sido refinados para uma forma consumível pronta para negócios, carregue-os no Data Warehouse do Azure, Banco de Dados SQL do Azure, Azure CosmosDB ou qualquer mecanismo analítico para o qual os seus usuários empresariais possam apontar a partir de suas respectivas ferramentas de business intelligence.

### <a name="monitor"></a>Monitoramento
Após ter criado e implantado com sucesso o pipeline de integração de dados, fornecendo valor empresarial a partir de dados refinados, monitore as atividades e pipelines agendados para saber as taxas de sucesso e falha. O Azure Data Factory possui suporte integrado para monitoramento de pipeline através do Azure Monitor, API, PowerShell, Microsoft Operations Management Suite e painéis de integridade no portal do Azure.

## <a name="top-level-concepts"></a>Conceitos de nível superior
Uma assinatura do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou data factories). O Azure Data Factory é composto de quatro componentes principais. Esses componentes trabalham juntos para oferecer a plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para mover e transformar dados.

### <a name="pipeline"></a>Pipeline
Um data factory pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que realiza uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados provenientes de um blob do Azure e, em seguida, executa uma consulta Hive em um cluster HDInsight para particionar os dados. 

A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de gerenciar cada uma individualmente. As atividades em um pipeline podem ser encadeadas para operarem de modo sequencial ou elas podem operar de forma independente em paralelo.

### <a name="activity"></a>Atividade
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle.

### <a name="datasets"></a>Conjunto de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam para ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Os serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para que o Data Factory se conecte aos recursos externos. Pense dessa maneira: um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado de Armazenamento do Azure especifica a cadeia de conexão para conectar-se à conta de Armazenamento do Azure. Além disso, um conjunto de dados de blob do Azure especifica o contêiner de blob e a pasta que contém os dados.

Serviços vinculados são usados para duas finalidades no Data Factory:

- Para representar uma **fonte de dados** que inclui, mas não está limitada a, um banco de dados do SQL Server local, um banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de blobs do Azure. Para obter uma lista dos armazenamentos de dados com suporte, consulte o artigo [Copy activity](copy-activity-overview.md) (Atividade de cópia).

- Para representar um **recurso de computação** que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, confira o artigo [transformar dados](transform-data.md).

### <a name="triggers"></a>Gatilhos
Os gatilhos representam a unidade de processamento que determina quando uma execução de pipeline precisa ser inicializada. Existem diferentes tipos de gatilhos para diferentes tipos de eventos. Para visualização, o data factory dá suporte ao gatilho do agendador de relógio. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da execução do pipeline. As execuções de pipeline normalmente são instanciadas por meio da transmissão de argumentos para os parâmetros que são definidos em pipelines. Os argumentos podem ser passados manualmente ou na definição do gatilho.

### <a name="parameters"></a>parâmetros
Os parâmetros são pares chave-valor da configuração somente leitura.  Os parâmetros são definidos no pipeline. Os argumentos para os parâmetros definidos são passados durante a execução por um contexto de execução criado por um gatilho ou por um pipeline executado manualmente. As atividades no pipeline consomem os valores de parâmetro.

Um conjunto de dados é um parâmetro fortemente tipado e uma entidade reutilizável/referenciável. Uma atividade pode referenciar conjuntos de dados e consumir as propriedades que são estabelecidas na definição do conjunto de dados.

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. Ele também é uma entidade reutilizável/referenciável.

### <a name="control-flow"></a>Controlar fluxo
O fluxo de controle é uma orquestração de atividades do pipeline que inclui o encadeamento de atividades em uma sequência, ramificação, definindo parâmetros no nível do pipeline, e passando argumentos durante a invocação do pipeline sob demanda ou a partir de um gatilho. Também inclui transmissão de estado personalizada e contêineres de looping, ou seja, iteradores for-each.


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Regiões com suporte

Atualmente, é possível criar data factories nas regiões Leste dos EUA, Leste dos EUA 2, Europa Ocidental. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite que você crie fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre os armazenamentos de dados com suporte e o processamento de dados usando serviços de computação em outras regiões ou em um ambiente local. Também permite monitorar e gerenciar fluxos de trabalho usando mecanismos programáticos e de IU.

Embora o Data Factory esteja disponível somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental, o serviço que capacita a movimentação de dados no Data Factory está disponível globalmente em várias regiões. Se um repositório de dados estiver atrás de um firewall, então um Integration Runtime auto-hospedado instalado em seu ambiente local moverá os dados.

Por exemplo, digamos que seus ambientes de computação, como o cluster Azure HDInsight e o Azure Machine Learning, estejam ficando sem a região Europa Ocidental. Você pode criar e usar uma instância do Azure Data Factory no Leste dos EUA e no Leste dos EUA 2 e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

## <a name="compare-with-version-2"></a>Comparar com a versão 2
Para obter uma lista das diferenças entre a versão 1 e 2 do serviço Data Factory, veja [Comparar com a versão 1](compare-versions.md). 

## <a name="next-steps"></a>Próximas etapas
Saiba como criar um data factory seguindo as instruções passo a passo nos seguintes Guias de Início Rápido: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST](quickstart-create-data-factory-rest-api.md) e no Portal do Azure. 
