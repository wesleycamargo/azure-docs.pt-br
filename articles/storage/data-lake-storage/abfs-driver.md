---
title: Driver de sistema de arquivos de Blob do Azure para versão prévia do Azure Data Lake Store Gen2
description: Driver de sistema de arquivos ABFS do Hadoop
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e92c4efba29f1c40f6d4cb155974ca3a896796e5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114326"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>ABFS (driver de sistema de arquivos de Blob do Azure): um driver de Armazenamento do Microsoft Azure dedicado para Hadoop

Um dos métodos de acesso primário para dados na versão prévia do Azure Data Lake Store Gen2 é por meio do [sistema de arquivos Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). O Azure Data Lake Store Gen2 apresenta um driver associado, o driver de Sistema de Arquivos de Blob do Azure ou `ABFS`. O ABFS faz parte do Apache Hadoop e está incluído em muitas das distribuições comerciais do Hadoop. Usando esse driver, muitos aplicativos e estruturas podem acessar dados no Data Lake Store Gen2 sem nenhum código referenciando explicitamente o serviço Data Lake Store Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Recurso anterior: driver do Azure Storage Blob do Windows

O driver de Blob de Armazenamento do Windows Azure ou [driver WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) forneceu suporte original para Blobs de Armazenamento do Azure. Esse driver executou a tarefa complexa de mapear a semântica do sistema de arquivos (conforme requerido pela interface do Hadoop FileSystem) para aquela da interface de estilo de armazenamento de objeto exposta pelo Armazenamento de Blobs. Esse driver continua dando suporte a esse modelo, fornecendo acesso de alto desempenho aos dados armazenados nos Blobs, mas contém uma quantidade significativa de código que realiza esse mapeamento, dificultando a manutenção. Além disso, algumas operações como [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) quando aplicadas a diretórios exigem que o driver realize um vasto número de operações (devido à falta de suporte para diretórios), o que geralmente leva a um desempenho degradado.

Desse modo, para resolver as deficiências de design inerentes do WASB, o novo serviço de armazenamento de dados do Azure Data Lake foi implementado com o suporte do novo driver ABFS.

## <a name="the-azure-blob-file-system-driver"></a>Driver de Sistema de Arquivos de Blob do Azure

A [interface REST do Azure Data Lake Store](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2) é projetada para dar suporte à semântica do sistema de arquivos no Armazenamento de Blobs. Como o Hadoop FileSystem também é projetado para dar suporte à mesma semântica, não há necessidade de um mapeamento complexo no driver. Portanto, o driver de sistema de Arquivos de Blob do Azure (ou ABFS) é um shim de cliente simples para a API REST.

No entanto, há algumas funções que o driver ainda deve executar:

### <a name="uri-scheme-to-reference-data"></a>Esquema de URI para referência de dados

Consistente com outras implementações do FileSystem no Hadoop, o driver ABFS define o próprio esquema de URI para que os recursos (diretórios e arquivos) possam ser abordados de maneira distinta. O esquema de URI está documentado em [Usar o URI do Azure Data Lake Store Gen2](./introduction-abfs-uri.md). A estrutura do URI é: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Usando o formato de URI acima, as ferramentas e estruturas padrão do Hadoop podem ser utilizadas para referenciar esses recursos:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internamente, o driver ABFS converte os recursos especificados no URI em arquivos e diretórios e faz chamadas à API REST do Azure Data Lake Store com essas referências.

### <a name="authentication"></a>Autenticação

O driver ABFS atualmente dá suporte à autenticação de chave compartilhada para que o aplicativo Hadoop possa acessar com segurança os recursos contidos no Data Lake Store Gen2. A chave é criptografada e armazenada na configuração do Hadoop.

### <a name="configuration"></a>Configuração

Todas as configurações para o driver ABFS são armazenadas no arquivo de configuração <code>core-site.xml</code>. Nas distribuições do Hadoop que caracterizam [Ambari](http://ambari.apache.org/), a configuração também pode ser gerenciada usando o portal da Web ou a API REST do Ambari.

Os detalhes de todas as entradas de configuração com suporte são especificados na [Documentação oficial do Hadoop](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Documentação do Hadoop

O driver ABFS está totalmente documentado na [Documentação oficial do Hadoop](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Próximas etapas

- [Configurar clusters HDInsight](./quickstart-create-connect-hdi-cluster.md)
- [Criar um cluster do Azure Databricks](./quickstart-create-databricks-account.md)
- [Usar o URI do Azure Data Lake Store Gen2](./introduction-abfs-uri.md)
