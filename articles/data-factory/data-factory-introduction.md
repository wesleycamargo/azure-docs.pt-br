---
title: "Introdução ao Data Factory, um serviço de integração de dados | Microsoft Docs"
description: "Saiba o que é o Azure Data Factory: um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados."
keywords: "integração de dados, integração de dados de nuvem, o que é o azure data factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introdução ao serviço Azure Data Factory, um serviço de integração de dados na nuvem
## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure?
No mundo de grandes volumes de dados, como os dados existentes são usados nos negócios? É possível enriquecer os dados gerados na nuvem usando dados de referência de fontes de dados locais ou de outras fontes de dados diferentes? Portanto, o que é necessário é uma plataforma para agregar e processamento de dados de uma grande variedade de fontes. O Azure Data Factory é um serviço de integração de dados baseado em nuvem que orquestra e automatiza a **movimentação** e a **transformação** dos dados. Você pode criar soluções de integração de dados que podem ingerir dados de entrada de vários repositórios de dados diferentes, transformar/processar os dados e publicar os dados de saída para outros repositórios de dados. 

![Diagrama: visão geral do Data Factory, um serviço de integração de dados](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figura1.** Receba dados de várias fontes de dados, prepare, transforme, analise os dados e publique dados prontos para uso e consumo.


## <a name="what-does-it-offer"></a>O que ele oferece? 
Tradicionalmente, os projetos de integração de dados têm girado em torno da criação de processos de extrair, transformar e carregar (ETL) que extraem dados de várias fontes de dados dentro de uma organização, transformam os dados de acordo com o esquema de destino de um Enterprise Data Warehouse (EDW) e carregar os dados em um EDW conforme mostrado na imagem a seguir. O EDW é então acessado como a única fonte verdadeira para soluções de análise de BI.

![ETL tradicional](media/data-factory-introduction/traditional-etl.png)
**ETL tradicional**

O atual panorama de dados para as empresas continua a crescer exponencialmente em volume, variedade e complexidade, conforme mostrado na imagem a seguir. Ele está mais diversificado do que nunca com dados locais e de nuvem de velocidades e formas diferentes. O processamento de dados deve ocorrer em locais geográficos e inclui uma combinação de software livre, soluções comerciais e serviços de processamento personalizados que são caros e difíceis de integrar e manter. A agilidade necessária para se adaptar ao atual panorama de grandes volumes de dados de alteração é uma oportunidade para mesclar o EDW tradicional com recursos necessários para um sistema de produção de informações moderno. O Azure Data Factory é a plataforma de composição para trabalhar em EDWs tradicionais e com o panorama de dados mutável para capacitar as empresas a aproveitar todos os dados que estão disponíveis para a tomada de decisões orientada a dados.

![Novo panorama de grandes volumes de dados](media/data-factory-introduction/new-big-data-landscape.png)
**Novo panorama de grandes volumes de dados**

O serviço Azure Data Factory capacita as empresas a aproveitar essa diversidade, fornecendo uma plataforma para **compor serviços de processamento, armazenamento e movimentação de dados para pipelines de produção de informações**e gerenciar ativos de dados confiáveis.

O serviço do Azure Data Factory permite:
- **Trabalhar facilmente com diversos serviços de armazenamento e processamento de dados**. 

    As empresas têm dados de diferentes tipos, localizados em diferentes fontes. A primeira etapa na criação de um sistema de produção de informações é se conectar a todas as fontes necessárias de dados e processamento, como serviços SaaS, os serviços web compartilhamentos, FTP, arquivos e mover os dados conforme necessário para um local centralizado para processamento posterior.

    Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. Isso é caro e é difícil de integrar e manter esses sistemas, além de, geralmente, não ter o monitoramento e os alertas de nível empresarial e os controles que podem oferecer um serviço completamente gerenciado.

    Com o Data Factory, você pode usar a Atividade de Cópia em um pipeline de dados para mover dados de repositórios de dados locais e na nuvem para um repositório de dados centralizado na nuvem para análise posterior. Por exemplo, você pode coletar dados em um Azure Data Lake Store e transformar os dados posteriormente usando um serviço de computação do Azure Data Lake Analytics. Ou, coletar dados em Armazenamento de Blobs do Azure e transformam dados posteriormente usando um cluster de Hadoop do Azure HDInsight.
- **Transformar dados em informações confiáveis**. 

    Depois que os dados estão presentes em um repositório de dados centralizado na nuvem, você deseja criar e implantar as pipelines de dados para produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis. A transformação de dados no Azure Data Factory é executada por atividades de transformação, como o Hive, Pig, MapReduce, Execução em Lote do Azure Machine Learning e atividades C# personalizadas em execução no cluster Hadoop do HDInsight do Azure, VMs do Azure Machine Learning ou pool de VMs do Lote do Azure.
- **Monitore pipelines de dados em um único lugar**.

    Com um portfólio diversificado de dados, é importante ter uma visão completa e confiável de seus serviços de armazenamento, processamento e movimentação de dados. O Data Factory ajuda você a avaliar rapidamente o funcionamento de ponta a ponta do pipeline de dados, a identificar problemas e a tomar ações corretivas, se necessário. Rastrear visualmente a linhagem de dados e as relações entre os seus dados através de qualquer uma das suas fontes. Veja uma contagem completa de histórico de execução do trabalho, o funcionamento do sistema e as dependências de um único painel de monitoramento.

## <a name="how-does-it-work"></a>Como ele funciona? 
Existem três estágios de produção de informações em um Azure Data Factory:

![Três estágios de produção de informações](media/data-factory-introduction/three-information-production-stages.png)

- **Conectar e Coletar**: Neste estágio, os dados de várias fontes de dados são coletados em um único lugar.
- **Transformar e Enriquecer**: neste estágio, os dados coletados são processados ou transformados.
- **Publicar**. Nesse estágio, os dados são publicados para que ele possa ser consumido por ferramentas de BI, ferramentas de análise e outros aplicativos.

## <a name="key-components"></a>Principais componentes
Uma assinatura do Azure pode ter um ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é composto por quatro componentes principais que trabalham juntos para fornecer a plataforma na qual você pode compor movimentações de dados simples a complexas e orquestrações de transformação para o fluxo de dados.

### <a name="activity"></a>Atividade
As Atividades definem as ações a serem realizadas em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a dois tipos de atividades: atividades de movimentação de dados e atividades de transformação de dados.

Cada atividade pode não ter ou ter vários conjuntos de dados de entrada e gerar um ou mais conjuntos de dados de saída. 


### <a name="data-movement-activities"></a>Atividades de movimentação de dados
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. A Data Factory dá suporte aos repositórios de dados a seguir. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Para obter mais informações, confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, confira o artigo [Atividades de transformação de dados](data-factory-data-transformation-activities.md).

Se você precisar mover dados de/para um repositório de dados a que a Atividade de Cópia não dê suporte, ou transformar seus dados usando sua própria lógica, crie uma **atividade personalizada do .NET**. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="pipeline"></a>Pipeline
Um pipeline é um grupo de atividades. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que recebe dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados de log. A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. Por exemplo, você pode implantar e agendar o pipeline, em vez de atividades de forma independente.

### <a name="datasets"></a>Conjunto de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no Armazenamento de Blobs do Azure, de onde o pipeline deve ler os dados. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense dessa maneira - o conjunto de dados representa a estrutura dos dados e o serviço vinculado define a conexão à fonte de dados.  Serviços vinculados são usados para duas finalidades no Data Factory:

* Para representar um **repositório de dados** , incluindo, mas não se limitando a um SQL Server local, banco de dados Oracle, compartilhamento de arquivos ou uma conta de Armazenamento de Blobs do Azure. Confira a seção [Atividades de movimentação de dados](#data-movement-activities) para obter uma lista de repositórios de dados com suporte.
* Para representar um **recurso de computação** que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Confira a seção [Atividades de transformação de dados](#data-transformation-activities) para obter uma lista de ambientes de computação com suporte.

### <a name="relationship-between-data-factory-entities"></a>Relação entre entidades de Data Factory
![Diagrama: Data Factory, um serviço de integração de dados de nuvem - conceitos principais](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figure 2.** Relações entre o Conjunto de dados, Atividade, Pipeline e Serviço vinculado

## <a name="supported-regions"></a>Regiões com suporte
No momento, você pode criar fábricas de dados nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite criar fluxos controlados por dados para orquestrar a movimentação de dados entre [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados usando [serviços de computação](data-factory-compute-linked-services.md) em outras regiões ou em um ambiente local. Ele também permite [monitorar e gerenciar fluxos de trabalho](data-factory-monitor-manage-pipelines.md) usando mecanismos programáticos e de interface do usuário.

Embora o Data Factory só esteja disponível nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**, o serviço que capacita a movimentação de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Se um repositório de dados estiver por trás de um firewall, um [Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado no ambiente local moverá os dados.

Por exemplo, digamos que seus ambientes de computação, como o cluster Azure HDInsight e o Azure Machine Learning, estejam ficando sem a região Europa Ocidental. Você pode criar e usar uma instância do Azure Data Factory na Europa Setentrional e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Leva alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

No futuro, pretendemos ter o Azure Data Factory em todas as regiões com suporte do Azure.

## <a name="next-steps"></a>Próximas etapas
Para saber como criar data factories com pipeline de dados, siga as instruções passo a passo nos tutoriais a seguir:

| Tutorial | Descrição |
| --- | --- |
| [Mover dados entre dois armazenamentos de dados em nuvem](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Neste tutorial, você cria um data factory com um pipeline que **move dados** do Armazenamento de Blobs para o banco de dados SQL. |
| [Transformar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md) |Neste tutorial, você cria seu primeiro data factory no Azure com um pipeline de dados que **processa os dados** executando o script do Hive em um cluster Azure HDInsight (Hadoop). |
| [Mover dados entre um armazenamento de dados local e um armazenamento de dados em nuvem usando o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) |Neste tutorial, você cria um data factory com um pipeline que **move dados** de um banco de dados SQL Server **local** para um blob do Azure. Como parte do passo a passo, você instala e configura o Gateway de Gerenciamento de Dados em seu computador. |

