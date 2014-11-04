<properties title="Introdu&ccedil;&atilde;o ao armazenamento do Azure" pageTitle="Introdu&ccedil;&atilde;o ao armazenamento do Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Introdução][Introdução]
> -   [O que aconteceu][O que aconteceu]

## Introdução ao armazenamento do Azure (Projetos ASP.NET)

> [AZURE.SELECTOR]
>
> -   [Blobs][Blobs]
> -   [Filas][Introdução]
> -   [Tabelas][Tabelas]

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte [Como usar o Armazenamento de Filas do .NET][Como usar o Armazenamento de Filas do .NET] para obter mais informações.

Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

##### Obter a cadeia de conexão do armazenamento

Antes de poder realizar qualquer coisa com a fila, será necessário obter a cadeia de conexão para a conta de armazenamento onde as filas residirão. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Para projetos ASP.NET, você pode usar o tipo **ConfigurationManager** para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.AppSettings["<storageAccountName>_AzureStorageConnectionString"]);

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Para obter mais informações, consulte [ASP.NET][ASP.NET].

  [Introdução]: /documentation/articles/vs-storage-aspnet-getting-started-queues/
  [O que aconteceu]: /documentation/articles/vs-storage-aspnet-what-happened/
  [Blobs]: /documentation/articles/vs-storage-aspnet-getting-started-blobs/
  [Tabelas]: /documentation/articles/vs-storage-aspnet-getting-started-tables/
  [Como usar o Armazenamento de Filas do .NET]: http://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-how-to-use-queues/
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
  [ASP.NET]: http://www.asp.net
