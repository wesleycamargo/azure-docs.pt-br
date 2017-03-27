---
title: "Introdução ao Azure Data Lake Analytics usando a Interface de linha de comando do Azure | Microsoft Docs"
description: 'Saiba como usar a interface de linha de comando do Azure para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics usando U-SQL e enviar o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4db7d45678c592749831c6b12d38363134da9e93
ms.lasthandoff: 03/21/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Tutorial: Introdução à Análise Azure Data Lake usando o a CLI (interface de linha de comando) do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar a CLI do Azure para criar contas da Análise Azure Data Lake, definir trabalhos de Análise Data Lake em [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para contas da Análise Data Lake. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

Neste tutorial, você desenvolverá um trabalho que lê um arquivo TSV (valores separados por tabulação) e o converte em um arquivo CSV (valores separados por vírgulas). Para acompanhar o mesmo tutorial usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do Azure**. Consulte [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
  * Baixe e instale o **pré-lançamento das** [ferramentas de CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.
* **Autenticação**, usando o seguinte comando:
  
        azure login
    Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, veja [Conectar-se a uma assinatura do Azure da CLI do Azure](../xplat-cli-connect.md).
* **Alterne para o modo Gerenciador de Recursos do Azure**usando o seguinte comando:
  
        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake
Você deve ter uma conta da Análise Data Lake antes de executar trabalhos. Para criar uma conta da Análise Data Lake, você deve especificar o seguinte:

* **Grupo de Recursos do Azure**: é necessário criar uma conta da Análise Data Lake em um grupo de Recursos do Azure. O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.  
  
    Para enumerar os grupos de recursos em sua assinatura:
  
        azure group list 
  
    Para criar um novo grupo de recursos:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nome da conta da Análise Data Lake**
* **Local**: um dos datacenters do Azure que dá suporte à Análise Data Lake.
* **Conta padrão do Data Lake**: cada conta da Análise Data Lake tem uma conta padrão do Data Lake.
  
    Para listar a conta existente do Data Lake:
  
        azure datalake store account list
  
    Para criar uma nova conta do Data Lake:
  
        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"
  
  > [!NOTE]
  > O nome da conta do Data Lake deve conter apenas letras minúsculas e números.
  > 
  > 

**Para criar uma conta da Análise Data Lake**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Conta de exibição da Análise Data Lake](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> O nome da conta da Análise Data Lake deve conter apenas letras minúsculas e números.
> 
> 

## <a name="upload-data-to-data-lake-store"></a>Carregar dados no Repositório Data Lake
Neste tutorial, você processará alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no Repositório Data Lake ou no Armazenamento de Blob do Azure. 

O Portal do Azure fornece uma interface do usuário para copiar alguns arquivos de dados de exemplo na conta padrão do Data Lake, o que inclui um arquivo de log de pesquisa. Consulte [Preparar dados de origem](data-lake-analytics-get-started-portal.md#prepare-source-data) para carregar os dados na conta do Repositório Data Lake.

Para carregar arquivos usando a CLI, use o seguinte comando:

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

A Análise Data Lake também pode acessar o armazenamento de Blob do Azure.  Para carregar dados no Armazenamento de Blob do Azure, consulte [Usando a CLI do Azure com o Armazenamento do Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Enviar trabalhos da Análise Data Lake
Os trabalhos da Análise Data Lake são escritos na linguagem U-SQL. Para saber mais sobre o U-SQL, confira [Introdução à linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) e [Referência da linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de trabalho da Análise Data Lake**

* Crie um arquivo de texto com o seguinte script U-SQL e salve o arquivo de texto em sua estação de trabalho:
  
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
  
    É mais simples usar caminhos relativos para arquivos armazenados em contas padrão do Data Lake. Você também pode usar caminhos absolutos.  Por exemplo, 
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    Você deve usar caminhos absolutos para acessar os arquivos em contas do Armazenamento vinculadas.  A sintaxe para os arquivos armazenados na conta do Armazenamento do Azure vinculada é:
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Atualmente, não há suporte para o contêiner de Blob do Azure com permissões de acesso de blobs públicos ou de contêineres públicos.      
  > 
  > 

**Para enviar o trabalho**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


Os seguintes comandos podem ser usados para listar trabalhos, obter detalhes de trabalhos e cancelar trabalhos:

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

Depois que o trabalho for concluído, você poderá usar os seguintes cmdlets para listar o arquivo e baixá-lo:

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Confira também
* Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, veja [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md).
* Para obter uma visão geral da Análise do Data Lake, veja [Visão geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).


