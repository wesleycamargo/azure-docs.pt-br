---
title: "Introdução ao Data Lake Store | Azure"
description: "Usar o Azure PowerShell para criar uma conta do Repositório Data Lake e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c70eff85416b6b41eb455edc430a66b588dec41


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introdução ao Repositório Azure Data Lake usando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Saiba como usar o Azure PowerShell para criar uma conta do Repositório Azure Data Lake e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Data Lake Store, veja [Visão geral do Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

## <a name="authentication"></a>Autenticação
Este artigo usa uma abordagem de autenticação mais simples com o Data Lake Store, em que você é solicitado a inserir as credenciais da conta do Azure. O nível de acesso à conta do Data Lake Store e ao sistema de arquivos é controlado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com o Data Lake Store, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como autenticar, confira [Autenticar com o Data Lake Store usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Repositório Azure Data Lake
1. Na área de trabalho, abra uma nova janela do Windows PowerShell e insira o trecho de código a seguir para entrar em sua conta do Azure, definir a assinatura e registrar o provedor do Data Lake Store. Quando solicitado a entrar, lembre-se de entrar como o proprietário ou um dos administradores da assinatura:
   
        # Log in to your Azure account
        Login-AzureRmAccount
   
        # List all the subscriptions associated to your account
        Get-AzureRmSubscription
   
        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>
   
        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Uma conta do Repositório Azure Data Lake está associada a um Grupo de Recursos do Azure. Comece criando um Grupo de Recursos do Azure.
   
        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"
   
    ![Criar um grupo de recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. Crie uma conta do Repositório Azure Data Lake. O nome especificado deve conter apenas letras minúsculas e números.
   
        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"
   
    ![Criar uma conta do Repositório Azure Data Lake](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")
4. Verifique se a conta foi criada com êxito.
   
        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName
   
    A saída para isso deve ser **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Criar estruturas de diretório no Repositório Azure Data Lake
Você pode criar diretórios em sua conta do Repositório Azure Data Lake para gerenciar e armazenar dados.

1. Especifique um diretório raiz.
   
        $myrootdir = "/"
2. Crie um novo diretório chamado **mynewdirectory** na raiz especificada.
   
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Verifique se o novo diretório foi criado com êxito.
   
        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir
   
    Ele deve mostrar uma saída semelhante à seguinte:
   
    ![Verificar diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Carregar dados no Repositório Azure Data Lake
É possível carregar seus dados no Repositório Data Lake diretamente no nível da raiz ou em um diretório que você criou na conta. Os trechos de código abaixo demonstram como carregar alguns dados de exemplo no diretório (**mynewdirectory**) criado na seção anterior.

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixe o arquivo e armazene-o em um diretório local no computador, como C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renomear, baixar e excluir dados do Repositório Data Lake
Para renomear um arquivo, use o seguinte comando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para baixar um arquivo, use o seguinte comando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para excluir um arquivo, use o seguinte comando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando solicitado, insira **Y** para excluir o item. Se você tiver mais de um arquivo para excluir, você pode fornecer todos os caminhos separados por vírgula.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Excluir sua conta do Repositório Azure Data Lake
Use o comando a seguir para excluir sua conta do Repositório Data Lake.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando solicitado, insira **Y** para excluir a conta.

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO2-->


