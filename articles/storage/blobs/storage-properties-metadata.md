---
title: Definir e recuperar as propriedades e os metadados para objetos no Armazenamento do Azure | Microsoft Docs
description: Armazene metadados personalizados em objetos no Armazenamento do Azure e defina e recupere propriedades do sistema.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: tamram
ms.openlocfilehash: d2c95139d42f42e43e2fa6e587d5b1b13afdf1e9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112436"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e recuperar as propriedades e os metadados

Os objetos no armazenamento do Azure oferecem suporte às propriedades do sistema e metadados definidos pelo usuário, além dos dados que eles contêm. Este artigo discute o gerenciamento das propriedades do sistema e dos metadados definidos pelo usuário com a [Biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriedades do sistema**: existem propriedades do sistema em cada recurso de armazenamento. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. As bibliotecas de clientes do Armazenamento do Microsoft Azure mantêm essas propriedades.

* **Metadados definidos pelo usuário**: os metadados definidos pelo usuário consistem em um ou mais pares nome-valor que você especifica para um recurso do Armazenamento do Microsoft Azure. É possível usar metadados para armazenar valores adicionais com um recurso. Os valores de metadados são apenas para suas próprias finalidades e não afetam o comportamento do recurso.

Recuperar os valores da propriedade e dos metadados para um recurso de armazenamento é um processo de duas etapas. Antes de poder ler esses valores, será necessário buscá-los explicitamente, chamando o método **FetchAttributes** ou **FetchAttributesAsync**. A exceção é se você estiver chamando o método **Exists** ou **ExistsAsync** em um recurso. Ao chamar um desses métodos, o Armazenamento do Microsoft Azure chama o método **FetchAttributes** adequado nas abordagens como parte da chamada para o método **Exists**.

> [!IMPORTANT]
> Se você achar que a propriedade ou os valores de metadados de um recurso de armazenamento não foram preenchidos, verifique se o código chama o método **FetchAttributes** ou **FetchAttributesAsync**.
>
> Pares nome/valor de metadados podem conter apenas caracteres ASCII. Pares de nome/valor de metadados são cabeçalhos HTTP válidos e, portanto, devem cumprir todas as restrições que regem cabeçalhos HTTP. É recomendável usar codificação de URL ou codificação Base64 para nomes e valores contendo caracteres não ASCII.
>

## <a name="setting-and-retrieving-properties"></a>Configurando e recuperando as propriedades
Para recuperar os valores da propriedade, chame o método **FetchAttributesAsync** no blob ou no contêiner para preencher as propriedades e leia os valores.

Para definir as propriedades em um objeto, especifique o valor da propriedade, em seguida, chame o método **SetProperties** .

O exemplo de código a seguir cria um contêiner e escreve alguns dos valores da propriedade em uma janela do console.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Configurando e recuperando os metadados
Você pode especificar os metadados como um ou mais pares de nome-valor em um recurso de contêiner ou blob. Para definir os metadados, adicione pares nome-valor à coleção **Metadados** no recurso e, em seguida, chame o método **SetMetadata** ou **SetMetadataAsync** para salvar os valores para o serviço.

> [!NOTE]
> O nome dos metadados deve estar em conformidade com as convenções de nomenclatura para os identificadores de C#.
>
>

O exemplo de código a seguir define os metadados em um contêiner. Um valor é definido usando o método **Add** da coleção. O outro valor é definido usando a sintaxe implícita de chave/valor. Ambos são válidos.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Para recuperar metadados, chame o método **FetchAttributes** ou **FetchAttributesAsync** no blob ou contêiner para preencher a coleção de **Metadados** e leia os valores, conforme mostrado no exemplo abaixo.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Próximas etapas
* [Biblioteca do cliente de armazenamento do Azure para a referência do .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Biblioteca do cliente de armazenamento do Azure para o pacote NuGet do .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
