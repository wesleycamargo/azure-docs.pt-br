---
title: Definir e recuperar as propriedades e os metadados para objetos no Armazenamento do Azure | Microsoft Docs
description: Armazene metadados personalizados em objetos no Armazenamento do Azure e defina e recupere propriedades do sistema.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: 86bb7e736754cbc6a93bba5fff5d8d1877b1e3b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916573"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e recuperar as propriedades e os metadados

Os objetos no armazenamento do Azure oferecem suporte às propriedades do sistema e metadados definidos pelo usuário, além dos dados que eles contêm. Este artigo discute o gerenciamento das propriedades do sistema e dos metadados definidos pelo usuário com a [Biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriedades do sistema**:  Existem propriedades do sistema em cada recurso de armazenamento. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. As bibliotecas de clientes do Armazenamento do Microsoft Azure mantêm essas propriedades.

* **Metadados definidos pelo usuário**: Metadados definidos pelo usuário consistem em um ou mais pares de nome-valor que você especificar para um recurso de armazenamento do Azure. É possível usar metadados para armazenar valores adicionais com um recurso. Os valores de metadados são apenas para suas próprias finalidades e não afetam o comportamento do recurso.

Recuperar os valores da propriedade e dos metadados para um recurso de armazenamento é um processo de duas etapas. Antes de poder ler esses valores, será necessário buscá-los explicitamente, chamando o método **FetchAttributes** ou **FetchAttributesAsync**. A exceção é se você estiver chamando o método **Exists** ou **ExistsAsync** em um recurso. Ao chamar um desses métodos, o Armazenamento do Microsoft Azure chama o método **FetchAttributes** adequado nas abordagens como parte da chamada para o método **Exists**.

> [!IMPORTANT]
> Se você achar que a propriedade ou os valores de metadados de um recurso de armazenamento não foram preenchidos, verifique se o código chama o método **FetchAttributes** ou **FetchAttributesAsync**.
>
> Pares de nome/valor de metadados são cabeçalhos HTTP válidos e portanto devem cumprir todas as restrições que regem cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho HTTP válidos, podem conter apenas caracteres ASCII e devem ser tratados como diferencia maiusculas de minúsculas. Os valores de metadados que contém caracteres não ASCII devem ser codificada em Base64 ou codificado de URL.

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

## <a name="next-steps"></a>Próximos passos
* [Biblioteca do cliente de armazenamento do Azure para a referência do .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Biblioteca do cliente de armazenamento do Azure para o pacote NuGet do .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
