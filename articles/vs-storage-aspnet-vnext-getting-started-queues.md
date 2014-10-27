<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro](../includes/vs-storage-aspnet-vnext-getting-started-intro.md)]

# Introdução ao Armazenamento do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="Blobs" class="current">Blobs</a><a href="/pt-br/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="Filas">Filas</a><a href="/pt-br/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="Tabelas">Tabelas</a></div>

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte [Como usar o Armazenamento de Filas do .NET][Como usar o Armazenamento de Filas do .NET] para obter mais informações.

Para acessar filas por meio de programação em projetos ASP.NET vNext, é necessário realizar as seguintes tarefas.

1.  Obtenha o assembly Microsoft.WindowsAzure.Storage.dll. Você pode usar o NuGet para isto. Clique com o botão direito do mouse no Gerenciador de Soluções e escolha Gerenciar Pacotes NuGet. Pesquise online por "MicrosoftAzure.Storage" e clique em Instalar para instalar o pacote Armazenamento do Azure e as dependências. Adicione uma referência a este assembly ao seu projeto.
2.  Instale o pacote Microsoft.Framework.ConfigurationModel.Json NuGet.
3.  Adicione a seguinte declaração de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

        using Microsoft.Framework.ConfigurationModel;

4.  Use o seguinte código para obter a definição da configuração.

        var config = new Configuration();
        config.AddJsonFile("config.json");

### Obter a cadeia de conexão do armazenamento

Antes de poder realizar qualquer coisa com a fila, será necessário obter a cadeia de conexão para a conta de armazenamento onde as filas residirão. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Se estiver usando um projeto ASP.NET vNest, você poderá chamar o método do objeto Configuration para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

  [vs-storage-aspnet-vnext-getting-started-intro]: ../includes/vs-storage-aspnet-vnext-getting-started-intro.md
  [Blobs]: /pt-br/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "Blobs"
  [Filas]: /pt-br/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "Filas"
  [Tabelas]: /pt-br/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "Tabelas"
  [Como usar o Armazenamento de Filas do .NET]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-queues/ "Como usar o Armazenamento de Filas do .NET"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
