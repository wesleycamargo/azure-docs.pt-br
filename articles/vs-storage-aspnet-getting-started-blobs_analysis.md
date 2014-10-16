<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro][vs-storage-aspnet-getting-started-intro]]

### Introdução ao Armazenamento do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/vs-storage-aspnet-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/en-us/documentation/articles/vs-storage-aspnet-getting-started-queues" title="Filas">Filas</a><a href="/en-us/documentation/articles/vs-storage-aspnet-getting-started-tables" title="Tabelas">Tabelas</a></div>

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter qualquer tamanho. Blobs podem ser coisas como imagens, arquivos de áudio e vídeo, dados brutos e arquivos de documentos.

Para iniciar, você precisará criar uma conta do Armazenamento do Azure e um ou mais contêineres no armazenamento. Por exemplo, você poderia criar um armazenamento chamado "Scrapbook" e criar contêineres no armazenamento chamados "imagens" para armazenar fotos e "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você poderá carregar arquivos de blob individuais para eles. Consulte [Como usar o Armazenamento de Blob do .NET][Como usar o Armazenamento de Blob do .NET] para obter mais informações sobre a manipulação de blobs por meio de programação.

Para acessar blobs por meio de programação em projetos ASP.NET, é necessário realizar as seguintes tarefas.

1.  Obtenha o assembly Microsoft.WindowsAzure.Storage.dll. Você pode usar o NuGet para isto. Clique com o botão direito do mouse no Gerenciador de Soluções e escolha Gerenciar Pacotes NuGet. Pesquise online por "MicrosoftAzure.Storage" e clique em Instalar para instalar o pacote Armazenamento do Azure e as dependências. Adicione uma referência a este assembly ao seu projeto.
2.  Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

    usando Microsoft.WindowsAzure.Storage;
    usando Microsoft.WindowsAzure.Storage.Auth;
    usando Microsoft.WindowsAzure.Storage.Queue;

###### Obter a cadeia de conexão do armazenamento

Antes de poder realizar qualquer coisa com um blob, será necessário obter a cadeia de conexão para a conta de armazenamento onde os blobs residirão. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Para projetos ASP.NET, você pode usar o tipo **ConfigurationManager** para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include][vs-storage-getting-started-blobs-include]]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Blobs]: /en-us/documentation/articles/vs-storage-aspnet-getting-started-blobs "Blobs"
  [Filas]: /en-us/documentation/articles/vs-storage-aspnet-getting-started-queues "Filas"
  [Tabelas]: /en-us/documentation/articles/vs-storage-aspnet-getting-started-tables "Tabelas"
  [Como usar o Armazenamento de Blob do .NET]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/ "Como usar o Armazenamento de Blob do .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
