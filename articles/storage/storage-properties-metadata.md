
<properties 
  pageTitle="Definir e recuperar as propriedades e os metadados para objetos no Armazenamento do Azure | Microsoft Azure" 
  description="Armazene metadados personalizados em objetos no Armazenamento do Azure e defina e recupere propriedades do sistema." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# Definir e Recuperar as Propriedades e os Metadados #

## Visão geral

Os objetos no Armazenamento do Azure suportam as propriedades do sistema e os metadados definidos pelo usuário, além dos dados que eles contêm:

*   **Propriedades do sistema.** Existem propriedades do sistema em cada recurso de armazenamento. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca de cliente do armazenamento do Azure os mantém para você.  

*   **Metadados definidos pelo usuário.** Os metadados definidos pelo usuário são metadados que você especifica em um determinado recurso, na forma de um par de nome-valor. É possível usar os metadados para armazenar valores adicionais com um recurso de armazenamento; esses valores são para suas próprias finalidades apenas e não afetam o comportamento do recurso.

## Configurando e Recuperando as Propriedades

Recuperar os valores da propriedade e dos metadados para um recurso de armazenamento é um processo de duas etapas. Antes de ler esses valores, é preciso buscá-los explicitamente chamando o método **FetchAttributes** ou **FetchAttributesAsync**.

> [AZURE.IMPORTANT]Os valores de propriedade e metadados para um recurso de armazenamento não são preenchidos, a menos que você chame um dos métodos **FetchAttributes**.

Para definir as propriedades em um blob, especifique o valor da propriedade, em seguida, chame o método **SetProperties** ou **SetPropertiesAsync**.

O exemplo de código a seguir cria um contêiner e escreve os valores da propriedade em uma janela do console:

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
 
Para recuperar os metadados, chame o método **FetchAttributes** no blob ou no contêiner para preencher a coleção **Metadados**, em seguida, leia os valores.

O exemplo de código a seguir cria um novo contêiner e define metadados para ele, então, lê os valores dos metadados de volta:

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## Consulte também  

- [Referência da Biblioteca do Cliente de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Introdução ao Armazenamento de Blobs para o .NET](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=Sept15_HO2-->