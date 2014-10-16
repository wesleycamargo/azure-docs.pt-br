<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro][vs-storage-aspnet-getting-started-intro]]

### Introdução ao Armazenamento do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Filas">Filas</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tabelas">Tabelas</a></div>

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte [Como usar o Armazenamento de Filas do .NET][Como usar o Armazenamento de Filas do .NET] para obter mais informações.

Para acessar filas por meio de programação em um projeto de serviço de nuvem, é necessário realizar as seguintes tarefas.

1.  Obtenha o assembly Microsoft.WindowsAzure.Storage.dll. Você pode usar o NuGet para isto. Clique com o botão direito do mouse no Gerenciador de Soluções e escolha Gerenciar Pacotes NuGet. Pesquise online por "MicrosoftAzure.Storage" e clique em Instalar para instalar o pacote Armazenamento do Azure e as dependências. Adicione uma referência a este assembly ao seu projeto.
2.  Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

    usando Microsoft.WindowsAzure.Storage;
    usando Microsoft.WindowsAzure.Storage.Auth;
    usando Microsoft.WindowsAzure.Storage.Queue;

###### Obter a cadeia de conexão do armazenamento

Antes de poder realizar qualquer coisa com a fila, será necessário obter a cadeia de conexão para a conta de armazenamento onde a fila residirá. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Para projetos de serviço de nuvem, você pode usar o tipo **CloudConfigurationManager** para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include][vs-storage-getting-started-queues-include]]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Blobs]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Blobs"
  [Filas]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues "Filas"
  [Tabelas]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tabelas"
  [Como usar o Armazenamento de Filas do .NET]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-queues/ "Como usar o Armazenamento de Filas do .NET"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
