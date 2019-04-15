---
title: Executar tarefas do Apache Sqoop com o Azure HDInsight (Apache Hadoop)
description: Saiba como usar o Azure PowerShell em uma estação de trabalho para executar importação e exportação do Sqoop entre um cluster do Hadoop e um Banco de Dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565845"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Usar Apache Sqoop com o Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop no HDInsight para importar e exportar dados entre um cluster do HDInsight e um banco de dados SQL do Azure.

Embora o Apache Hadoop é uma opção natural para processar dados não estruturados e semiestruturados, como logs e arquivos, também pode ser necessário processar dados estruturados armazenados em bancos de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta desenvolvida para transferir dados entre clusters de Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Apache Hive e, em seguida, exportar os dados de volta para um RDBMS. Neste artigo, você está usando um banco de dados do SQL Server do banco de dados relacional.

> [!IMPORTANT]  
> Este artigo define o ambiente de teste para executar a transferência de dados. Escolha um método de transferência de dados para esse ambiente de um dos métodos na seção [trabalhos do Sqoop executar](#run-sqoop-jobs), mais adiante.

Para as versões do Sqoop em clusters de HDInsight com suporte, consulte [o que há de novo nas versões de clusters fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

O cluster HDInsight é fornecido com alguns dados de exemplo. Você usa estas duas amostras:

* Um arquivo de log Log4j do Apache, que está localizado em `/example/data/sample.log`. Os seguintes logs são extraídos do arquivo:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela de Hive denominada `hivesampletable`, que faz referência ao arquivo de dados localizado em `/hive/warehouse/hivesampletable`. A tabela contém alguns dados de dispositivo móvel.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | clientid |cadeia de caracteres |
  | querytime |cadeia de caracteres |
  | market |cadeia de caracteres |
  | deviceplatform |cadeia de caracteres |
  | devicemake |cadeia de caracteres |
  | devicemodel |cadeia de caracteres |
  | state |cadeia de caracteres |
  | country |cadeia de caracteres |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

Neste artigo, você pode usar esses dois conjuntos de dados para testar o Sqoop para importar e exportar.

## <a name="create-cluster-and-sql-database"></a>Configurar o ambiente de teste
O cluster, o banco de dados SQL e outros objetos são criados por meio do portal do Azure usando um modelo do Azure Resource Manager. O modelo pode ser encontrado no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Resource Manager chama um pacote de bacpac para implantar os esquemas de tabela em um banco de dados SQL.  O pacote bacpac está localizado em um contêiner de blob público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se você quiser usar um contêiner particular para os arquivos bacpac, use os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar usando um modelo ou o Portal do Azure é compatível apenas com a importação de um arquivo BACPAC do Armazenamento de Blobs do Azure.

1. Selecione a imagem a seguir para abrir o modelo do Resource Manager no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Insira as seguintes propriedades:

    |Campo |Valor |
    |---|---|
    |Assinatura |Selecione sua assinatura do Azure na lista suspensa.|
    |Grupo de recursos |Selecione o grupo de recursos na lista suspensa, ou crie um novo|
    |Local padrão |Selecione uma região na lista suspensa.|
    |Nome do cluster |Insira um nome para o cluster Hadoop. Use apenas a letra minúscula.|
    |Nome de usuário de logon do cluster |Mantenha o valor preenchido previamente `admin`.|
    |Senha de logon do cluster |Digite uma senha.|
    |Nome de usuário SSH |Mantenha o valor preenchido previamente `sshuser`.|
    |SSH senha |Digite uma senha.|
    |Logon de administrador do SQL |Mantenha o valor preenchido previamente `sqluser`.|
    |Senha de administrador do SQL |Digite uma senha.|
    |local de_artifacts | Use o valor padrão, a menos que você deseja usar seu próprio arquivo bacpac em um local diferente.|
    |Token de Sas do local de_artifacts |Deixe em branco.|
    |Nome do arquivo Bacpac |Use o valor padrão, a menos que você deseja usar seu próprio arquivo bacpac.|
    |Local padrão |Use o valor padrão.|

    O nome do servidor SQL do Azure será `<ClusterName>dbserver`. O nome do banco de dados será `<ClusterName>db`. O nome de conta de armazenamento padrão será `e6qhezrh2pdqu`.

3. Selecione **Concordo com os termos e as condições declarados acima**.

4. Selecione **Comprar**. Você poderá ver um novo bloco intitulado Como enviar a implantação para a implantação do modelo. É preciso sobre cerca de 20 minutos para criar o cluster e o banco de dados SQL.

## <a name="run-sqoop-jobs"></a>Executar trabalhos do Sqoop

O HDInsight pode executar trabalhos do Sqoop usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [SDK .NET para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (por enquanto) |
| [PowerShell do Azure](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? | Windows |

## <a name="limitations"></a>Limitações

* Exportação em massa — com HDInsight baseado em Linux, o conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure, atualmente, não permite inserções em massa.
* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop realizará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximos passos
Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Hive com o HDInsight](../hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](../hdinsight-use-pig.md)
* [Carregar dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para fazer upload de dados para HDInsight/Azure Storage Blob.
