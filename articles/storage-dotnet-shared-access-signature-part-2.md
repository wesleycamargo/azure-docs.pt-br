<properties 
	pageTitle="Criar e usar um SAS com o serviço Blob | Microsoft Azure" 
	description="Explore a geração e o uso de assinaturas de acesso compartilhado com o serviço Blob" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>


# Assinaturas de acesso compartilhado, Parte 2: Criar e usar um SAS com o Serviço Blob

A [Parte 1](../storage-dotnet-shared-access-signature-part-1/) deste tutorial explorou as assinaturas de acesso compartilhado (SAS) e explicou as práticas recomendadas para sua utilização. A Parte 2 mostra como gerar e usar assinaturas de acesso compartilhado com o serviço Blob do Azure. Os exemplos são escritos em C# e usam a Biblioteca de Cliente do Armazenamento do Azure para .NET. Os cenários abordados incluem esses aspectos de trabalho com assinaturas de acesso compartilhado:

- Gerando uma assinatura de acesso compartilhado em um contêiner
- Gerando uma assinatura de acesso compartilhado em um blob
- Criando uma política de acesso armazenado para gerenciar assinaturas nos recursos de um contêiner
- Testando as assinaturas de acesso compartilhado de um aplicativo cliente

# Sobre este tutorial #
Neste tutorial, vamos nos concentrar em criar assinaturas de acesso compartilhado para contêineres e blobs, criando dois aplicativos de console. O primeiro aplicativo de console gera assinaturas de acesso compartilhado em um contêiner e em um blob. Esse aplicativo tem conhecimento das chaves de conta de armazenamento. O segundo aplicativo de console, que agirá como um aplicativo cliente, acessa os recursos do contêiner e do blob, usando as assinaturas de acesso compartilhado criadas com o primeiro aplicativo. Esse aplicativo usa as assinaturas de acesso compartilhado somente para autenticar seu acesso aos recursos de contêiner e de blob - ele desconhece as chaves de conta.

# Parte 1: Criar um aplicativo de console para gerar assinaturas de acesso compartilhado #

Primeiro, verifique se você tem a Biblioteca de Cliente do Armazenamento do Azure para .NET instalada. Você pode instalar o [Pacote NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet package") que contém a maioria dos assemblies mais atuais para a biblioteca de cliente. Esse é o método recomendado para garantir que você tenha as correções mais recentes. Você também pode baixar a biblioteca de cliente como parte da versão mais recente do [SDK do Azure para .NET](http://www.windowsazure.com/pt-br/downloads/).

No Visual Studio, crie um novo aplicativo de console do Windows e dê a ele o nome **GenerateSharedAccessSignatures**. Adicione referências a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll** usando uma das seguintes abordagens:

- 	Se você quiser instalar o pacote NuGet, primeiro instale a [Extensão do Gerenciador de Pacotes NuGet para Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). No Visual Studio, selecione **Projeto | Gerenciar Pacotes NuGet**, pesquise online por **Armazenamento do Azure** e siga as instruções de instalação.
- 	Como alternativa, localize os assemblies na sua instalação do SDK do Azure e adicione referências a eles.
 
Na parte superior do arquivo Program.cs, adicione a seguinte instrução **using**:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Edite o arquivo app.config para que ele contenha um parâmetro de configuração com uma cadeia de conexão que aponte para a sua conta de armazenamento. O arquivo app.config deve ser semelhante a este:

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

## Gerar um URI de assinatura de acesso compartilhado para um contêiner ##

Em primeiro lugar, adicionaremos um método para gerar uma assinatura de acesso compartilhado em um novo contêiner. Nesse caso, a assinatura não está associada a uma política de acesso armazenado, portanto, ela transporta no URI as informações que indicam sua hora de expiração e as permissões concedidas por ela.

Primeiro, adicione código ao método **Main()** para autenticar o acesso à sua conta de armazenamento e criar um novo contêiner:

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Insert calls to the methods created below here...
	    
	    //Require user input before closing the console window.
	    Console.ReadLine();
    }

Em seguida, adicione um novo método que gere a assinatura de acesso compartilhado para o contêiner e retorne o URI de assinatura:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
	    //Set the expiry time and permissions for the container.
	    //In this case no start time is specified, so the shared access signature becomes valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
	    
	    //Generate the shared access signature on the container, setting the constraints directly on the signature.
	    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

Adicione as linhas a seguir na parte inferior do método **Main()**, antes da chamada de **Console.ReadLine()**, para chamar **GetContainerSasUri()** e grave o URI da assinatura na janela do console:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compile e execute para gerar a saída do URI de assinatura de acesso compartilhado para o novo contêiner. O URI será semelhante a este:       

https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Depois de executar o código, a assinatura de acesso compartilhado criada no contêiner será válida durante as próximas quatro horas. A assinatura concede uma permissão de cliente para listar os blobs no contêiner e gravar um novo blob no contêiner.

## Gerar um URI de assinatura de acesso compartilhado para um blob ##

Em seguida, escreveremos um código semelhante para criar um novo blob dentro do contêiner e gerar uma assinatura de acesso compartilhado para ele. Essa assinatura de acesso compartilhado não está associada a uma política de acesso armazenado, portanto, ela inclui as informações de hora de início, hora de expiração e permissão no URI.

Adicione um novo método que criará um novo blob e escreva um texto nele; em seguida, uma assinatura de acesso compartilhado será gerada e retornará o URI de assinatura:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Set the expiry time and permissions for the blob.
	    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
	    //The shared access signature will be valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
	    
	    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
	    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

Na parte inferior do método **Main()**, adicione as linhas a seguir para chamar **GetBlobSasUri()**, antes da chamada para **Console.ReadLine()**, e grave o URI de assinatura de acesso compartilhado na janela de console:    
    
    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();
    

Compile e execute para gerar a saída do URI de assinatura de acesso compartilhado para o novo blob. O URI será semelhante a este:

https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

## Criar uma política de acesso armazenado no contêiner ##

Agora, vamos criar uma política de acesso armazenado no contêiner, que definirá as restrições de qualquer assinatura de acesso compartilhado associada a ela.

Nos exemplos anteriores, especificamos a hora de início (implícita ou explicitamente), a hora de expiração e as permissões no próprio URI de assinatura de acesso compartilhado. Nos exemplos a seguir, especificaremos isso na política de acesso armazenado, e não na assinatura de acesso compartilhado. Isso nos permitirá alterar essas restrições sem reemitir a assinatura de acesso compartilhado.

Observe que é possível ter uma ou mais restrições na assinatura de acesso compartilhado e o restante na política de acesso armazenado. No entanto, você só pode especificar a hora de início, a hora de expiração e permissões em um local ou outro; por exemplo, você não pode especificar permissões na assinatura de acesso compartilhado e especificá-las também na política de acesso armazenado.

Adicione um novo método que criará uma nova política de acesso armazenado e retornará o nome da política:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
	    //Create a new stored access policy and define its constraints.
	    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
	    {
		    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
		    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
	    };
	    
	    //Get the container's existing permissions.
	    BlobContainerPermissions permissions = new BlobContainerPermissions();
	    
	    //Add the new policy to the container's permissions.
	    permissions.SharedAccessPolicies.Clear();
	    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
	    container.SetPermissions(permissions);
    }

Na parte inferior do método **Main()**, antes da chamada para **Console.ReadLine()**, adicione as linhas a seguir para chamar o método **CreateSharedAccessPolicy()**:    

    //Create an access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

## Gerar um URI de assinatura de acesso compartilhado no contêiner que usa uma política de acesso ##

Em seguida, criaremos outra assinatura de acesso compartilhado no contêiner criado anteriormente, mas, desta vez, associaremos a assinatura à política de acesso criada no exemplo anterior.

Adicione um novo método para gerar outra assinatura de acesso compartilhado no contêiner:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Generate the shared access signature on the container. In this case, all of the constraints for the 
	    //shared access signature are specified on the stored access policy.
	    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }
    
Na parte inferior do método **Main()**, antes da chamada para **Console.ReadLine()**, adicione as linhas a seguir para chamar o método **GetContainerSasUriWithPolicy**:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

## Gerar um URI de assinatura de acesso compartilhado no blob que usa uma política de acesso ##

Finalmente, adicionaremos um método semelhante para criar outro blob e gerar uma assinatura de acesso compartilhado associada a uma política de acesso.

Adicione um novo método para criar um blob e gerar uma assinatura de acesso compartilhado:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
	    "A stored access policy defines the constraints for the signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Generate the shared access signature on the blob.
	    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

Na parte inferior do método **Main()**, antes da chamada para **Console.ReadLine()**, adicione as linhas a seguir para chamar o método **GetBlobSasUriWithPolicy**:    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Agora, o método **Main()** deverá ser semelhante a este em sua totalidade. Execute-o para gravar os URIs de assinatura de acesso compartilhado na janela de console e, em seguida, copiá-los e colá-los em um arquivo de texto a ser usado na segunda parte deste tutorial.    

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Generate a SAS URI for the container, without a stored access policy.
	    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, without a stored access policy.
	    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
	    Console.WriteLine();
	    
	    //Create an access policy on the container, which may be optionally used to provide constraints for 
	    //shared access signatures on the container and the blob.
	    string sharedAccessPolicyName = "tutorialpolicy";
	    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
	    
	    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    Console.ReadLine();
    }

Ao executar o aplicativo de console GenerateSharedAccessSignatures, você verá uma saída semelhante à seguinte na janela do console. Essas são as assinaturas de acesso compartilhado que você usará na Parte 2 do tutorial.

![sas-console-output-1][sas-console-output-1]

# Parte 2: Criar um aplicativo de console para testar as assinaturas de acesso compartilhado #

Para testar as assinaturas de acesso compartilhado criadas nos exemplos anteriores, criaremos um segundo aplicativo de console que utiliza as assinaturas para executar operações no contêiner e em um blob.

Observe que, se mais de quatro horas tiverem decorrido desde a conclusão da primeira parte do tutorial, as assinaturas geradas quando a hora de expiração foi definida para quatro horas não serão mais válidas. Da mesma forma, as assinaturas associadas à política de acesso armazenado expirarão após 10 horas. Se um desses intervalos, ou ambos, tiverem decorrido, você deverá executar o código no primeiro aplicativo de console para gerar novas assinaturas de acesso compartilhado a serem usadas na segunda parte do tutorial.

No Visual Studio, crie um novo aplicativo de console do Windows e dê a ele o nome **ConsumeSharedAccessSignatures**. Adicione referências a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, como anteriormente.

Na parte superior do arquivo Program.cs, adicione a seguinte instrução **using**:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
No corpo do método **Main()**, adicione as seguintes constantes e atualize seus valores nas assinaturas de acesso compartilhado geradas na parte 1 do tutorial.

    static void Main(string[] args)
    {
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }
    
## Adicionar um método para testar operações de contêiner usando uma assinatura de acesso compartilhado ##

Em seguida, adicionaremos um método que testará algumas operações representativas de contêiner usando uma assinatura de acesso compartilhado no contêiner. Observe que a assinatura de acesso compartilhado é usada para retornar uma referência ao contêiner, autenticando o acesso ao contêiner com base na assinatura sozinha.

Adicione o seguinte método ao Program.cs:


	static void UseContainerSAS(string sas)
	{
	    //Try performing container operations with the SAS provided.
	
	    //Return a reference to the container using the SAS URI.
	    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
	
	    //Create a list to store blob URIs returned by a listing operation on the container.
	    List<Uri> blobUris = new List<Uri>();
	
	    try
	    {
	        //Write operation: write a new blob to the container. 
	        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
	        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
	        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	        msWrite.Position = 0;
	        using (msWrite)
	        {
	            blob.UploadFromStream(msWrite);
	        }
	        Console.WriteLine("Write operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Write operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //List operation: List the blobs in the container, including the one just added.
	        foreach (ICloudBlob blobListing in container.ListBlobs())
	        {
	            blobUris.Add(blobListing.Uri);
	        }
	        Console.WriteLine("List operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("List operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Read operation: Get a reference to one of the blobs in the container and read it. 
	        CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
	        MemoryStream msRead = new MemoryStream();
	        msRead.Position = 0;
	        using (msRead)
	        {
	            blob.DownloadToStream(msRead);
	            Console.WriteLine(msRead.Length);
	        }
	        Console.WriteLine("Read operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Read operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	    Console.WriteLine();
	
	    try
	    {
	        //Delete operation: Delete a blob in the container.
	        CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
	        blob.Delete();
	        Console.WriteLine("Delete operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Delete operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }        
	}


Atualize o método **Main()** para chamar **UseContainerSAS()** com as duas assinaturas de acesso compartilhado que você criou no contêiner:


	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}


## Adicionar um método para testar operações de blob usando uma assinatura de acesso compartilhado ##

Finalmente, adicionaremos um método que testará algumas operações representativas de blob usando uma assinatura de acesso compartilhado no blob. Nesse caso, usamos o construtor **CloudBlockBlob(String)** passando a assinatura de acesso compartilhado para retornar uma referência ao blob. Nenhuma outra autenticação é obrigatória; ela se baseia na assinatura sozinha.

Adicione o seguinte método ao Program.cs:


	static void UseBlobSAS(string sas)
	{
	    //Try performing blob operations using the SAS provided.
	
	    //Return a reference to the blob using the SAS URI.
	    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));
	
	    try
	    {
	        //Write operation: write a new blob to the container. 
	        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
	        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	        msWrite.Position = 0;
	        using (msWrite)
	        {
	            blob.UploadFromStream(msWrite);
	        }
	        Console.WriteLine("Write operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Write operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Read operation: Read the contents of the blob.
	        MemoryStream msRead = new MemoryStream();
	        using (msRead)
	        {
	            blob.DownloadToStream(msRead);
	            msRead.Position = 0;
	            using (StreamReader reader = new StreamReader(msRead, true))
	            {
	                string line;
	                while ((line = reader.ReadLine()) != null)
	                {
	                    Console.WriteLine(line);
	                }
	            }
	        }
	        Console.WriteLine("Read operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Read operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Delete operation: Delete the blob.
	        blob.Delete();
	        Console.WriteLine("Delete operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Delete operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }        
	}


Atualize o método **Main()** para chamar **UseBlobSAS()** com as duas assinaturas de acesso compartilhado que você criou no blob:

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}

Execute o aplicativo de console e observe a saída para ver quais operações são permitidas para quais assinaturas. A saída na janela de console será semelhante a esta:

![sas-console-output-2][sas-console-output-2]

# Próximas etapas #

[Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS](../storage-dotnet-shared-access-signature-part-1/)

[Gerenciar o acesso aos recursos de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/ee393343.aspx)

[Delegando acesso com uma assinatura de acesso compartilhado (API REST)](http://msdn.microsoft.com/pt-br/library/windowsazure/ee395415.aspx)

[Introdução à Tabela e à Fila SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG

<!--HONumber=42-->
