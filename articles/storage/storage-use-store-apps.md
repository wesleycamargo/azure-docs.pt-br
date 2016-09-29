<properties
	pageTitle="Usar o Armazenamento do Azure em aplicativos da Windows Store | Microsoft Azure"
	description="Saiba como criar um aplicativo da Windows Store que usa o Armazenamento de Arquivos, Tabela, Fila ou Blobs do Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm" />
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="tamram"/>
	
# Como usar o Armazenamento do Azure em aplicativos da Windows Store

## Visão geral

Este guia mostra como iniciar o desenvolvimento de um aplicativo da Windows Store que utiliza o armazenamento do Azure.

## Baixar as ferramentas necessárias

- O [Visual Studio](https://www.visualstudio.com/pt-BR/visual-studio-homepage-vs.aspx) facilitará o build, a depuração, a localização, o empacotamento e a implantação de aplicativos da Windows Store. É necessário o Visual Studio 2012 ou posterior.
- A [Biblioteca de cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fornece uma biblioteca de classes do Windows Runtime para trabalhar com o armazenamento do Azure.
- As [Ferramentas do WCF Data Services para aplicativos da Windows Store](http://www.microsoft.com/download/details.aspx?id=30714) ampliam a experiência Adicionar Referência de Serviço com o suporte a OData no lado do cliente para aplicativos da Windows Store no Visual Studio.

## Desenvolver aplicativos

### Preparando-se

Crie um novo projeto de aplicativo da Windows Store no Visual Studio 2012 ou posterior:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Em seguida, adicione uma referência à Biblioteca de Cliente de Armazenamento do Azure clicando com o botão direito do mouse em **Referências**, clicando em **Adicionar Referência** e navegando até a Biblioteca de Cliente de Armazenamento para Tempo de Execução do Windows que você baixou:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Usando a biblioteca com os serviços Blob e Fila

Neste ponto, seu aplicativo está pronto para chamar os serviços Blob e Fila do Azure. Adicione as instruções **using** a seguir, de forma que os tipos de armazenamento do Azure possam ser referenciados diretamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Em seguida, adicione um botão à sua página. Adicione o código a seguir ao evento **Click** e modifique seu método de manipulador de eventos usando a [palavra-chave async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Esse código pressupõe que você tenha duas variáveis de cadeia de caracteres, *accountName* e *accountKey*. Elas representam o nome da sua conta de armazenamento e a chave de conta associada a essa conta.

Compile e execute o aplicativo. Clicar no botão verificará se existe um contêiner chamado *container1* em sua conta e, se não existir, ele será criado.

### Usando a biblioteca com o serviço Tabela

Os tipos que são usados para se comunicar com o serviço Tabela do Azure dependem do WFC Data Services para a biblioteca de aplicativos da Windows Store. Em seguida, adicione uma referência às bibliotecas necessárias do WCF usando o Console do Gerenciador de Pacotes:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Use o comando a seguir para apontar o Gerenciador de Pacotes para a localização de sua máquina:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Esse comando adicionará todas as referências necessárias ao seu projeto automaticamente. Se não quiser usar o Console do Gerenciador de Pacotes, você poderá adicionar a pasta NuGet do WCF Data Services em seu computador local à lista de origens do pacote e, em seguida, adicionar a referência por meio da interface do usuário, conforme descrito em [Gerenciando pacotes NuGet usando a caixa de diálogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Quando fizer referência ao pacote NuGet do WCF Data Services, altere o código no evento **Click** de seu botão:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Esse código verifica se uma tabela nomeada *table1* existe em sua conta, criando-a se ela não existir.

Você também pode adicionar uma referência à Microsoft.WindowsAzure.Storage.Table.dll, que está disponível no mesmo pacote que você baixou. Essa biblioteca contém funcionalidades adicionais, como consultas genéricas e serialização com base em reflexão. Observe que essa biblioteca não dá suporte a JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!---HONumber=AcomDC_0914_2016-->