---
title: "Gerencie expiração de armazenamento Blob do Azure na Rede de Distribuição de Conteúdo do Microsoft Azure | Microsoft Docs"
description: "Aprenda sobre as opções para controlar a vida útil de blobs no cache do Azure CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: c2b49058ec7dd52b5063e815447697fa17ddb53a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Gerencie expiração de armazenamento Blob do Azure na Rede de Distribuição de Conteúdo do Microsoft Azure
> [!div class="op_single_selector"]
> * [Conteúdo da Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [Serviço de armazenamento de blobs](../storage/common/storage-introduction.md#blob-storage) no Armazenamento do Azure é uma das várias origens baseadas no Azure integradas à CDN (Rede de Distribuição de Conteúdo) do Azure. Qualquer conteúdo de blob publicamente acessível pode ser armazenado em cache no Azure CDN até que o tempo de vida (TTL) seja decorrido. A vida útil é determinada pelo cabeçalho `Cache-Control` na resposta HTTP do servidor de origem. Este artigo descreve várias maneiras que você pode definir o cabeçalho `Cache-Control` em um blob no Armazenamento do Azure.

> [!TIP]
> Você pode optar por não definir nenhuma vida útil em um blob. Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias.
> 
> Para obter mais informações sobre como a CDN do Azure trabalha para acelerar o acesso a blob e outros arquivos, consulte [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md).
> 
> Para mais informações sobre o Armazenamento de Blobs do Azure, confira [Introdução ao armazenamento de Blobs](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) é uma das maneiras mais rápidas e eficientes de administrar os serviços do Azure. Use o cmdlet `Get-AzureStorageBlob` para obter uma referência para o blob, em seguida, defina a propriedade `.ICloudBlob.Properties.CacheControl`. 

Por exemplo:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Você também pode usar o PowerShell para [gerenciar os perfis e os pontos de extremidade da CDN](cdn-manage-powershell.md).
> 
>

## <a name="azure-storage-client-library-for-net"></a>Biblioteca do Cliente de Armazenamento do Azure para .NET
Para definir o cabeçalho `Cache-Control` de um blob usando o .NET, use a [Biblioteca do Cliente de Armazenamento do Azure para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para definir a propriedade [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

Por exemplo:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Existem mais exemplos de código .NET disponíveis nos [Exemplos de Armazenamento de Blobs do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Outros métodos
* [Interface de linha de comando do Azure](../cli-install-nodejs.md)
  
    Quando você carrega um blob, pode definir a propriedade *cacheControl* usando a opção `-p` na Interface de Linha de Comando do Azure. O exemplo a seguir define o TTL para uma hora (3600 segundos):
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Defina explicitamente a propriedade *x-ms-blob-cache-control* em uma solicitação [Put Block](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx), ou [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).

* Ferramentas de gerenciamento de armazenamento de terceiros
  
    Algumas ferramentas de gerenciamento de armazenamento do Azure de terceiros permitem que você defina a propriedade **CacheControl** em blobs. 

## <a name="testing-the-cache-control-header"></a>Testando o cabeçalho de Controle de Cache
Você pode facilmente verificar as configurações TTL dos seus blobs. Com as [ferramentas para desenvolvedores](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu navegador, teste se o blob inclui `Cache-Control` no cabeçalho de resposta. Você também pode usar uma ferramenta como a **wget**, [Postman](https://www.getpostman.com/) ou [Fiddler](http://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximas etapas
* [Saiba como gerenciar a expiração do conteúdo do Serviço de Nuvem na CDN do Azure](cdn-manage-expiration-of-cloud-service-content.md)

