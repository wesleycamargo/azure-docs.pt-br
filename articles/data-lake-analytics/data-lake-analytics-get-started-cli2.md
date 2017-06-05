---
title: "Introdução ao Azure Data Lake Analytics usando a CLI do Azure 2.0 | Microsoft Docs"
description: 'Saiba como usar a interface de linha de comando do Azure 2.0 para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics usando U-SQL e enviar o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3f957bda2c38bdeac3ee6b0dbd94fac497c8f7cf
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Tutorial: introdução ao Azure Data Lake Analytics usando a CLI do Azure 2.0
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar a CLI do Azure 2.0 para criar contas do Azure Data Lake Analytics, definir trabalhos do Data Lake Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para contas do Data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

Neste tutorial, você desenvolverá um trabalho que lê um arquivo TSV (Valores Separados por Tabulação) e irá convertê-lo em um arquivo CSV (Valores Separados por Vírgula). Para acompanhar o mesmo tutorial usando outras ferramentas compatíveis,use a lista suspensa na parte superior desta seção.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI 2.0 do Azure**. Consulte [Instalar e configurar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para entrar na sua assinatura do Azure:

```azurecli
az login
```

Você precisará navegar até uma URL e inserir um código de autenticação.  E siga as instruções para inserir suas credenciais.

Uma vez feito o logon, o comando de logon listará suas assinaturas.

Para usar uma assinatura específica:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake
Você precisa ter uma conta do Data Lake Analytics antes de executar trabalhos. Para criar uma conta do Data Lake Analytics, você deve especificar os seguintes itens:

* **Grupo de recursos do Azure**. É necessário criar uma conta do Data Lake Analytics em um grupo de Recursos do Azure. O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.  

    Para listar os grupos de recursos existentes em sua assinatura:

    ```azurecli
    az group list
    ```

    Para criar um novo grupo de recursos:

    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Nome da conta do Data Lake Analytics**. Cada conta do Data Lake Analytics tem um nome.
* **Local**. Use um dos datacenters do Azure que dá suporte ao Data Lake Analytics.
* **Conta padrão do Data Lake Store**: cada conta do Data Lake Analytics tem uma conta padrão do Data Lake Store.

    Para listar a conta existente do Data Lake Store:

    ```azurecli
    az dls account list
    ```

    Para criar uma nova conta do Azure Data Lake Store:

    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Use o seguinte código para criar uma conta do Data Lake Analytics:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Depois de criar uma conta, você pode usar os seguintes comandos para listar as contas e mostrar seus detalhes:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Carregar dados no Repositório Data Lake
Neste tutorial, você processa alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no Repositório Data Lake ou no Armazenamento de Blob do Azure.

O portal do Azure fornece uma interface do usuário para copiar alguns arquivos de dados de exemplo para a conta padrão do Data Lake Store, o que inclui um arquivo de log de pesquisa. Consulte [Preparar dados de origem](data-lake-analytics-get-started-portal.md) para carregar os dados na conta do Repositório Data Lake.

Para carregar arquivos usando a CLI 2.0, use o seguinte comando:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

A Análise Data Lake também pode acessar o armazenamento de Blob do Azure.  Para carregar dados no Armazenamento de Blob do Azure, consulte [Usando a CLI do Azure com o Armazenamento do Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Enviar trabalhos da Análise Data Lake
Os trabalhos da Análise Data Lake são escritos na linguagem U-SQL. Para saber mais sobre o U-SQL, confira [Introdução à linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) e [Referência da linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de trabalho da Análise Data Lake**

Crie um arquivo de texto com o seguinte script U-SQL e salve o arquivo de texto em sua estação de trabalho:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();

Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()**, em seguida, cria um arquivo csv usando **Outputters.Csv()**.

Não modifique os dois caminhos, a menos que você copie o arquivo de origem para um local diferente.  A Análise Data Lake criará a pasta de saída se ela não existir.

É mais simples usar caminhos relativos para arquivos armazenados em contas padrão do Data Lake Store. Você também pode usar caminhos absolutos.  Por exemplo:

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

Você deve usar caminhos absolutos para acessar os arquivos em contas do Armazenamento vinculadas.  A sintaxe para os arquivos armazenados na conta do Armazenamento do Azure vinculada é:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Atualmente, não há suporte para o contêiner de Blob do Azure com permissões de acesso de blobs públicos ou de contêineres públicos.      
  >
  >

**Para enviar trabalhos**

Use a sintaxe a seguir para enviar um trabalho.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Por exemplo:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Para listar trabalhos e mostrar detalhes do trabalho**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Para cancelar trabalhos**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Recuperar os resultados do trabalho

Depois que um trabalho é concluído, use os seguintes comandos para listar os arquivos de saída e baixá-los:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Por exemplo:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Próximas etapas

* Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
* Para ver o documento de referência da CLI 2.0 do Data Lake Analytics, consulte[Data Lake Analytics - az dla](https://docs.microsoft.com/cli/azure/dla).
* Para ver o documento de referência da CLI 2.0 do Data Lake Store, consulte[Data Lake Store - az dls](https://docs.microsoft.com/cli/azure/dls).
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).
* Para obter uma visão geral da Análise Data Lake, consulte [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

