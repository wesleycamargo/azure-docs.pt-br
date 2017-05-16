---
title: "Introdução ao Azure Data Lake Analytics usando o Azure PowerShell | Microsoft Docs"
description: 'Use o Azure PowerShell para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics usando o U-SQL e enviar o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: introdução à Análise Azure Data Lake usando o Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o Azure PowerShell para criar contas do Azure Data Lake Analytics e, em seguida, enviar e executar trabalhos do U-SQL. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter as seguintes informações:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Preparando-se para o tutorial
Para criar uma conta do Data Lake Analytics, você precisa primeiro definir:

* **Grupo de Recursos do Azure**: é necessário criar uma conta do Data Lake Analytics em um grupo de Recursos do Azure.
* **Nome da conta do Data Lake Analytics**: o nome da conta do Data Lake deve conter apenas letras minúsculas e números.
* **Local**: um dos datacenters do Azure que dá suporte à Análise Data Lake.
* **Conta padrão do Data Lake Store**: cada conta do Data Lake Analytics tem uma conta padrão do Data Lake Store. Essas contas devem estar no mesmo local.

Os trechos de código do PowerShell neste tutorial usam essas variáveis para armazenar essas informações

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake

Se você ainda não tiver um Grupo de Recursos para usar, crie um. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store usada para o armazenamento de logs. Você pode reutilizar uma conta existente ou criar uma nova conta. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Quando um Grupo de Recursos e uma conta do Data Lake Store estiverem disponíveis, crie uma conta do Data Lake Analytics.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações sobre uma conta do Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Enviar um trabalho do U-SQL

Criar um arquivo de texto com o seguinte script do U-SQL.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Enviar o script.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Monitorar trabalhos do U-SQL

Listar todos os trabalhos na conta. A saída inclui os trabalhos em execução no momento e os trabalhos que foram concluídos recentemente.

```
Get-AdlJob -Account $adla
```

Obter o status de um trabalho específico.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Em vez de chamar Get-AdlAnalyticsJob repetidamente até que um trabalho seja concluído, você pode usar o cmdlet espera Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Depois que o trabalho for concluído, verifique se o arquivo de saída existe listando os arquivos em uma pasta.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Verificar a existência de um arquivo.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Carregar e baixar arquivos

Baixar a saída do script do U-SQL.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Carregar um arquivo para ser usado como uma entrada para um script do U-SQL.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Consulte também
* Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).

