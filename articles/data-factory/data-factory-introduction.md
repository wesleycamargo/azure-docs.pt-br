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
ms.date: 09/22/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0d53dfea0ebaae4f8080718d84e2d26ecae37681


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introdução ao serviço Azure Data Factory, um serviço de integração de dados na nuvem
## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure?
O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a **movimentação** e a **transformação** dos dados. Você pode criar soluções de integração de dados usando o serviço Data Factory que podem receber dados de vários repositórios de dados, transformar/processar os dados e publicar os dados resultantes nos repositórios de dados. 

O serviço Data Factory permite criar pipelines de dados que movem e transformam dados e depois executar os pipelines em um agendamento especificado (por hora, diariamente, semanalmente, etc.). Ele também fornece visualizações avançadas para exibir a linhagem e as dependências entre os pipelines de dados e monitorar todos os seus pipelines de dados em uma única exibição unificada, a fim de identificar os problemas e configurar alertas de monitoramento facilmente.

![Diagrama: visão geral do Data Factory, um serviço de integração de dados](./media/data-factory-introduction/what-is-azure-data-factory.png)
**Figura1.**  Receba dados de várias fontes de dados, prepare, transforme, analise os dados e publique dados prontos para uso e consumo.

## <a name="pipelines-and-activities"></a>Pipelines e atividades
Em uma solução de Data Factory, você cria um ou mais **pipelines**de dados. Pipeline é um agrupamento lógico de atividades. Eles são usados para agrupar atividades em uma unidade que, em conjunto, executam uma tarefa. 

**Atividades** definem as ações a serem realizadas em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a dois tipos de atividades: atividades de movimentação de dados e atividades de transformação de dados. 

## <a name="data-movement-activities"></a>Atividades de movimentação de dados
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter mais detalhes. 

## <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Confira o artigo [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para obter mais detalhes.

Se você precisar mover dados de/para um repositório de dados a que a Atividade de Cópia não dê suporte, ou transformar seus dados usando sua própria lógica, crie uma **atividade personalizada do .NET**. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="linked-services"></a>Serviços vinculados
Serviços vinculados definem as informações necessárias para o Data Factory se conectar a recursos externos (Exemplos: Armazenamento do Azure, SQL Server local, Azure HDInsight). Serviços vinculados são usados para duas finalidades no Data Factory:

* Para representar um **repositório de dados** , incluindo, mas não se limitando a um SQL Server local, banco de dados Oracle, compartilhamento de arquivos ou uma conta de Armazenamento de Blobs do Azure. Confira a seção [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter uma lista de repositórios de dados com suporte. 
* Para representar um **recurso de computação** que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Confira a seção [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para obter uma lista de ambientes de computação com suporte. 

## <a name="datasets"></a>CONJUNTOS DE DADOS
Serviços vinculados vinculam armazenamentos de dados a um data factory do Azure. Conjuntos de dados representam estruturas de dados nos repositórios de dados. Por exemplo, um serviço vinculado do Armazenamento do Azure fornece informações de conexão para que o Data Factory se conecte a uma conta do Armazenamento do Azure. Um conjunto de dados de blob do Azure especifica o contêiner de blobs e a pasta no Armazenamento de Blobs do Azure, de onde o pipeline deve ler os dados. Da mesma forma, um serviço vinculado do Azure SQL fornece informações de conexão para um banco de dados SQL e um conjunto de dados do Azure SQL especifica a tabela que contém os dados.   

## <a name="relationship-between-data-factory-entities"></a>Relação entre entidades de Data Factory
O Data Factory tem algumas entidades principais que trabalham juntas para definir dados de entrada e saída, eventos de processamento e cronograma e recursos necessários para executar o fluxo de dados desejado.

![Diagrama: Data Factory, um serviço de integração de dados de nuvem - conceitos principais](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figure 2.**  Relações entre o Conjunto de dados, Atividade, Pipeline e Serviço vinculado

Com os quatro conceitos simples de serviços vinculados, conjuntos de dados, atividades, pipelines, você está pronto para começar! [Você pode criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md). 

## <a name="supported-regions"></a>Regiões com suporte
No momento, você pode criar fábricas de dados nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação. 

O Azure Data Factory em si não armazena dados. Ele permite criar fluxos controlados por dados para orquestrar a movimentação de dados entre [repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores) e o processamento de dados usando [serviços de computação](data-factory-compute-linked-services.md) em outras regiões ou em um ambiente local. Ele também permite [monitorar e gerenciar fluxos de trabalho](data-factory-monitor-manage-pipelines.md) usando mecanismos programáticos e de interface do usuário. 

Embora o Azure Data Factory só esteja disponível nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**, o serviço que capacita a movimentação de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Se um repositório de dados estiver por trás de um firewall, um [Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado no ambiente local moverá os dados. 

Por exemplo, digamos que seus ambientes de computação, como o cluster Azure HDInsight e o Azure Machine Learning, estejam ficando sem a região Europa Ocidental. Você pode criar e usar uma instância do Azure Data Factory na Europa Setentrional e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Leva alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

No futuro, pretendemos ter o Azure Data Factory em todas as regiões com suporte do Azure.

## <a name="next-steps"></a>Próximas etapas
Para saber como criar data factories com pipeline de dados, siga as instruções passo a passo nos tutoriais a seguir. 

| Tutorial | Descrição |
| --- | --- |
| [Criar um pipeline de dados que processa dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md) |Neste tutorial, você cria seu primeiro data factory no Azure com um pipeline de dados que **processa os dados** executando o script do Hive em um cluster Azure HDInsight (Hadoop). |
| [Criar um pipeline de dados para mover dados entre dois armazenamentos de dados em nuvem](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Neste tutorial, você cria um data factory com um pipeline que **move dados** do Armazenamento de Blobs para o banco de dados SQL. |
| [Criar um pipeline de dados para mover dados entre um armazenamento de dados local e um armazenamento de dados em nuvem usando o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) |Neste tutorial, você cria um data factory com um pipeline que **move dados** de um banco de dados SQL Server **local** para um blob do Azure. Como parte do passo a passo, você instala e configura o Gateway de Gerenciamento de Dados em seu computador. |




<!--HONumber=Nov16_HO2-->


