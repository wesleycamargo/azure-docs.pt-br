---
title: "SDK do .NET: operações de filesystem no Azure Data Lake Store | Microsoft Docs"
description: "Use o SDK do .NET para Azure Data Lake Store para executar operações de filesystem no Data Lake Store, como criar pastas etc."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 1094a5ea3c000707aa7736d22d4df0558da32b5e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Operações de filesystem no Azure Data Lake Store usando o SDK do .NET
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, você aprenderá a executar operações de filesytem no Data Lake Store usando o SDK do .NET. As operações de filesytem incluem a criação de pastas em uma conta do Data Lake Store, carregamento de arquivos, download de arquivos etc.

Para obter instruções sobre como executar operações de gerenciamento de conta no Data Lake Store usando o SDK do .NET, consulte [Operações de gerenciamento de conta no Data Lake Store usando o SDK .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2017.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Repositório Azure Data Lake**. Para obter instruções sobre como criar uma conta, confira [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
O exemplo de código disponível [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica o processo de criação de arquivos no armazenamento, concatenação de arquivos, download de um arquivo e exclusão de alguns arquivos no armazenamento. Esta seção do artigo percorre com você as principais partes do código.

1. Abra o Visual Studio e crie um aplicativo de console.
2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

   | Propriedade | Valor |
   | --- | --- |
   | Categoria |Modelos/Visual C#/Windows |
   | Modelo |Aplicativo de console |
   | NOME |CreateADLApplication |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes NuGet**, verifique se a **Origem do pacote** está definida como **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.DataLake.Store` - Este tutorial usa a versão v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Este tutorial usa a versão v2.3.1.
    
    Feche o **Gerenciador de Pacotes NuGet**.

6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Declare as variáveis, conforme mostrado abaixo, e forneça os valores para os espaços reservados. Além disso, o caminho local e o nome de arquivo fornecido aqui existem no computador.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net";        
            }
        }

Nas seções restantes do artigo, você pode ver como usar o métodos do .NET disponíveis para executar operações como autenticação, carregamento de arquivos, etc.

## <a name="authentication"></a>Autenticação

* Para saber sobre autenticação do usuário final em seu aplicativo, consulte [Autenticação do usuário final com Data Lake Store usando SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para saber sobre autenticação do usuário final em seu aplicativo, consulte [Autenticação de serviço para serviço com Data Lake Store usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Criar objeto de cliente
O trecho de código a seguir cria o objeto de cliente de sistema de arquivos do Data Lake Store, que é usado para emitir solicitações para o serviço.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Criar um arquivo e diretório
Adicione o trecho de código a seguir ao seu aplicativo. Este trecho de código adiciona um arquivo, bem como qualquer diretório pai que não exista.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOuputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Anexar a um arquivo
O trecho de código a seguir acrescenta dados a um arquivo existente na conta do Data Lake Store.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Ler um arquivo
O trecho de código a seguir lê o conteúdo de um arquivo na conta do Data Lake Store.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Obter propriedades de arquivo
O trecho de código a seguir retorna as propriedades associadas a um arquivo ou diretório.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

A definição do método `PrintDirectoryEntry` está disponível como parte do exemplo [no Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Renomear um arquivo
O trecho de código a seguir renomeia um arquivo existente na conta do Data Lake Store.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Enumerar um diretório
O trecho de código a seguir enumera os diretórios em uma conta do Data Lake Store

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

A definição do método `PrintDirectoryEntry` está disponível como parte do exemplo [no Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Excluir diretórios recursivamente
O trecho de código a seguir exclui um diretório e todos os seus subdiretórios, recursivamente.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Exemplos
Aqui estão alguns exemplos sobre como usar o SDK do sistema de arquivos do Data Lake Store.
* [Exemplo básico no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemplo avançado no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Consulte também
* [Operações de gerenciamento de conta no Data Lake Store usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Referência do SDK do .NET do Azure Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
