<properties urlDisplayName="Azure Storage in Windows Store apps" pageTitle="Usar o armazenamento do Azure em aplicativos da Windows Store | Azure" metaKeywords="" description="Learn how to use Azure blobs, queues, and tables to store data for a Windows Store app." metaCanonical="" services="storage" documentationCenter="" title="How to use Azure Storage in Windows Store Apps" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="tamram" />





# Como usar o Armazenamento do Azure em aplicativos da Windows Store

Este guia mostra como iniciar o desenvolvimento de um aplicativo da Windows Store que utiliza o armazenamento do Azure.

## Baixar as ferramentas necessárias ##

- [Visual Studio 2012](http://msdn.microsoft.com/pt-br/library/windows/apps/br211384) torna mais fácil compilar, depurar, localizar, empacotar e implantar aplicativos da Windows Store.
- [Biblioteca do Cliente de Armazenamento do Microsoft Azure para aplicativos do Tempo de Execução do Windows](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) fornece uma biblioteca de classes para trabalhar com o Armazenamento do Azure.
- [Ferramentas de serviços de dados WCF para aplicativos da Windows Store](http://www.microsoft.com/pt-br/download/details.aspx?id=30714) estende a experiência de Adicionar Referência de Serviço com suporte a OData no lado do cliente para aplicativos da Windows Store no Visual Studio 2012 e posteriores.

## Desenvolver aplicativos ##

<h3>Preparando-se</h3>

Crie um novo projeto de aplicativo da Windows Store no Visual Studio 2012 ou posterior:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Em seguida, adicione uma referência à biblioteca de cliente de armazenamento do Azure clicando com o botão direito do mouse em **Referências**, escolhendo **Adicionar Referência**e navegando até a biblioteca de cliente de armazenamento para Tempo de Execução do Windows que você baixou:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

<h3>Usando a biblioteca com os serviços Blob e Fila</h3>

Neste ponto, seu aplicativo está pronto para se chamar os serviços Blob e Fila. Adicione as instruções **using** a seguir, de forma que os tipos de armazenamento do Azure possam ser referenciados diretamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
Em seguida, adicione um botão à sua página. Adicione o código a seguir ao seu evento **Click** e modifique seu método manipulador de eventos com a [palavra-chave async](http://msdn.microsoft.com/en-US/library/vstudio/hh156513.aspx):
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
Esse código pressupõe que você tem duas variáveis de cadeia de caracteres, *accountName* e *accountKey*, que representam o nome de sua conta de armazenamento e a chave associada a essa conta.

Compile e execute o aplicativo. Clicar no botão primeiro verificará se existe um contêiner chamado *container1* em sua conta e, se não existir, ele será criado.

<h3>Usando a biblioteca com o serviço Tabela</h3>

Os tipos usados para se comunicar com o serviço Tabela dependem da biblioteca de WCF Data Services para aplicativos da Windows Store. Em seguida, adicione uma referência às bibliotecas necessárias do WCF usando o Console do Gerenciador de Pacotes:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Use o comando a seguir para apontar o Gerenciador de Pacotes para a localização de seu computador:
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Esse comando adicionará todas as referências necessárias ao seu projeto automaticamente. Se não quiser usar o Console do Gerenciador de Pacotes, você também poderá adicionar a pasta NuGet do WCF Data Services em seu computador local à lista de origens do pacote e, em seguida, adicionar a referência por meio da interface do usuário, conforme descrito em [Gerenciando pacotes NuGet com a caixa de diálogo (a página pode estar em inglês)](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Quando fizer referência ao pacote NuGet do WCF Data Services, altere o código no evento **Click** de seu botão:
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
Esse código verifica se uma tabela nomeada *table1* existe em sua conta, criando-a se ela não existir.

Você também pode adicionar uma referência à Microsoft.WindowsAzure.Storage.Table.dll, disponível no mesmo pacote que você baixou. Essa biblioteca contém funcionalidades adicionais, como consultas genéricas e serialização com base em reflexão. Observe que essa biblioteca não dá suporte a JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!--HONumber=35.1-->
