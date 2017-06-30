---
title: Use o SDK do .NET para desenvolver aplicativos no Azure Data Lake Store | Microsoft Docs
description: "Use o SDK do .NET do Azure Data Lake Store para criar uma conta do Data Lake Store e execute operações básicas no Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: f637b8617c4a9ed3be1eb0932a94b0ffcc7c8f7e
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introdução ao Repositório Azure Data Lake usando o SDK do .NET
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

Saiba como usar o [SDK do .NET para o Azure Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx) a fim de executar operações básicas, como criar pastas, carregar e baixar arquivos de dados etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2015 Atualização 2.

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Repositório Azure Data Lake**. Para obter instruções sobre como criar uma conta, confira [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Criar um aplicativo do Azure Active Directory**. Você pode usar o aplicativo Azure AD para autenticar o aplicativo Data Lake Store com o Azure AD. Há diferentes abordagens para autenticar com o Azure AD, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como se autenticar, veja [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [Autenticação de serviço a serviço](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
1. Abra o Visual Studio e crie um aplicativo de console.
2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

   | Propriedade | Valor |
   | --- | --- |
   | Categoria |Modelos/Visual C#/Windows |
   | Modelo |Aplicativo de console |
   | Nome |CreateADLApplication |
4. Clique em **OK** para criar o projeto.
5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes Nuget**, verifique se a **Origem do pacote** está definida para **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - este tutorial usa a versão 2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial usa a versão v2.2.12.

        ![Adicionar uma origem de Nuget](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gerenciador de Pacotes NuGet**.
6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Declare as variáveis conforme mostrado abaixo e forneça os valores de nome do Data Lake Store e do grupo de recursos existente. Além disso, o caminho local e o nome de arquivo fornecido aqui têm que existir no computador. Adicione o trecho de código a seguir após as declarações de namespace.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

Nas seções restantes do artigo, você pode ver como usar o métodos do .NET disponíveis para executar operações como autenticação, carregamento de arquivos, etc.

## <a name="authentication"></a>Autenticação

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Se você estiver usando autenticação de usuário final (recomendada para este tutorial)

Use-o com um aplicativo nativo existente do AD do Azure para autenticar seu aplicativo **interativamente**, o que significa que será solicitada a inserção das suas credenciais do Azure.

Para facilidade de uso, o trecho a seguir usa valores padrão para a ID do cliente e o URI de redirecionamento que funcionarão com qualquer assinatura do Azure. Para ajudá-lo a concluir este tutorial mais rápido, recomendamos que use essa abordagem. No trecho a seguir, basta fornecer o valor para sua ID de locatário. Você pode recuperá-la usando as instruções fornecidas em [Criar um aplicativo do Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

Algumas coisas sobre o trecho acima:

* Para ajudá-lo a concluir o tutorial mais rapidamente, esse trecho de código usa um domínio e uma ID de cliente do Azure AD que está disponível por padrão a todas as assinaturas do Azure. Portanto, você pode **usar o trecho de código como está em seu aplicativo**.
* No entanto, se você quiser usar sua própria ID de cliente de aplicativo e o domínio do Azure AD, crie um aplicativo nativo Azure AD e use a ID de locatário do Azure AD, a ID de cliente e o URI de redirecionamento para o aplicativo que você criou. Veja [Criar um aplicativo do Active Directory para autenticação do usuário final no Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Se você está usando autenticação serviço a serviço com o segredo do cliente
O trecho a seguir pode ser usado para autenticar seu aplicativo de forma **não interativa**, usando segredo/chave do cliente para entidade de serviço/aplicativo. Use-o com um ”aplicativo Web” do Azure AD. Para obter instruções sobre como criar um aplicativo Web do Azure AD e como recuperar a ID do cliente e o segredo do cliente necessários ao trecho a seguir, veja [Criar um Aplicativo do Active Directory para autenticação de serviço para serviço no Data Lake Store](data-lake-store-authenticate-using-active-directory.md).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Se você está usando a autenticação serviço a serviço com certificado

Como alternativa, o trecho a seguir pode ser usado para autenticar seu aplicativo de forma **não interativa**, usando o certificado para entidade de serviço/aplicativo do Azure Active Directory. Use-a com um [aplicativo do Azure AD com certificados](../azure-resource-manager/resource-group-authenticate-service-principal.md) existente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

## <a name="create-client-objects"></a>Criar objetos de cliente
O trecho a seguir cria os objetos de cliente sistema de arquivos e conta do Data Lake Store, que são usados para emitir solicitações para o serviço.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Listar todas as contas do Data Lake Store em uma assinatura
O trecho a seguir lista todas as contas do Data Lake Store em determinada assinatura do Azure.

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Criar um diretório
O trecho a seguir mostra um método `CreateDirectory` que você pode usar para criar um diretório em uma conta de Data Lake Store.

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Carregar um arquivo
O trecho a seguir mostra um método `UploadFile` que você pode usar para carregar arquivos para uma conta de Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

O SDK dá suporte a upload e download recursivos entre um caminho de arquivo local e um caminho de arquivo do Data Lake Store.    

## <a name="get-file-or-directory-info"></a>Obter informações do arquivo ou diretório
O trecho a seguir mostra um método `GetItemInfo` que você pode usar para recuperar informações sobre um arquivo ou diretório disponível no Data Lake Store.

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Listar arquivos ou diretórios
O trecho a seguir mostra um método `ListItem` que você pode usar para listar os arquivos e diretórios em uma conta de Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenar arquivos
O trecho a seguir mostra um método `ConcatenateFiles` que você pode usar para concatenar os arquivos.

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Anexar a um arquivo
O trecho a seguir mostra um método `AppendToFile` que você usa para acrescentar dados a um arquivo já armazenado em uma conta de Data Lake Store.

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

## <a name="download-a-file"></a>Baixar um arquivo
O trecho a seguir mostra um método `DownloadFile` que você pode usar para baixar um arquivo de uma conta de Data Lake Store.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
         _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath);
    }

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Referência do SDK do .NET do Azure Data Lake Store](https://docs.microsoft.com/dotnet/api/?view=azuremgmtdatalakestore-2.1.0-preview&term=DataLake.Store)
* [Referência do REST do Azure Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)

