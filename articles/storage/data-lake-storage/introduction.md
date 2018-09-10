---
title: Visualização do Azure Data Lake Storage Gen2 Introdução
description: Fornece uma visão geral da Visualização do Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 072573b16fbeebac1ec942b0be508cf901b5cd27
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42143928"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Introdução ao Azure Data Lake Storage Gen2 Preview

O Data Storage do Azure Data Lake Gen2 Preview é um conjunto de recursos dedicados à análise de big data, criado sobre o [ armazenamento do Azure Blob ](../blobs/storage-blobs-introduction.md). Ele permite que você faça interface com seus dados usando os paradigmas de sistema de arquivos e armazenamento de objetos. Isso faz do Data Lake Storage Gen2 o único serviço de armazenamento multimodal baseado em nuvem, permitindo que você extraia o valor analítico de todos os seus dados.

O Data Lake Storage Gen2 apresenta todas as qualidades necessárias para o ciclo de vida completo dos dados analíticos. Isso resulta da convergência dos recursos de nossos dois serviços de armazenamento existentes. Os recursos do [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), como semântica do sistema de arquivos, segurança e escala em nível de arquivo, são combinados com armazenamento em camadas de baixo custo, alta disponibilidade / recursos de recuperação de desastre e um grande ecossistema SDK / de ferramentas [Armazenamento do Blob do Azure](../blobs/storage-blobs-introduction.md). No Data Lake Storage Gen2, todas as qualidades de armazenamento de objetos permanecem enquanto se adicionam as vantagens de uma interface de sistema de arquivos otimizada para cargas de trabalho de análise.

## <a name="designed-for-enterprise-big-data-analytics"></a>Projetado para análise de big data da empresa

O Data Lake Storage Gen2 é o serviço de armazenamento básico para a criação de EDL (Enterprise Data Lakes) no Azure. Projetado desde o início para atender a vários petabytes de informações enquanto mantém centenas de gigabits de taxa de transferência, o Data Lake Storage Gen2 oferece uma maneira fácil de gerenciar grandes quantidades de dados.

Um recurso fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](./namespace.md) ao serviço de armazenamento Blob, que organiza objetos / arquivos em uma hierarquia de diretórios para acesso a dados de alto desempenho. O namespace hierárquico também permite que o Data Lake Storage Gen2 ofereça suporte a paradigmas de armazenamento de objetos e sistemas de arquivos ao mesmo tempo. Por exemplo, uma convenção de nomenclatura de armazenamento de objetos comum usa barras no nome para imitar uma estrutura de pastas hierárquica. Essa estrutura se torna real com o Data Lake Storage Gen2. Operações como renomear ou excluir um diretório tornam-se operações únicas de metadados atômicos no diretório, em vez de enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

No passado, a análise baseada na nuvem tinha que se comprometer em áreas de desempenho, gerenciamento e segurança. O Data Lake Storage Gen2 aborda cada um desses aspectos das seguintes maneiras:

- **O desempenho** é otimizado porque você não precisa copiar ou transformar dados como um pré-requisito para análise. O namespace hierárquico melhora muito o desempenho das operações de gerenciamento de diretório, o que melhora o desempenho geral do trabalho.

- **Gerenciamento** é mais fácil porque você pode organizar e manipular arquivos por meio de diretórios e subdiretórios.

- **Segurança** é aplicável porque você pode definir as permissões POSIX em arquivos individuais ou pastas.

- **A efetividade de custo** é possível porque o Data Lake Storage Gen2 é construído sobre o [ armazenamento de Blob do Azure de baixo custo ](../blobs/storage-blobs-introduction.md). Os recursos adicionais reduzem ainda mais o custo total de propriedade para executar a análise de big data no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais recursos do Data Lake Storage Gen2

> [!NOTE]
> Durante a pré-visualização pública do Data Lake Storage Gen2, alguns dos recursos listados abaixo podem variar em sua disponibilidade. À medida que novos recursos e regiões são lançados durante o programa de visualização, essas informações serão comunicadas.
> [ Inscreva-se ](https://aka.ms/adlsgen2signup) na pré-visualização pública do Data Lake Storage Gen2.  

- **Acesso compatível com Hadoop**: Data Lake Storage O Gen2 permite gerenciar e acessar dados como faria com um [ Hadoop Distributed File System (HDFS) ](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [ driver ABFS ](./abfs-driver.md) está disponível em todos os ambientes Apache Hadoop, incluindo [ Azure HDInsight ](../../hdinsight/index.yml) e [ Azure Databricks ](../../azure-databricks/index.yml) para acessar dados armazenados no Data Lake Storage Gen2.

- **Um superconjunto de permissões POSIX**: o modelo de segurança para o Data Lake Gen2 é totalmente compatível com as permissões ACL e POSIX, juntamente com alguma granularidade extra específica para o Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas de administração ou por meio de estruturas, como Hive e Spark.

- **Econômico**: Data Lake Storage O Gen2 apresenta capacidade de armazenamento e transações de baixo custo. À medida que os dados transitam por todo o seu ciclo de vida, as taxas de faturamento mudam, mantendo os custos no mínimo por meio de recursos integrados, como [ ciclo de vida de armazenamento do Blur do Azure ](../common/storage-lifecycle-managment-concepts.md).

- **Funciona com ferramentas, estruturas e aplicativos de armazenamento do Blob**: Data Lake Storage O Gen2 continua a trabalhar com uma grande variedade de ferramentas, estruturas e aplicativos que existem hoje para o armazenamento do Blob.

- **Driver otimizado**: o driver `abfs` é [ otimizado especificamente ](./abfs-driver.md) para análise de big data. As APIs REST correspondentes são exibidas pelo `dfs`terminal, `dfs.core.windows.net`.

## <a name="scalability"></a>Escalabilidade

O Armazenamento do Azure é dimensionável por design, independentemente de você acessar via Data Lake Storage Gen2 ou interfaces de armazenamento do Blob. É capaz de armazenar e servir *muitos exabytes de dados*. Essa quantidade de armazenamento está disponível com taxa de transferência medida em gigabits por segundo (Gbps) em altos níveis de operações de entrada/saída por segundo (IOPS). Além da persistência, o processamento é executado em latências por solicitação quase constantes que são medidas nos níveis de serviço, conta e arquivo.

## <a name="cost-effectiveness"></a>Custo-benefício

Um dos muitos benefícios da criação do Data Lake Storage Gen2 no armazenamento do Azure Blob é o [ baixo custo ](https://azure.microsoft.com/pricing/details/storage) de capacidade de armazenamento e transações. Ao contrário de outros serviços de armazenamento em nuvem, o Data Lake Storage Gen2 reduz os custos porque os dados não precisam ser movidos ou transformados antes de executar a análise.

Além disso, recursos como o [namespace hierárquico](./namespace.md) melhoram significativamente o desempenho geral de muitas tarefas de análise. Essa melhoria no desempenho significa que você precisa de menos poder computacional para processar a mesma quantidade de dados, resultando em um custo total de propriedade (TCO) mais baixo para o trabalho analítico de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir descrevem alguns dos principais conceitos do Data Lake Storage Gen2 e detalham como armazenar, acessar, gerenciar e obter insights de seus dados:

* [Namespace hierárquico](./namespace.md)
* [Criar uma conta de armazenamento](./quickstart-create-account.md)
* [ Crie um cluster do HDInsight com o Armazenamento de Dados do Azure Data Lake Gen2 ](./quickstart-create-connect-hdi-cluster.md)
* [ Use uma conta Gen2 do Azure Data Lake Storage em bancos de dados do Azure ](./quickstart-create-databricks-account.md) 