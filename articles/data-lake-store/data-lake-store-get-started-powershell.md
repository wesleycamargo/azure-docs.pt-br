---
title: Use o PowerShell para começar a usar o Azure Data Lake Store Gen1 | Microsoft Docs
description: Usar o Azure PowerShell para criar uma conta do Azure Data Lake Storage Gen1 e executar operações básicas
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 6635b572d11339307083b8aa2aaaba69dc731101
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885852"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Introdução ao Azure Data Lake Store Gen1 usando Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como usar o Azure PowerShell para criar uma conta do Azure Data Lake Storage Gen1 e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Data Lake Storage Gen1, confira [Visão geral do Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="authentication"></a>Autenticação
Este artigo usa uma abordagem de autenticação mais simples com o Data Lake Storage Gen1, em que você é solicitado a inserir as credenciais da conta do Azure. O nível de acesso à conta do Data Lake Storage Gen1 e ao sistema de arquivos é controlado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com o Data Lake Storage Gen1, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como se autenticar, veja [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [Autenticação de serviço a serviço](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
1. Na área de trabalho, abra uma nova janela do Windows PowerShell. Insira o snippet de código a seguir para entrar em sua conta do Azure, definir a assinatura e registrar o provedor do Data Lake Storage Gen1. Quando solicitado a entrar, lembre-se de entrar como o proprietário ou um dos administradores da assinatura:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Uma conta Gen1 do Data Lake Storage está associada a um Grupo de Recursos do Azure. Comece criando um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um Grupo de Recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")
3. Crie uma conta do Azure Data Lake Storage Gen1. O nome especificado deve conter apenas letras minúsculas e números.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Criar uma conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do Data Lake Storage Gen1")
4. Verifique se a conta foi criada com êxito.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    A saída para o cmdlet deve ser **True**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Crie estruturas de diretório em sua conta do Data Lake Storage Gen1
Você pode criar diretórios em sua conta do Data Lake Storage Gen1 para gerenciar e armazenar dados.

1. Especifique um diretório raiz.

        $myrootdir = "/"
2. Crie um novo diretório chamado **mynewdirectory** na raiz especificada.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Verifique se o novo diretório foi criado com êxito.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Ele deverá exibir uma saída conforme mostrado na seguinte captura de tela:

    ![Verificar o Diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar o Diretório")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Carregar dados em uma conta do Data Lake Storage Gen1
É possível carregar seus dados no Data Lake Storage Gen1 diretamente no nível da raiz ou em um diretório que você criou na conta. Os snippets de código desta seção demonstram como carregar alguns dados de exemplo no diretório (**mynewdirectory**) criado na seção anterior.

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixe o arquivo e armazene-o em um diretório local no computador, como C:\sampledata\.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Renomear, baixar e excluir dados de uma conta do Data Lake Storage Gen1
Para renomear um arquivo, use o seguinte comando:

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para baixar um arquivo, use o seguinte comando:

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para excluir um arquivo, use o seguinte comando:

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando solicitado, insira **Y** para excluir o item. Se você tiver mais de um arquivo para excluir, você pode fornecer todos os caminhos separados por vírgula.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Excluir sua conta do Data Lake Storage Gen1
Use o comando a seguir para excluir sua conta do Data Lake Storage Gen1.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Quando solicitado, insira **Y** para excluir a conta.

## <a name="next-steps"></a>Próximos passos
* [Diretrizes de ajuste do desempenho para uso do PowerShell com o Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Use o Armazenamento de Data Lake do Azure Gen1 para requisitos de big data](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
