<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-cloud-services-getting-started-intro][vs-storage-cloud-services-getting-started-intro]]

### Introdução ao Armazenamento do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/pt-br/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Filas">Filas</a><a href="/pt-br/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tabelas">Tabelas</a></div>

O serviço de armazenamento Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Consulte [Como usar o Armazenamento de Tabelas do .NET][Como usar o Armazenamento de Tabelas do .NET] para obter mais informações.

Para acessar tabelas por meio de programação em um projeto de serviço de nuvem, é necessário realizar as seguintes tarefas.

1.  Obtenha o assembly Microsoft.WindowsAzure.Storage.dll. Você pode usar o NuGet para isto. Clique com o botão direito do mouse no Gerenciador de Soluções e escolha Gerenciar Pacotes NuGet. Pesquise online por "MicrosoftAzure.Storage" e clique em Instalar para instalar o pacote Armazenamento do Azure e as dependências. Adicione uma referência a este assembly ao seu projeto.
2.  Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

    usando Microsoft.WindowsAzure.Storage;
    usando Microsoft.WindowsAzure.Storage.Auth;
    usando Microsoft.WindowsAzure.Storage.Queue;

###### Obter a cadeia de conexão do armazenamento

Antes de poder realizar qualquer coisa com uma tabela, será necessário obter a cadeia de conexão para a conta de armazenamento onde as tabelas residirão. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Para projetos de serviço de nuvem, você pode usar o tipo **CloudConfigurationManager** para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include][vs-storage-getting-started-tables-include]]

  [vs-storage-cloud-services-getting-started-intro]: ../includes/vs-storage-cloud-services-getting-started-intro.md
  [Blobs]: /pt-br/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Blobs"
  [Filas]: /pt-br/documentation/articles/vs-storage-cloud-services-getting-started-queues "Filas"
  [Tabelas]: /pt-br/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tabelas"
  [Como usar o Armazenamento de Tabelas do .NET]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Como usar o Armazenamento de Tabelas do .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
