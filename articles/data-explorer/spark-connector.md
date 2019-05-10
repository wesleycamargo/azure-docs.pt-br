---
title: Use o conector do Gerenciador de dados do Azure para o Apache Spark para mover dados entre clusters de Spark e o Data Explorer do Azure.
description: Este tópico mostra como mover dados entre clusters do Apache Spark e do Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441358"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Conector do Gerenciador de dados do Azure para o Apache Spark (visualização)

[Apache Spark](https://spark.apache.org/) é um mecanismo de análise unificado para processamento de dados em larga escala. Gerenciador de dados do Azure é um serviço de análise de dados rápido, totalmente gerenciado para análise em tempo real em grandes volumes de dados. 

Conector do Gerenciador de dados do Azure para Spark implementa a fonte de dados e o coletor de dados para mover dados entre clusters de Spark e o Data Explorer do Azure para usar os dois de seus recursos. Usando o Azure Data Explorer e o Apache Spark, você pode criar aplicativos rápidos e escalonáveis, direcionando os dados controlados por cenários, como o aprendizado de máquina (ML), extração, transformação e carregamento (ETL) e Log Analytics. Gravação no Data Explorer do Azure pode ser feita no lote e o modo de streaming.
Leitura do Data Explorer do Azure dá suporte à remoção de coluna e a aplicação de predicado, que reduz o volume de dados transferidos pela filtragem de dados no Data Explorer do Azure.

O conector Spark do Gerenciador de dados do Azure é um [projeto de software livre](https://github.com/Azure/azure-kusto-spark) que podem ser executados em qualquer cluster Spark.

> [!NOTE]
> Embora alguns dos exemplos a seguir se referir a um [Azure Databricks](https://docs.azuredatabricks.net/) cluster Spark, o conector Spark de Gerenciador de dados do Azure não tem dependências diretas em Databricks ou qualquer outra distribuição do Spark.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster do Azure Data Explorer e o banco de dados](/azure/data-explorer/create-cluster-database-portal) 
* Criar um cluster Spark
* Instalar a biblioteca do conector Data Explorer do Azure e bibliotecas listadas na [dependências](https://github.com/Azure/azure-kusto-spark#dependencies) incluindo o seguinte [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) bibliotecas:
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Cliente de ingestão do Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Pré-compilado bibliotecas para [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Como criar o conector do Spark

Conector do Spark pode ser criado a partir [fontes](https://github.com/Azure/azure-kusto-spark) conforme detalhado abaixo.

> [!NOTE]
> Esta etapa é opcional. Se você estiver usando bibliotecas criadas previamente acesse [instalação de cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Pré-requisitos de build

* SDK do Java 1.8 instalado
* [Maven 3. x](https://maven.apache.org/download.cgi) instalado
* Versão do Apache Spark 2.4.0 ou superior

> [!TIP]
> 2.3 versões também têm suporte, mas podem exigir algumas alterações em dependências de POM. XML.

Para aplicativos do Java/Scala usando as definições do projeto Maven, vincule seu aplicativo com o seguinte artefato (versão mais recente pode ser diferente):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Criar comandos

Para criar o jar e executar todos os testes:

```
mvn clean package
```

Para criar o jar, execute todos os testes e instale o jar ao seu repositório Maven local:

```
mvn clean install
```

Para obter mais informações, consulte [uso do conector](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Instalação de cluster Spark

> [!NOTE]
> É recomendável usar a versão mais recente do conector Spark de Gerenciador de dados do Azure ao executar as seguintes etapas:

1. Defina configurações de cluster, com base em um cluster do Azure Databricks usando o Spark 2.4 e Scala 2.11 de Spark seguinte: 

    ![Configurações de cluster do Databricks](media/spark-connector/databricks-cluster.png)

1. Importe a biblioteca de conector do Gerenciador de dados do Azure:

    ![Importar a biblioteca de Data Explorer do Azure](media/spark-connector/db-create-library.png)

1. Adicione dependências adicionais:

    ![Adicionar dependências](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > A versão correta do java para cada versão do Spark for encontrada [aqui](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Verifique se todas as necessárias bibliotecas são instaladas:

    ![Verifique se a bibliotecas instaladas](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Conector do Spark do Gerenciador de dados do Azure permite que você autentique com o Azure Active Directory (Azure AD) usando um [aplicativo do Azure AD](#azure-ad-application-authentication), [token de acesso do Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [autenticação de dispositivo ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (para cenários de não produção), ou [do Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). O usuário deve instalar o pacote do azure-keyvault e fornecer as credenciais de aplicativo para acessar o recurso de Cofre de chaves.

### <a name="azure-ad-application-authentication"></a>Autenticação de aplicativo do Azure AD

Método de autenticação simples e comuns a maioria dos. Esse método é recomendado para uso do conector Spark do Azure Data Explorer.

|propriedades  |Descrição  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificador do aplicativo (cliente) do AD do Azure.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autoridade de autenticação do AD do Azure. ID de diretório do AD (Locatário) do Azure.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chave do aplicativo do Azure AD para o cliente.     |

### <a name="azure-data-explorer-privileges"></a>Privilégios do Gerenciador de dados do Azure

Os seguintes privilégios devem ser concedidos em um Cluster do Gerenciador de dados do Azure:

* Para ler (fonte de dados), o aplicativo do Azure AD deve ter *viewer* privilégios no banco de dados de destino, ou *admin* privilégios na tabela de destino.
* Para gravar (coletor de dados), o aplicativo do Azure AD deve ter *ingestor* privilégios no banco de dados de destino. Ela também deve ter *usuário* privilégios no banco de dados de destino para criar novas tabelas. Se a tabela de destino já existir, *admin* privilégios na tabela de destino podem ser configurados.
 
Para obter mais informações sobre funções de entidade do Data Explorer do Azure, consulte [autorização baseada em função](/azure/kusto/management/access-control/role-based-authorization). Para gerenciar funções de segurança, consulte [gerenciamento de funções de segurança](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Coletor do Spark: Gravar dados do Azure Explorer

1. Configure parâmetros do coletor:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Gravação DataFrame do Spark para cluster do Gerenciador de dados do Azure como lote:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Gravar dados de streaming:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Fonte do Spark: Leitura de dados do Azure Explorer

1. Durante a leitura de pequenas quantidades de dados, defina a consulta de dados:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Ao ler grandes quantidades de dados, armazenamento de BLOBs transitório deve ser fornecido. Forneça a chave SAS do contêiner de armazenamento, ou nome da conta de armazenamento, chave de conta e nome do contêiner. Essa etapa só é necessária para a versão prévia atual do conector do Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    No exemplo acima, podemos não acessar o Cofre de chaves usando a interface do conector. Como alternativa, podemos usar um método mais simples de usar os segredos do Databricks.

1. Ler do Gerenciador de dados do Azure:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
