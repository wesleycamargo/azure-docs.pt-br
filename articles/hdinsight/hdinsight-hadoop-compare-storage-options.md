---
title: Comparar opções de armazenamento para uso com clusters do Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como eles funcionam com o Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 7f113587dfabd66461a9bcfbde18a0178c6608f0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733538"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparar opções de armazenamento para uso com clusters do Azure HDInsight

Os usuários do Azure HDInsight podem escolher entre várias opções de armazenamento diferentes durante a criação de clusters do HDInsight:

* Azure Data Lake Storage Gen2
* Armazenamento do Azure
* Azure Data Lake Storage Gen1

Este artigo fornece uma visão geral desses tipos de armazenamento diferentes e de seus recursos exclusivos.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Usar Azure Data Lake Storage Gen2 com Apache Hadoop no Azure HDInsight

Para obter mais informações sobre o Azure Data Lake Storage Gen2, confira [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

O Azure Data Lake Storage Gen2 usa os principais recursos do Azure Data Lake Storage Gen1, como um sistema de arquivos compatível com Hadoop, Azure Active Directory e ACLs (listas de controle de acesso) baseadas em POSIX, e os integra ao Armazenamento de Blobs do Azure. Essa combinação permite usufruir do desempenho do Azure Data Lake Storage Gen1, ao mesmo tempo em que usa o gerenciamento do ciclo de vida de dados e camada do Armazenamento de Blobs.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade principal do Azure Data Lake Storage Gen2

* Acesso compatível com Hadoop: O Azure Data Lake Storage Gen2 permite gerenciar e acessar dados como faria com um HDFS (Sistema de Arquivos Distribuído) do Hadoop. O driver ABFS (Sistema de arquivos do Blob do Azure) está disponível em todos os ambientes do Apache Hadoop, incluindo o Azure HDInsight e o Azure Databricks, para acessar dados armazenados no Data Lake Storage Gen2.

* Um superconjunto de permissões POSIX: O modelo de segurança do Data Lake Gen2 dá suporte a permissões POSIX e ACL juntamente com alguma granularidade adicional específica para o Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas administrativas ou estruturas como Apache Hive e Apache Spark.

* Custo efetivo: O Data Lake Storage Gen2 oferece transações e capacidade de armazenamento de baixo custo. Recursos como o ciclo de vida de armazenamento de Blobs do Azure ajudam a reduzir os custos, ajustando as taxas de cobrança conforme a movimentação de dados no ciclo de vida.

* Funciona com aplicativos, estruturas e ferramentas de armazenamento de Blob: O Data Lake Storage Gen2 continua funcionando com uma ampla matriz de ferramentas, estruturas e aplicativos que atualmente existem para armazenamento de Blobs.

* Driver otimizado: O driver ABFS é otimizado especificamente para análise de big data. As APIs REST correspondentes são exibidas por meio do ponto de extremidade de dfs, dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>O que há de novo no Azure Data Lake Storage Gen2

#### <a name="managed-identities-for-secure-file-access"></a>Identidades gerenciadas para acesso de arquivo seguro

O HDInsight do Azure usa identidades gerenciadas para proteger o acesso do cluster a arquivos no Azure Data Lake Storage Gen2. As identidades gerenciadas são um recurso do Azure Active Directory que fornece aos serviços do Azure um conjunto de credenciais gerenciadas automaticamente. Essas credenciais podem ser usadas para autenticar qualquer serviço com suporte para autenticação do AD. O uso de identidades gerenciadas não exige que você armazene credenciais em código ou arquivos de configuração.

Para obter mais informações, consulte [O que são identidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Driver ABFS (sistema de arquivos de Blob do Azure)

Os aplicativos do Apache Hadoop esperam nativamente ler e gravar dados do armazenamento em disco local. Um driver do sistema de arquivos do Hadoop, como o ABFS, permite que os aplicativos do Hadoop funcionem com o armazenamento em nuvem, emulando operações do sistema de arquivos regulares do Hadoop. O driver converte esses comandos recebidos do aplicativo em operações entendidas pela plataforma de armazenamento de nuvem verdadeira.

Anteriormente, o driver do sistema de arquivos do Hadoop converteria todas as operações do sistema de arquivos para chamadas à API REST do Armazenamento do Azure no lado do cliente e, em seguida, chamaria a API REST. Essa conversão do lado do cliente, no entanto, resultou em várias chamadas à API REST para uma única operação do sistema de arquivos, como uma renomeação de arquivo. O ABFS transferiu parte da lógica do sistema de arquivos do Hadoop do lado do cliente para o lado do servidor, e a API do Azure Data Lake Storage Gen2 agora é executada em paralelo com a API do Blob. Essa migração melhora o desempenho porque agora as operações comuns do sistema de arquivos Hadoop podem ser executadas com uma chamada à API REST.

Para obter mais informações, consulte [O driver ABFS (sistema de arquivos de Blob do Azure): Um driver de Armazenamento do Azure dedicado para Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Esquema de URI do Azure Data Lake Storage Gen 2

O Azure Data Lake Storage Gen2 usa um novo esquema de URI para acessar arquivos no Armazenamento do Azure a partir do HDInsight:

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

Para obter mais informações, consulte [Usar URI do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Usar armazenamento do Azure

O Armazenamento do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. O HDInsight pode usar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão para o cluster. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados estruturados ou não estruturados armazenados como blobs.

> [!WARNING]  
> Há várias opções disponíveis ao criar uma conta de Armazenamento do Azure. A tabela a seguir fornece informações sobre quais opções têm suporte com o HDInsight:

| Tipo de conta de armazenamento | Serviços com suporte | Níveis de desempenho compatíveis | Camadas de acesso compatíveis |
|----------------------|--------------------|-----------------------------|------------------------|
| Uso geral V2   | Blob               | Standard                    | Frequente, Esporádico, Arquivos*    |
| Uso geral V1   | Blob               | Standard                    | N/D                    |
| Armazenamento de blob         | Blob               | Standard                    | Frequente, Esporádico, Arquivos*    |

Não recomendamos o contêiner de blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de blobs padrão após cada uso para reduzir o custo de armazenamento. O contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

Não há suporte para o uso de um contêiner de blobs como o sistema de arquivos padrão para vários clusters.
 
 > [!NOTE]  
 > A camada de acesso aos arquivos é um tipo offline que tem uma latência de recuperação de várias horas e não é recomendada para uso com o HDInsight. Para saber mais, consulte [Camada de acesso aos arquivos](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight
O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight de uso do Armazenamento do Azure:

![Clusters Hadoop usam a API HDFS para acessar e armazenar dados estruturados e não estruturados no armazenamento de blobs.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "Arquitetura de Armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight permite que você acesse os dados armazenados no Armazenamento do Azure. A sintaxe do é:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Veja algumas considerações ao usar a conta de armazenamento do Azure com clusters HDInsight.

* **Contêineres nas contas de armazenamento conectadas a um cluster:** Como o nome e a chave da conta estão associados ao cluster durante a criação, você tem acesso total aos blobs nesses contêineres.

* **Contêineres públicos ou blobs públicos em contas de armazenamento que NÃO estão conectadas a um cluster:** Você tem permissão somente leitura para os blobs nos contêineres.
  
  > [!NOTE]  
  > Um contêiner público permite obter uma lista de todos os blobs disponíveis nesse contêiner e obter metadados do contêiner. Um blob público somente permite acessar os blobs se você souber a URL exata. Para obter mais informações, veja [Gerenciar o acesso a contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Contêineres privados em contas de armazenamento que NÃO estão conectadas a um cluster:** Não é possível acessar os blobs nos contêineres, a menos que você defina a conta de armazenamento ao enviar os trabalhos do WebHCat. Isso será explicado mais adiante neste artigo.

As contas de armazenamento definidas no processo de criação e suas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo core-site.xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo o Apache Hive, MapReduce, streaming do Apache Hadoop e o Apache Pig, podem conter uma descrição de contas de armazenamento e metadados. (Isso funciona atualmente com o Pig para contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser usados para dados estruturados e não estruturados. Os contêineres de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser `input/log1.txt`. Não existe nenhum diretório `input` real, mas, devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.

### <a id="benefits"></a>Benefícios do Armazenamento do Azure
O custo de desempenho implícito de não ter clusters de cálculo e recursos de armazenamento colocalizados é reduzido pela forma como os clusters de cálculo são criados próximos aos recursos da conta de armazenamento na região do Azure, no qual a rede de alta velocidade torna os nós de computação eficientes para acessarem os dados no Armazenamento do Azure.

Há vários benefícios associados ao armazenamento de dados no Armazenamento do Azure em vez de no HDFS:

* **Reutilização e compartilhamento de dados:** Os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no Armazenamento do Azure podem ser acessados por meio de APIs HDFS ou por meio de APIs REST do Armazenamento de Blobs. Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas podem ser usados para produzir e consumir os dados.
* **Arquivamento de dados:** Armazenar dados no armazenamento do Azure permite que os clusters HDInsight usados para computação sejam excluídos com segurança sem perder dados do usuário.
* **Custo de armazenamento de dados:** Armazenar dados no DFS a longo prazo é mais caro do que armazenar os dados no armazenamento do Azure porque o custo de um cluster de computação é maior que o custo do armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você está economizando em custos de carregamento de dados.
* **Expansão elástica:** Embora o HDFS forneça um sistema de arquivos dimensionado, a escala é determinada pelo número de nós que você cria para o cluster. A alteração da escala pode se tornar um processo mais complicado do que depender dos recursos de dimensionamento elástico do Armazenamento do Azure que você obtém automaticamente.
* **Replicação geográfica:** O armazenamento do Azure pode ser replicado geograficamente. Embora isso forneça redundância de dados e recuperação geográfica, um failover para o local replicado geograficamente afetará seriamente o desempenho e poderá incorrer em custos adicionais. Portanto, nossa recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Determinados trabalhos e pacotes do MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no contêiner de Armazenamento do Azure. Nesse caso, você ainda pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa o DFS para vários desses resultados intermediários em trabalhos Hive e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) ainda funciona conforme o esperado. Apenas os comandos específicos à implementação nativa do HDFS (que é conhecida como DFS), como `fschk` e `dfsadmin` mostram um comportamento diferente no Armazenamento do Azure.

## <a name="use-azure-data-lake-storage-gen1"></a>Usar o Azure Data Lake Storage Gen1

### <a name="overview"></a>Visão geral

Para saber mais sobre o Azure Data Lake Storage Gen1, confira [Visão geral do Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

O Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para cargas de trabalho analíticas de big data. O Azure Data Lake permite que você capture dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias.

O Data Lake Storage Gen1 pode ser acessado a partir do Hadoop (disponível com um cluster HDInsight) usando as APIs REST compatíveis com WebHDFS. Ele foi desenvolvido para permitir a análise dos dados armazenados e está ajustado para trabalhar em cenários de análise de dados. Ele já inclui todos os recursos corporativos — segurança, capacidade de gerenciamento, escalabilidade, confiabilidade e disponibilidade — essenciais para casos de uso reais.

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "Arquitetura de armazenamento do HDInsight")

Alguns dos principais recursos do Data Lake Storage Gen1 incluem o seguinte.

#### <a name="built-for-hadoop"></a>Desenvolvido para Hadoop

Data Lake Storage O Gen1 é um sistema de arquivos Apache Hadoop compatível com o Hadoop Distributed File System (HDFS) e funciona com o ecossistema Hadoop.  Seus aplicativos ou serviços existentes do HDInsight que usam a API WebHDFS podem ser facilmente integrados ao Data Lake Storage Gen1. O Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicativos

Os dados armazenados no Data Lake Storage Gen1 podem ser facilmente analisados usando estruturas analíticas do Hadoop, como MapReduce ou Hive. Os clusters do Microsoft Azure HDInsight podem ser provisionados e configurados para acessar diretamente os dados armazenados no Data Lake Storage Gen1.

#### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, arquivos em petabytes

O Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar uma variedade de dados para análise. Ele não impõe qualquer limite de tamanho de conta, de tamanho de arquivo ou de quantidade de dados que podem ser armazenados em um Data Lake. Os arquivos individuais podem variar de quilobytes a petabytes, tornando-os uma ótima opção para armazenar qualquer tipo de dados. Os dados são armazenados permanentemente por meio de várias cópias, e não há limite de tempo de armazenamento dos dados no Data Lake.

#### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho ajustado para a análise de big data

O Data Lake Storage Gen1 foi desenvolvido para executar sistemas analíticos de larga escala que exigem um throughput massivo para consultar e analisar grandes quantidades de dados. O Data Lake espalha partes de um arquivo por vários servidores de armazenamento individuais. Isso melhora a produtividade da leitura do arquivo em paralelo para a realização de análises de dados.

#### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto para empresas: altamente disponível e seguro

O Data Lake Storage Gen1 fornece disponibilidade e confiabilidade padrão do setor. Seus ativos de dados são armazenados permanentemente por meio de cópias redundantes, a fim de se proteger contra quaisquer falhas inesperadas. As empresas podem usar o Data Lake Storage Gen1 em suas soluções como parte importante de sua plataforma de dados existente.

O Data Lake Storage Gen1 também fornece segurança de nível corporativo para os dados armazenados. Para obter mais informações, consulte [Protegendo dados no Armazenamento de dados do Lake Azure Gen1](#DataLakeStoreSecurity).

#### <a name="all-data"></a>Todos os dados

O Data Lake Storage Gen1 pode armazenar qualquer dado em seu formato nativo, sem necessidade de transformações anteriores. O Data Lake Storage Gen1 não exige que um esquema seja definido antes de os dados serem carregados, deixando a estrutura analítica individual interpretar os dados e definir um esquema no momento da análise. A capacidade de armazenar arquivos de tamanhos e formatos arbitrários possibilita que o Data Lake Storage Gen1 manipule dados estruturados, semi-estruturados e não estruturados.

Os contêineres Data Lake Storage Gen1 para dados são essencialmente pastas e arquivos. Você opera nos dados armazenados usando SDKs, o portal do Azure e o Azure PowerShell. Desde que você coloque os dados no repositório usando essas interfaces e os contêineres apropriados, é possível armazenar qualquer tipo de dados. O Data Lake Storage Gen1 não executa nenhum tratamento especial de dados com base no tipo de dados que armazena.

### <a name="DataLakeStoreSecurity"></a> Protegendo dados no Data Lake Storage Gen1
O Data Lake Storage Gen1 usa o Azure Active Directory para autenticação e listas de controle de acesso (ACLs) para gerenciar o acesso aos seus dados.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Authentication |O Data Lake Storage Gen1 integra-se ao Azure Active Directory (AAD) para gerenciamento de identidade e acesso para todos os dados armazenados no Data Lake Storage Gen1. Como resultado da integração, o Data Lake Storage Gen1 se beneficia de todos os recursos do AAD, incluindo autenticação multifator, acesso condicional, controle de acesso baseado em função, monitoramento do uso do aplicativo, monitoramento e alerta de segurança, etc. O Data Lake Storage Gen1 suporta o protocolo OAuth 2.0 para autenticação na interface REST. Consulte [autenticação Gen1 de armazenamento do Data Lake](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controle de acesso |O Data Lake Storage Gen1 fornece controle de acesso, suportando permissões no estilo POSIX expostas pelo protocolo WebHDFS. As ACLs podem ser habilitadas na pasta raiz, nas subpastas e nos arquivos individuais. Para obter mais informações sobre como as ACLs funcionam no contexto do Data Lake Storage Gen1, consulte [Controle de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Criptografia |O Azure Data Lake Storage Gen1 também fornece criptografia para dados armazenados na conta. Você especifica as configurações de criptografia ao criar uma conta do Data Lake Storage Gen1. Você pode optar por ter seus dados criptografados ou optar por nenhuma criptografia. Para obter mais informações, consulte [Criptografia no Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer configuração relacionada à criptografia, consulte [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure ](../data-lake-store/data-lake-store-get-started-portal.md). |

Deseja saber mais sobre como proteger dados no Data Lake Storage Gen1? Siga os links abaixo.

* Para obter instruções sobre como proteger dados no Data Lake Storage Gen1, consulte [Protegendo dados no Armazenamento de dados do Azure Data Lake Gen1](../data-lake-store/data-lake-store-secure-data.md).

### <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplicativos compatíveis com o Data Lake armazenamento Gen1
O Azure Data Lake Storage Gen1 é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure.  Siga os links abaixo para saber mais sobre como o Data Lake Storage Gen1 pode ser usado tanto com componentes de software livre quanto com outros serviços do Azure.

* Confira [Aplicativos e serviços compatíveis com o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicativos de software livre interoperáveis com o Data Lake Storage Gen1.
* Consulte [Integração com outros serviços do Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para entender como o Data Lake Storage Gen1 pode ser usado com outros serviços do Azure para permitir uma gama mais ampla de cenários.
* Veja [Cenários para usar o Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) para aprender a usar o Data Lake Storage Gen1 em cenários como a ingestão de dados, o processamento de dados, o download de dados e a visualização de dados.

### <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>O que é o sistema de arquivos Data Lake Storage Gen1 (adl://)?
O Data Lake Storage Gen1 pode ser acessado por meio do novo sistema de arquivos, o AzureDataLakeFilesystem (adl: //), em ambientes do Hadoop (disponíveis com o cluster HDInsight). Os aplicativos e os serviços que usam adl:// podem aproveitar a otimização adicional de desempenho que não está disponível no momento no WebHDFS. Como resultado, o Data Lake Storage Gen1 oferece a flexibilidade de aproveitar o melhor desempenho com a opção recomendada de usar adl:// ou manter o código existente, continuando a usar a API WebHDFS diretamente. O Azure HDInsight aproveita totalmente o AzureDataLakeFilesystem para fornecer o melhor desempenho no Data Lake Storage Gen1.

Você pode acessar seus dados no Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Para obter mais informações sobre como acessar os dados no Data Lake Storage Gen1, consulte [Exibir propriedades dos dados armazenados](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)