---
title: Usar ADLS (Azure Data Lake Storage) Gen2 com Apache Hadoop no Azure HDInsight
description: Fornece uma visão geral do Azure Data Lake Storage Gen2 e como ele trabalha com Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 2ae11afe1ecbe500a4851aab6d56e612fbe79ee6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976091"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Usar Azure Data Lake Storage Gen2 com Apache Hadoop no Azure HDInsight

O ADLS (Azure Data Lake Storage) Gen2 usa os principais recursos do Azure Data Lake Storage Gen1, como um sistema de arquivos compatível com Hadoop, Azure Active Directory e ACLs com baseada em POSIX, e os integra ao Armazenamento de Blobs do Azure. Essa combinação permite usufruir do desempenho do Azure Data Lake Storage Gen1, ao mesmo tempo em que usa o gerenciamento do ciclo de vida de dados e camada do Armazenamento de Blobs.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade principal do Azure Data Lake Storage Gen2

- Acesso compatível com Hadoop: O Azure Data Lake Storage Gen2 permite gerenciar e acessar dados como faria com um HDFS (Sistema de Arquivos Distribuído) do Hadoop. O driver ABFS está disponível em todos os ambientes do Apache Hadoop, incluindo o Azure HDInsight e Azure Databricks para acessar dados armazenados no Data Lake Storage Gen2.

- Um superconjunto de permissões POSIX: O modelo de segurança do Data Lake Gen2 dá suporte a permissões POSIX e ACL juntamente com alguma granularidade adicional específica para o Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas administrativas ou estruturas como Apache Hive e Apache Spark.

- Custo efetivo: O Data Lake Storage Gen2 oferece transações e capacidade de armazenamento de baixo custo. Recursos como o ciclo de vida de armazenamento de Blobs do Azure ajudam a reduzir os custos, ajustando as taxas de cobrança conforme a movimentação de dados no ciclo de vida.

- Funciona com aplicativos, estruturas e ferramentas de armazenamento de Blob: O Data Lake Storage Gen2 continua funcionando com uma ampla matriz de ferramentas, estruturas e aplicativos que atualmente existem para armazenamento de Blobs.

- Driver otimizado: O driver ABFS é otimizado especificamente para análise de big data. As APIs REST correspondentes são exibidas por meio do ponto de extremidade de dfs, dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>O que há de novo no Azure Data Lake Storage Gen2

### <a name="managed-identities-for-secure-file-access"></a>Identidades gerenciadas para acesso de arquivo seguro

O HDInsight do Azure usa identidades gerenciadas para proteger o acesso do cluster a arquivos no Azure Data Lake Storage Gen2. As identidades gerenciadas são um recurso do Azure Active Directory que fornece aos serviços do Azure um conjunto de credenciais gerenciadas automaticamente. Essas credenciais podem ser usadas para autenticar qualquer serviço com suporte para autenticação do AD. O uso de identidades gerenciadas não exige que você armazene credenciais em código ou arquivos de configuração.

Para obter mais informações, consulte [O que são identidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Driver ABFS (sistema de arquivos de Blob do Azure)

Os aplicativos do Apache Hadoop esperam nativamente ler e gravar dados do armazenamento em disco local. Um driver do sistema de arquivos do Hadoop, como o ABFS, permite que os aplicativos do Hadoop funcionem com o armazenamento em nuvem, emulando operações do sistema de arquivos regulares do Hadoop para o aplicativo. Em seguida, o driver converte essas operações em operações que a plataforma real de armazenamento em nuvem reconhece.

Anteriormente, o driver do sistema de arquivos do Hadoop converteria todas as operações do sistema de arquivos para chamadas à API REST do Armazenamento do Azure no lado do cliente e, em seguida, chamaria a API REST. Essa conversão do lado do cliente, no entanto, resultou em várias chamadas à API REST para uma única operação do sistema de arquivos, como uma renomeação de arquivo. O ABFS transferiu parte da lógica do sistema de arquivos do Hadoop do lado do cliente para o lado do servidor, e a API do ADLS Gen2 agora é executada em paralelo com a API do Blob. Essa migração melhora o desempenho porque agora as operações comuns do sistema de arquivos Hadoop podem ser executadas com uma chamada à API REST.

Para obter mais informações, consulte [O driver ABFS (sistema de arquivos de Blob do Azure): Um driver de Armazenamento do Azure dedicado para Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>Esquema de URI do ADLS Gen 2

O ADLS Gen2 usa um novo esquema de URI para acessar arquivos no armazenamento do Azure a partir do HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

O esquema de URI fornece acesso criptografado SSL (prefixo `abfss://`) e acesso não criptografado (prefixo `abfs://`). É recomendável usar `abfss` sempre que possível, mesmo ao acessar dados que residam na mesma região do Azure.

`<FILE_SYSTEM_NAME>` identifica o caminho do sistema de arquivos Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifica o nome da conta do Armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.

O `<PATH>` é o nome do caminho de HDFS do arquivo ou diretório.

Se os valores para `<FILE_SYSTEM_NAME>` e `<ACCOUNT_NAME>` não forem especificados, será usado o sistema de arquivos padrão. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo `hadoop-mapreduce-examples.jar` que vem com clusters HDInsight pode ser referido usando um dos caminhos a seguir:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> O nome do arquivo é `hadoop-examples.jar` nos clusters HDInsight versões 2.1 e 1.6. Ao trabalhar com arquivos fora do HDInsight, a maioria dos utilitários não reconhecem o formato ABFS e, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.

Para obter mais informações, consulte [Usar URI do Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>Próximas etapas
- [Introdução ao Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md)
- [Usar a versão prévia do Azure Data Lake Storage Gen2 com clusters HDInsight do Azure](../storage/data-lake-storage/use-hdi-cluster.md)