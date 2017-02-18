---
title: "Use a interface de linha de comando para começar com o Azure Data Lake Store | Microsoft Docs"
description: "Usar a linha de comando da plataforma cruzada do Azure para criar uma conta do Repositório Data Lake e executar operações básicas"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f33ccee7dd520adf074856616005c929040116dd
ms.openlocfilehash: 89d1811f3f336f2526ccba3b6be26e90ab7120ad


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introdução ao Repositório Azure Data Lake usando a linha de comando do Azure
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

> [!NOTE]
> Para carregar e baixar grandes quantidades de dados (arquivos grandes, uma grande quantidade de arquivos ou ambos), recomendamos o uso do [SDK do Python](data-lake-store-get-started-python.md), do [SDK do .NET](data-lake-store-get-started-net-sdk.md) ou do [Azure PowerShell](data-lake-store-get-started-powershell.md). Essas opções têm um desempenho melhor, pois usam vários threads para paralelizar a movimentação de dados.
> 
>  

Saiba como usar a interface de linha de comando do Azure ara criar uma conta do Repositório Azure Data Lake e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Data Lake Store, veja [Visão geral do Data Lake Store](data-lake-store-overview.md).

A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux. A CLI do Azure é de software livre. O código-fonte é gerenciado no GitHub em <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artigo aborda apenas o uso da CLI do Azure com o Repositório Data Lake. Para obter um orientação geral sobre como usar a CLI do Azure, confira [Como usar a CLI do Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração. Certifique-se de reinicializar o computador depois de instalar a CLI.

## <a name="authentication"></a>Autenticação
Este artigo usa uma abordagem de autenticação mais simples com o Data Lake Store, em que você faz logon como um usuário final. O nível de acesso à conta do Data Lake Store e ao sistema de arquivos é controlado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com o Data Lake Store, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como autenticar, confira [Autenticar com o Data Lake Store usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="login-to-your-azure-subscription"></a>Faça logon em sua assinatura do Azure
1. Siga as etapas documentadas em [Conectar a uma assinatura do Azure a partir da Interface de Linha de Comando do Azure (Azure CLI)](../xplat-cli-connect.md) e conectar à sua assinatura usando o método `azure login`.
2. Liste as assinaturas associadas à sua conta usando o comando `azure account list`.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    Da saída acima, **Azure-sub-1** está habilitado no momento e a outra assinatura é **Azure-sub-2**. 
3. Selecione a assinatura com a qual deseja trabalhar. Se você quiser trabalhar com a assinatura Azure-sub-2, use o comando `azure account set`.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do Repositório Azure Data Lake
Abra uma sessão de prompt, shell ou terminal de comando e execute os comandos a seguir.

1. Alterne para modo Gerenciador de Recursos do Azure usando o seguinte comando:
   
        azure config mode arm
2. Crie um novo grupo de recursos. No comando a seguir fornecem os valores de parâmetro que você deseja usar.
   
        azure group create <resourceGroup> <location>
   
    Se o nome do local contiver espaços, coloque-o entre aspas duplas. Por exemplo "Leste dos EUA 2".
3. Crie uma conta do Repositório Data Lake.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Crie pastas na conta do Repositório Data Lake
Você pode criar pastas em sua conta do Repositório Azure Data Lake para gerenciar e armazenar dados. Use o seguinte comando para criar uma pasta chamada "mynewfolder" na raiz do Repositório Data Lake.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por exemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Carregar dados no Repositório Azure Data Lake
É possível carregar seus dados no Repositório Data Lake diretamente no nível da raiz ou em uma pasta que você criou na conta. Os trechos de código abaixo demonstram como carregar alguns dados de exemplo no diretório (**mynewdirectory**) criado na seção anterior.

Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Baixe o arquivo e armazene-o em um diretório local no computador, como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por exemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Listar os arquivos no Repositório Data Lake
Use o comando a seguir para listar os arquivos em uma conta do Repositório Data Lake.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por exemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

A saída desse comando deve ser:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renomear, baixar e excluir dados do Repositório Data Lake
* **Para renomear um arquivo**, use o seguinte comando:
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    Por exemplo:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **Para baixar um arquivo**, use o comando a seguir. Verifique se o caminho de destino especificado já existe.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Por exemplo:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **Para excluir um arquivo**, use o seguinte comando:
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Por exemplo:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    Quando solicitado, insira **Y** para excluir o item.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Exibir a lista de controle de acesso para uma pasta no Repositório Data Lake
Use o seguinte comando para exibir as ACLs em uma pasta do Repositório Data Lake. Na versão atual, as ACLs podem ser definidas somente na raiz do Repositório Data Lake. Portanto, o parâmetro path abaixo será sempre raiz (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por exemplo:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Excluir sua conta do Repositório Data Lake
Use o comando a seguir para excluir sua conta do Repositório Data Lake.

    azure datalake store account delete <dataLakeStoreAccountName>

Por exemplo:

    azure datalake store account delete mynewdatalakestore

Quando solicitado, insira **Y** para excluir a conta.

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Feb17_HO1-->


