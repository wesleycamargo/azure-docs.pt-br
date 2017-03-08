---
title: Diretrizes de ajuste de desempenho do Azure Data Lake Store | Microsoft Docs
description: Diretrizes de ajuste de desempenho do Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7
ms.lasthandoff: 01/18/2017


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Diretrizes de ajuste de desempenho para o Azure Data Lake Store

Este artigo fornece orientação sobre como obter o melhor desempenho durante a gravação de dados ou a leitura de dados do Azure Data Lake Store de desempenho. Este artigo destina-se a ajudar os usuários a compreender os parâmetros que podem ser configurados para ferramentas de upload/download de dados e cargas de trabalho de análise de dados comumente usadas. O ajuste neste guia é direcionado especificamente para cargas de trabalho com uso intensivo de recursos em que há uma grande quantidade de dados que estão sendo lidos ou gravados no Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)
* **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.


## <a name="guidelines-for-data-ingestion-tools"></a>Diretrizes para ferramentas de ingestão de dados

Esta seção fornece orientações gerais para melhorar o desempenho quando os dados são copiados ou movidos no Data Lake Store. Nesta seção, discutiremos os fatores que limitam o desempenho e as formas de superar essas limitações. Abaixo estão algumas considerações a serem levadas em conta.

* **Dados de origem** - há várias restrições que podem surgir dependendo de onde os dados de origem são provenientes. A taxa de transferência pode ser um gargalo se a fonte de dados está lenta ou o armazenamento remoto tem recursos com baixa taxa de transferência. SSDs, preferencialmente no disco local, fornecerão o melhor desempenho devido à maior taxa de transferência do disco.

* **Rede** - se você tem os dados de origem em VMs, a conexão de rede entre a VM e o Data Lake Store é importante. Use as VMs com a maior NIC disponível para obter mais largura de banda de rede.

* **Cópia entre regiões** - há um grande custo de rede inerente a E/S de dados entre regiões, por exemplo, a execução de uma ferramenta de ingestão de dados em uma VM no Leste dos EUA 2 para gravar dados em uma conta do Data Lake Store no Centro dos EUA. Se você estiver copiando dados entre regiões, o desempenho poderá ser reduzido. É recomendável usar trabalhos de ingestão de dados em máquinas virtuais na mesma região que a conta do Data Lake Store de destino para maximizar a taxa de transferência de rede.                                                                                                                                        

* **Cluster** - se você está executando trabalhos de ingestão de dados por meio de um cluster HDInsight (como DistCp), é recomendável usar VMs da série D para o cluster, pois contêm mais memória. Números maiores de núcleos também ajudam a aumentar a produtividade.                                                                                                                                                                                                                                                                                                            

* **Simultaneidade de threads** - se você está usando um cluster HDInsight para copiar dados de um contêiner de armazenamento, há limitações para quantos threads podem ser usados com base no tamanho do cluster, no tamanho do contêiner e nas configurações de threads paralelos. Uma das maneiras mais importantes de obter um melhor desempenho no Data Lake Store é aumentar a simultaneidade. Você deve ajustar suas configurações para obter o valor máximo de simultaneidade e obter a maior taxa de transferência. A tabela a seguir mostra as configurações para cada método de ingestão que pode ser configurado para obter mais simultaneidade. Siga os links na tabela para ir para artigos que falam sobre como usar a ferramenta para incluir dados no Data Lake Store e também como ajustar o desempenho da ferramenta para obter a taxa de transferência máxima.

    | Ferramenta               | Configuração de simultaneidade                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [Powershell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount, ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Unidades do Azure Data Lake Analytics         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (mapper)                             |
    | [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size, -m (mapper)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>Diretrizes ao trabalhar com cargas de trabalho do HDInsight

Ao executar cargas de trabalho analíticas para trabalhar com dados no Data Lake Store, é recomendável que você use versões do cluster HDInsight 3.5 para obter o melhor desempenho com o Data Lake Store. Quando o trabalho faz uso mais intensivo de E/S, determinados parâmetros podem ser configurados por motivos de desempenho. Por exemplo, se o trabalho consiste principalmente em leitura ou gravação, aumentar a simultaneidade de E/S para e o Azure Data Lake Store pode aumentar o desempenho.

O Azure Data Lake Store é otimizado da melhor forma para o desempenho quando há mais simultaneidade. Existem algumas maneiras gerais de aumentar a simultaneidade para os trabalhos com uso intensivo de E/S.

1. **Executar um número maior de contêineres YARN de computação em vez de alguns contêineres YARN grandes** – ter mais contêineres aumentará a simultaneidade para operações de entrada e saída, aproveitando assim a capacidade do Data Lake Store.

    Por exemplo, vamos supor que você tenha dois nós D3v2 no cluster HDInsight. Cada nó D3v2 tem 12 GB de memória YARN para que duas máquinas D3v2 tenham 24 GB de memória YARN. Suponhamos também que você tenha definido os tamanhos de contêiner YARN como 6 GB. Isso significa que você pode ter quatro contêineres de 6 GB. Portanto, pode haver quatro tarefas simultâneas em execução em paralelo. Para aumentar a simultaneidade, você pode reduzir o tamanho dos contêineres para 3 GB, o que lhe dará oito contêineres de 3 GB. Isso permite que haja oito tarefas simultâneas em execução em paralelo. Abaixo está uma ilustração.

    ![Desempenho do Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    Uma pergunta comum é por que não diminuir o tamanho do contêiner para 1 GB de memória para que eu possa obter 24 contêineres e aumentar ainda mais a simultaneidade. Isso depende de a tarefa precisar de 3 GB de memória ou 1 GB ser suficiente.  Você pode executar uma operação simples no contêiner que requer apenas 1 GB de memória ou uma operação complexa que pode usar 3 GB de memória.  Se reduzir muito o tamanho do contêiner, você poderá receber uma exceção de memória insuficiente.  Quando isso acontecer, você deverá aumentar o tamanho dos contêineres.  Além da memória, o número de núcleos virtuais também pode afetar o paralelismo.

    ![Desempenho do Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **Aumentar a memória no cluster para obter mais simultaneidade** – você pode aumentar a memória no cluster aumentando o tamanho do cluster ou escolhendo um tipo de VM que tenha mais memória. Isso aumentará a quantidade de memória YARN disponível para que você possa criar mais contêineres, o que aumenta a simultaneidade.  

    Por exemplo, vamos supor que você tenha um único nó D3v2 no cluster HDInsight com 12 GB de memória YARN e contêineres de 3 GB.  Você dimensiona o cluster para 2 D3v2, o que aumenta a memória YARN para 24 GB.  Isso aumenta a simultaneidade de 4 para 8.

    ![Desempenho do Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **Comece definindo o número de tarefas para o número de simultaneidade que você tem** – agora, você já definiu o tamanho do contêiner adequadamente para obter o máximo de simultaneidade. Agora, você deve definir o número de tarefas para usar todos os contêineres. Há diferentes nomes para tarefas em cada carga de trabalho.

    Talvez você queira considerar o tamanho de seu trabalho. Se o tamanho do trabalho for grande, cada tarefa poderá ter uma grande quantidade de dados a serem processados. Talvez você queira usar mais tarefas para que cada tarefa não processe muitos dados.

    Por exemplo, vamos supor que você tenha seis contêineres. As tarefas devem ser definidas como 6 como ponto de partida. Você pode experimentar o uso de números mais altos de tarefas para ver se o desempenho melhora. Definir um número maior de tarefas não aumenta a simultaneidade. Se definirmos tarefas como maiores do que 6, a tarefa não será executada até a próxima onda. Se definirmos tarefas como menores do que 6, a simultaneidade não será totalmente utilizada.

    Cada carga de trabalho tem diferentes parâmetros disponíveis para definir o número de tarefas. A tabela a seguir lista alguns deles.

    | Carga de trabalho               | Parâmetro para definir tarefas                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [Spark no HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [Hive no HDInsight](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [MapReduce no HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalho</li><li>Número de instâncias de spout executor</li><li>Número de instâncias de bolt executor </li><li>Número de tarefas de spout</li><li>Número de tarefas de bolt</li></ul>|

## <a name="see-also"></a>Consulte também
* [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)
* [Introdução à Análise Data Lake do Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

