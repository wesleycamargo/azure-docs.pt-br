---
title: "Use o PowerShell para introdução ao Azure Data Lake Store | Microsoft Docs"
description: "Usar o Azure PowerShell para criar uma conta do Repositório Data Lake e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 1fe2b4b6c84e3c4d96677e3da5a94b9462e2a7bf
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introdução ao Repositório Azure Data Lake usando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Saiba como usar o Azure PowerShell para criar uma conta do Repositório Azure Data Lake e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Data Lake Store, veja [Visão geral do Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="authentication"></a>Autenticação
Este artigo usa uma abordagem de autenticação mais simples com o Data Lake Store, em que você é solicitado a inserir as credenciais da conta do Azure. O nível de acesso à conta do Data Lake Store e ao sistema de arquivos é controlado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com o Data Lake Store, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como se autenticar, veja [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [Autenticação de serviço a serviço](data-lake-store-authenticate-using-active-directory.md).

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

    ![Criar um Grupo de Recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")
3. Crie uma conta do Repositório Azure Data Lake. O nome especificado deve conter apenas letras minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta do Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do Azure Data Lake Store")
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

    ![Verificar o Diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar o Diretório")

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

## <a name="performance-guidance-while-using-powershell"></a>Diretrizes de desempenho durante o uso do PowerShell

Abaixo estão as configurações mais importantes que podem ser ajustadas para obter o melhor desempenho ao usar o PowerShell para trabalhar com o Data Lake Store:

| Propriedade            | Padrão | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Esse parâmetro permite que você escolha o número de threads paralelos para carregar ou baixar cada arquivo. Esse número representa o máximo de threads que pode ser alocado por arquivo, mas você pode obter menos threads dependendo do cenário (por exemplo, se você estiver carregando um arquivo de 1 KB, você obterá um thread mesmo que você peça 20 threads).  |
| ConcurrentFileCount | 10      | Esse parâmetro é especificamente para carregar ou baixar pastas. Esse parâmetro determina o número de arquivos simultâneos que podem ser carregados ou baixados. Esse número representa o número máximo de arquivos simultâneos que podem ser carregados ou baixados ao mesmo tempo, mas você pode obter menos simultaneidade dependendo do cenário (por exemplo, se você estiver carregando dois arquivos, você obterá dois uploads de arquivo simultâneos mesmo se você pedir 15). |

**Exemplo**

Esse comando baixa os arquivos do Azure Data Lake Store para a unidade local do usuário usando 20 threads por arquivo e 100 arquivos simultâneos.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Como determino o valor a ser definido para esses parâmetros?

Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: Determinar a contagem total de threads** – você deve começar calculando a contagem total de threads a usar. Como diretriz geral, você deve usar 6 threads para cada núcleo físico.

        Total thread count = total physical cores * 6

    **Exemplo**

    Supondo que você está executando os comandos do PowerShell de uma VM D14 que tem 16 núcleos

        Total thread count = 16 cores * 6 = 96 threads


* **Etapa 2: Calcular PerFileThreadCount** – calculamos nosso PerFileThreadCount com base no tamanho dos arquivos. Para arquivos menores do que 2,5 GB, não é necessário alterar esse parâmetro, pois o padrão de 10 é suficiente. Para arquivos com mais de 2,5 GB, você deve usar 10 threads como a base para os primeiros 2,5 GB e adicionar 1 thread para cada aumento adicional de 256 MB no tamanho do arquivo. Se você estiver copiando uma pasta com uma grande variedade de tamanhos de arquivo, considere agrupá-los em tamanhos de arquivo semelhantes. Ter tamanhos de arquivo diferentes pode fazer com que o desempenho não seja o ideal. Se não for possível agrupar os tamanhos de arquivo semelhantes, você deverá definir PerFileThreadCount com base no maior tamanho do arquivo.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Exemplo**

    Supondo que você tem 100 arquivos variando de 1 a 10 GB, usamos 10 GB como o maior tamanho do arquivo para a equação, cuja leitura seria semelhante à seguinte.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Etapa 3: Calcular ConcurrentFilecount** – use a contagem total de threads e PerFileThreadCount para calcular ConcurrentFileCount com base na equação a seguir.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exemplo**

    Com base nos valores de exemplo que utilizamos

        96 = 40 * ConcurrentFileCount

    Portanto, **ConcurrentFileCount** é **2,4**, que podemos pode arredondar para **2**.

### <a name="further-tuning"></a>Ajuste adicional

Você pode exigir ajuste adicional porque há um intervalo de tamanhos de arquivo com os quais trabalhar. O cálculo acima funciona bem se a maioria dos arquivos ou todos eles forem maiores e mais próximos do intervalo de 10 GB. Se em vez disso houver vários tamanhos de arquivos diferentes com muitos arquivos sendo menores, você poderá reduzir PerFileThreadCount. Reduzindo PerFileThreadCount, podemos aumentar ConcurrentFileCount. Então, se supormos que a maioria dos nossos arquivos são menores no intervalo de 5 GB, poderemos refazer nosso cálculo:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Portanto, **ConcurrentFileCount** será agora 96/20, que é 4,8, arredondado para **4**.

Você pode continuar a ajustar essas configurações, alterando **PerFileThreadCount** para cima e para baixo dependendo da distribuição de seus tamanhos de arquivo.

### <a name="limitation"></a>Limitações

* **O número de arquivos é menor do que ConcurrentFileCount**: se o número de arquivos que você está carregando for menor do que o **ConcurrentFileCount** calculado, você deverá reduzir **ConcurrentFileCount** para ser igual ao número de arquivos. Você pode usar quaisquer segmentos restantes para aumentar **PerFileThreadCount**.

* **Muitos threads**: se você aumentar muito a contagem de threads sem aumentar o tamanho do cluster, correrá o risco de degradação do desempenho. Pode haver problemas de contenção ao realizar alternância de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, o cluster poderá ser muito pequeno. Você pode aumentar o número de nós no cluster, o que lhe dará mais simultaneidade.

* **Erros de limitação**: você pode ver erros de limitação se a simultaneidade for muito alta. Se você estiver vendo erros de limitação, deverá reduzir a simultaneidade ou entrar em contato conosco.

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)


