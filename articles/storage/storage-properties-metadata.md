
<properties 
  pageTitle="Definir e recuperar as propriedades e os metadados para objetos no Armazenamento do Azure | Microsoft Azure" 
  description="Armazene metadados personalizados em objetos no Armazenamento do Azure e defina e recupere propriedades do sistema." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="carmonm" 
  editor="tysonn"/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="02/20/2016" 
  ms.author="tamram"/>


# Definir e Recuperar as Propriedades e os Metadados #

## Visão geral

Os objetos no Armazenamento do Azure suportam as propriedades do sistema e os metadados definidos pelo usuário, além dos dados que eles contêm:

*   **Propriedades do sistema.** Existem propriedades do sistema em cada recurso de armazenamento. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca de cliente do armazenamento do Azure os mantém para você.  

*   **Metadados definidos pelo usuário.** Os metadados definidos pelo usuário são metadados que você especifica em um determinado recurso, na forma de um par de nome-valor. É possível usar os metadados para armazenar valores adicionais com um recurso de armazenamento; esses valores são para suas próprias finalidades apenas e não afetam o comportamento do recurso.

Recuperar os valores da propriedade e dos metadados para um recurso de armazenamento é um processo de duas etapas. Antes de ler esses valores, é preciso buscá-los explicitamente chamando o método **FetchAttributes**.

> [AZURE.IMPORTANT] Os valores de propriedade e metadados para um recurso de armazenamento não são preenchidos, a menos que você chame um dos métodos **FetchAttributes**.

## Configurando e Recuperando as Propriedades

Para recuperar os valores da propriedade, chame o método **FetchAttributes** no blob ou no contêiner para preencher as propriedades e leia os valores.

Para definir as propriedades em um objeto, especifique o valor da propriedade, em seguida, chame o método **SetProperties**.

O exemplo de código a seguir cria um contêiner e escreve alguns dos valores da propriedade em uma janela do console:

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## Configurando e Recuperando os Metadados

Você pode especificar os metadados como um ou mais pares de nome-valor em um recurso de contêiner ou blob. Para definir os metadados, adicione pares de nome-valor à coleção **Metadados** no recurso, em seguida, chame o método **SetMetadata** para salvar os valores no serviço.

> [AZURE.NOTE]\: O nome dos metadados deve obedecer às convenções de nomenclatura para os identificadores de C#.
 
O exemplo de código a seguir define os metadados em um contêiner. Um valor é definido usando o método **Add** da coleção. O outro valor é definido usando a sintaxe implícita de chave/valor. Ambos são válidos.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Para recuperar os metadados, chame o método **FetchAttributes** no blob ou no contêiner para preencher a coleção **Metadados**, em seguida, leia os valores, conforme mostrado no exemplo abaixo.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## Veja também  

- [Biblioteca do Cliente de Armazenamento do Azure para a referência do .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Biblioteca do Cliente de Armazenamento do Azure para o pacote do .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 

<!---HONumber=AcomDC_0224_2016-->