---
title: Migrar clusters do Apache Hadoop locais para o Azure HDInsight – melhores práticas de migração de dados
description: Aprenda as melhores práticas de migração de dados para migrar clusters do Hadoop locais para o Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 6b06b8eb8d5e18acd3107ec5cccac79fc7be7edc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418170"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrar clusters do Apache Hadoop locais para o Azure HDInsight – melhores práticas de migração de dados

Este artigo fornece recomendações para a migração de dados para o Azure HDInsight. Ele faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas locais do Apache Hadoop para o Azure HDInsight.

## <a name="migrate-data-from-on-premises-to-azure"></a>Migrar dados do local para o Azure

Há duas opções principais para migrar dados do local para o ambiente do Azure:

1.  Transferir dados pela rede com TLS
    1.  Pela Internet
    2.  ExpressRoute
2.  Dados de envio
    1.  Serviço de importação/exportação
        - Apenas HDs ou SDDs SATA internos
        - Criptografado em REST (AES-128/AES-256)
        - O trabalho de importação pode ter até 10 discos
        - Disponível em todas as regiões públicas e GA
    1.  Data Box
        - Até 80 TB de dados por Data Box
        - Criptografado em REST (AES-256)
        - Usa os protocolos NAS e é compatível com ferramentas de cópia de dados comuns
        - Hardware reforçado
        - Disponível apenas nos EUA e em versão prévia pública

A tabela a seguir tem a duração de transferência de dados aproximada com base na largura de banda de rede e no volume de dados. Use um Data Box se for esperado que a migração de dados levará mais de três semanas.

|**Quantidade de dados**|**Largura de banda da rede**|||
|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**
|1 TB|2 dias|1 dia| 2 horas|14 minutos|
|10 TB|22 dias|10 dias|1 dia|2 horas|
|35 TB|76 dias|34 dias|3 dias|8 horas|
|80 TB|173 dias|78 dias|8 dias|19 horas|
|100 TB|216 dias|97 dias|10 dias|1 dia|
|200 TB|1 ano|194 dias|19 dias|2 dias|
|500 TB|3 anos|1 ano|49 dias|5 dias|
|1 PB|6 anos|3 anos|97 dias|10 dias|
|2 PB|12 anos|5 anos|194 dias|19 dias|

Ferramentas nativas do Azure, como DistCp, Azure Data Factory e AzureCp, podem ser usadas para transferir dados pela rede. A ferramenta de terceiros WANDisco também pode ser usada para a mesma finalidade. O Kafka Mirrormaker e o Sqoop podem ser usados para a transferência de dados contínua do local para os sistemas de armazenamento do Azure.

## <a name="performance-considerations-when-using-apache-distcp"></a>Considerações de desempenho ao usar o Apache DistCp

O DistCp é um projeto da Apache que usa um trabalho de mapeamento do MapReduce para transferir dados, manipular erros e recuperar esses erros. Ele atribui uma lista de arquivos de origem para cada tarefa de mapeamento. A tarefa de mapeamento, em seguida, copia todos os seus arquivos atribuídos para o destino. Há várias técnicas que podem melhorar o desempenho do DistCp.

### <a name="increase-the-number-of-mappers"></a>Aumentar o número de mapeadores

O DistCp tenta criar tarefas de mapeamento para que cada uma copie aproximadamente o mesmo número de bytes. Por padrão, os trabalhos do DistCp usam 20 mapeadores. Usar mais mapeadores para o Distcp (com o parâmetro “m” na linha de comando) aumenta o paralelismo durante o processo de transferência de dados e diminui a duração da transferência de dados. No entanto, há duas coisas a considerar ao aumentar o número de mapeadores:

1. A menor granularidade do DistCp é um único arquivo. Especificar um número de mapeadores maior do que o número de arquivos de origem não ajuda e desperdiça os recursos de cluster disponíveis.
1. Considere a memória YARN disponível no cluster para determinar o número de mapeadores. Cada tarefa de mapeamento é iniciada como um contêiner do YARN. Supondo que não há outras cargas de trabalho pesadas sendo executadas no cluster, o número de mapeadores pode ser determinado pela seguinte fórmula: m = (número de nós de trabalho \* memória de YARN para cada nó de trabalho) / tamanho do contêiner do YARN. No entanto, se outros aplicativos estiverem usando a memória, opte por usar apenas uma parte da memória do YARN para trabalhos do DistCp.

### <a name="use-more-than-one-distcp-job"></a>Usar mais de um trabalho do DistCp

Quando o tamanho do conjunto de dados a ser movido for maior que 1 TB, use mais de um trabalho do DistCp. Usar mais de um trabalho limita o impacto de falhas. Se algum trabalho falhar, você precisará apenas reiniciar esse trabalho específico em vez de todos os trabalhos.

### <a name="consider-splitting-files"></a>Considerar dividir os arquivos

Se houver um pequeno número de arquivos grandes, considere dividi-los em partes do arquivo de 256 MB para obter maior simultaneidade potencial com mais mapeadores.

### <a name="use-the-strategy-command-line-parameter"></a>Usar o parâmetro de linha de comando “strategy”

Considere o uso do parâmetro `strategy = dynamic` na linha de comando. O valor padrão do parâmetro `strategy` é `uniform size`, caso em que cada mapa copia aproximadamente o mesmo número de bytes. Quando esse parâmetro é alterado para `dynamic`, o arquivo de listagem é dividido em vários “arquivos de partes”. O número de arquivos de partes é um múltiplo do número de mapas. Cada tarefa de mapeamento é atribuída a um dos arquivos de partes. Após todos os caminhos em uma parte serem processados, a parte atual é excluída e uma nova parte é adquirida. O processo continua até que não haja mais partes disponíveis. Essa abordagem "dinâmica" permite que tarefas de mapeamento mais rápidas consumam mais caminhos que as mais lentas, acelerando assim o trabalho geral do DistCp.

### <a name="increase-the-number-of-threads"></a>Aumentar o número de threads

Veja se aumentar o parâmetro `-numListstatusThreads` melhora o desempenho. Esse parâmetro controla o número de threads a serem usados para a criação de listagem de arquivos e 40 é o valor máximo.

### <a name="use-the-output-committer-algorithm"></a>Usar o algoritmo de confirmador de saída

Veja se passar o parâmetro `-Dmapreduce.fileoutputcommitter.algorithm.version=2` melhora o desempenho do DistCp. Esse algoritmo de confirmador saída tem otimizações em torno da gravação de arquivos de saída no destino. O comando a seguir é um exemplo que mostra o uso de parâmetros diferentes:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migração de metadados

### <a name="hive"></a>Hive

O metastore do Hive pode ser migrado usando os scripts ou usando a replicação de banco de dados.

#### <a name="hive-metastore-migration-using-scripts"></a>Migração do metastore do Hive usando scripts

1. Gere os DDLs do Hive de metastore do Hive local. Esta etapa pode ser realizada usando um [script de Bash de wrapper]. (https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)
1. Edite o DDL gerado para substituir a URL do HDFS com URLs WASB/ADLS/ABFS
1. Execute o DDL atualizado no metastore do cluster do HDInsight
1. Certifique-se de que a versão do metastore do Hive é compatível entre o local e a nuvem

#### <a name="hive-metastore-migration-using-db-replication"></a>Migração do metastore do Hive usando a replicação de banco de dados

- Configure a replicação de banco de dados entre o banco de dados do metastore do Hive local e o banco de dados de metastore do HDInsight
- Use "Hive MetaTool" para substituir a URL do HDFS com URLs WASB/ADLS/ABFS, por exemplo:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="ranger"></a>Ranger

- Exporte as políticas do Ranger locais para arquivos xml.
- Transforme os caminhos baseados em HDFS específicos locais em WASB/ADLS usando uma ferramenta como XSLT.
- Importe as políticas no Ranger em execução no HDInsight.

## <a name="next-steps"></a>Próximas etapas

Leia o próximo artigo desta série:

- [Melhores práticas de segurança e DevOps para migração do local para o Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)