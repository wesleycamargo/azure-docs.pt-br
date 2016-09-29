<properties
   pageTitle="Usar o SDK do .NET do Data Lake Store para desenvolver aplicativos | Microsoft Azure"
   description="Usar o SDK do .NET do Repositório Azure Data Lake para desenvolver aplicativos"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/15/2016"
   ms.author="nitinme"/>

# Introdução ao Repositório Azure Data Lake usando o SDK do .NET

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Saiba como usar o [SDK do .NET para o Azure Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx) a fim de executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, etc. Para obter mais informações sobre o Data Lake, veja [Repositório Azure Data Lake](data-lake-store-overview.md).

## Pré-requisitos

* **Visual Studio 2013 ou 2015**. As instruções abaixo usam o Visual Studio 2015.

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Repositório Azure Data Lake**. Para obter instruções sobre como criar uma conta, confira [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Crie um aplicativo do Azure Active Directory** se deseja que seu aplicativo autentique automaticamente com o Azure Active Directory.

	* **Para autenticação não interativa** - no Azure Active Directory, você precisa criar um **aplicativo Web**. Depois de criar o aplicativo, recupere os seguintes valores relacionados ao aplicativo.
		- Obter a **ID do cliente** e o **segredo do cliente** para o aplicativo
		- Atribua o aplicativo do Azure Active Directory a uma função. A função pode estar no nível do escopo no qual você deseja conceder permissão ao aplicativo do Azure Active Directory. Por exemplo, você pode atribuir o aplicativo no nível da assinatura ou no nível de um grupo de recursos.

	Veja [Criar aplicativo do Active Directory e a entidade de serviço usando o portal](../resource-group-create-service-principal-portal.md) para obter instruções sobre como recuperar esses valores, definir as permissões e atribuir funções.

## Criar um aplicativo .NET

1. Abra o Visual Studio e crie um aplicativo de console.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

	| Propriedade | Valor |
	|----------|-----------------------------|
	| Categoria | Modelos/Visual C#/Windows |
	| Modelo | Aplicativo de console |
	| Nome | CreateADLApplication |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

	1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
	2. Na guia **Gerenciador de Pacotes NuGet**, verifique se a **Origem do pacote** está definida para **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
	3. Procure e instale os seguintes pacotes NuGet:

		* `Microsoft.Azure.Management.DataLake.Store` - este tutorial usa a versão 0.12.5-preview.
		* `Microsoft.Azure.Management.DataLake.StoreUploader` - este tutorial usa a versão 0.10.6-preview.
		* `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial usa a versão 2.2.8-preview.

		![Adicionar uma fonte do NuGet](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Criar uma nova conta da Análise do Azure Data Lake")

	4. Feche o **Gerenciador de Pacotes NuGet**.

6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

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
                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

Nas seções restantes do artigo, você pode ver como usar o métodos do .NET disponíveis para executar operações como autenticação, carregamento de arquivos, etc.

## Autenticação

O trecho a seguir pode ser usado para uma experiência de logon interativo.

    // User login via interactive popup
    //    Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"))
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Como alternativa, o trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando segredo/chave do cliente para entidade de segurança/aplicativo.

    // Service principal / appplication authentication with client secret / key
    //    Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-clientid>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

Como alternativa, o trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando o certificado para entidade de segurança/aplicativo.

    // Service principal / application authentication with certificate
    //    Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## Criar objetos de cliente

O trecho a seguir cria os objetos de cliente sistema de arquivos e conta do Data Lake Store, que são usados para emitir solicitações para o serviço.

    // Create client objects
    var fileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## Listar todas as contas do Data Lake Store em uma assinatura

O trecho a seguir lista todas as contas do Data Lake Store em determinada assinatura do Azure.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## Criar um diretório

O trecho a seguir mostra um método `CreateDirectory` que você pode usar para criar um diretório em uma conta de Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## Carregar um arquivo

O trecho a seguir mostra um método `UploadFile` que você pode usar para carregar arquivos para uma conta de Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

DataLakeStoreUploader dá suporte ao upload e ao download recursivos entre um caminho de arquivo (ou pasta) local para o Data Lake Store.

## Obter informações do arquivo ou diretório

O trecho a seguir mostra um método `GetItemInfo` que você pode usar para recuperar informações sobre um arquivo ou diretório disponível no Data Lake Store.

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## Listar arquivos ou diretórios

O trecho a seguir mostra um método `ListItem` que você pode usar para listar os arquivos e diretórios em uma conta de Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## Concatenar arquivos

O trecho a seguir mostra um método `ConcatenateFiles` que você pode usar para concatenar os arquivos.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## Anexar a um arquivo

O trecho a seguir mostra um método `AppendToFile` que você usa para acrescentar dados a um arquivo já armazenado em uma conta de Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## Baixar um arquivo

O trecho a seguir mostra um método `DownloadFile` que você pode usar para baixar um arquivo de uma conta de Data Lake Store.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## Próximas etapas

- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referência do SDK do .NET do Azure Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referência do REST do Azure Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)

<!---HONumber=AcomDC_0921_2016-->