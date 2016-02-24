
<properties 
	pageTitle="Gerenciamento dos ativos e entidades relacionadas com o .NET SDK dos Serviços de Mídia" 
	description="Saiba como gerenciar ativos e entidades relacionadas com o SDK dos serviços de mídia para .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/09/2016"  
	ms.author="juliako"/>


#Gerenciamento dos ativos e entidades relacionadas com o .NET SDK dos Serviços de Mídia


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [REST](media-services-rest-manage-entities.md)


Este tópico mostra como realizar as seguintes tarefas de gerenciamento de serviços de mídia:

- Obter uma referência de ativo 
- Obter uma referência de trabalho 
- Listar todos os ativos 
- Listar trabalhos e ativos 
- Listar todas as políticas de acesso 
- Listar todos os localizadores
- Enumerar através de grandes coleções de entidades
- Excluir um ativo 
- Excluir um trabalho 
- Excluir uma política de acesso 

##Pré-requisitos 

Consulte [Configurar seu ambiente](media-services-set-up-computer.md)

##Obter uma Referência de Ativo

Uma tarefa frequente é obter uma referência a um ativo existente nos serviços de mídia. O exemplo de código a seguir mostra como obter uma referência de ativo da coleção de ativos no objeto de contexto do servidor, com base em uma ID de ativo. O exemplo de código a seguir usa uma consulta Linq para obter uma referência a um objeto IAsset existente.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##Obter uma Referência de Trabalho

Quando você trabalha com o processamento de tarefas em código de serviços de mídia, muitas vezes precisa obter uma referência a um trabalho existente com base em uma ID. O exemplo de código a seguir mostra como obter uma referência a um objeto do IJob da coleção de Trabalhos. Aviso Talvez você precise obter uma referência de trabalho ao iniciar um trabalho de codificação de longa duração e precise verificar o status do trabalho em um thread. Em casos como esse, quando o método retorna de um thread, você precisa recuperar uma referência atualizada para um trabalho.

	static IJob GetJob(string jobId)
	{
	    // Use a Linq select query to get an updated 
	    // reference by Id. 
	    var jobInstance =
	        from j in _context.Jobs
	        where j.Id == jobId
	        select j;
	    // Return the job reference as an Ijob. 
	    IJob job = jobInstance.FirstOrDefault();
	
	    return job;
	}

##Listar todos os Ativos

À medida que cresce o número de ativos que você tem no armazenamento, é útil listar seus ativos. O exemplo de código a seguir mostra como percorrer a coleção de ativos no objeto de contexto do servidor. Com cada ativo, o exemplo de código também grava alguns de seus valores de propriedade no console. Por exemplo, cada ativo pode conter vários arquivos de mídia. O exemplo de código grava todos os arquivos associados a cada ativo.



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Listar Trabalhos e Ativos

Uma tarefa importante relacionada é listar ativos com seu trabalho associado nos serviços de mídia. O exemplo de código a seguir mostra como listar cada objeto IJob e, em seguida, para cada trabalho, exibe as propriedades do trabalho, todas as tarefas relacionadas, todos os ativos e todos os ativos de entrada e de saída. O código neste exemplo pode ser útil para muitas outras tarefas. Por exemplo, se você deseja listar os ativos de saída de um ou mais trabalhos de codificação que você executou anteriormente, este código mostra como acessar os ativos de saída. Quando você tem uma referência a um ativo de saída, é possível entregar o conteúdo a outros usuários ou aplicativos baixando-o ou fornecendo URLs.

Para obter mais informações sobre as opções para a entrega de ativos, consulte [Fornecer ativos com o SDK dos Serviços de Mídia para .NET](media-services-deliver-streaming-content.md).

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Listar Todas as Políticas de Acesso

Nos serviços de mídia, você pode definir uma política de acesso em um ativo ou seus arquivos. Uma política de acesso define as permissões para um arquivo ou um ativo (o tipo de acesso e a duração). Em seu código de serviços de mídia, você normalmente define uma política de acesso criando um objeto IAccessPolicy e associá-la a um ativo existente. Em seguida, você pode criar um objeto ILocator, que permite que o fornecimento de acesso direto aos ativos nos serviços de mídia. O projeto do Visual Studio que acompanha esta série de documentação contém vários exemplos de código que mostram como criar e atribuir políticas de acesso e localizadores a ativos.

O exemplo de código a seguir mostra como listar todas as políticas de acesso no servidor e mostra o tipo de permissões associadas a cada um. Outra maneira útil para exibir as políticas de acesso é listar todos os objetos de ILocator no servidor e, em seguida, para cada localizador, você pode listar sua política de acesso associada usando sua propriedade AccessPolicy.

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##Listar todos os localizadores

Um localizador é uma URL que fornece um caminho direto para acessar um recurso, juntamente com as permissões para o ativo conforme definido pela política de acesso associada do localizador. Cada ativo pode ter uma coleção de objetos ILocator associados a ele em sua propriedade de Localizadores. O contexto do servidor também tem uma coleção de localizadores que contém todos os localizadores.

O exemplo de código a seguir lista todos os localizadores no servidor. Para cada localizador, ele mostra a Id da política de ativo e de acesso relacionada. Ele também exibe o tipo de permissões, a data de expiração e o caminho completo para o ativo.

Observe que um caminho de localizador para um ativo é apenas uma URL base para o ativo. Para criar um caminho direto para arquivos individuais para os quais um usuário ou aplicativo pode navegar, seu código deve adicionar o caminho do arquivo específico ao caminho do localizador. Para obter mais informações sobre como fazer isso, consulte [Fornecer ativos com o SDK dos Serviços de Mídia para .NET](media-services-deliver-streaming-content.md).

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}

## Enumerar através de grandes coleções de entidades

Ao consultar entidades, um limite de 1.000 entidades podem ser retornadas ao mesmo tempo porque a REST v2 pública limita os resultados da consulta a 1.000 resultados. Você precisa usar Ignorar e Levar ao enumerar através de grandes coleções de entidades.
	
A função a seguir faz um loop por todos os trabalhos na conta de Serviços de Mídia fornecida. Os Serviços de Mídia retornam 1.000 trabalhos da coleção de trabalhos. A função utiliza Ignorar e Levar para certificar-se de que todos os trabalhos sejam enumerados (caso você tenha mais de 1.000 trabalhos em sua conta).
	
	static void ProcessJobs()
	{
	    try
	    {
	
	        int skipSize = 0;
	        int batchSize = 1000;
	        int currentBatch = 0;
	
	        while (true)
	        {
	            // Loop through all Jobs (1000 at a time) in the Media Services account
	            IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
	            foreach (IJob job in _jobsCollectionQuery)
	            {
	                currentBatch++;
	                Console.WriteLine("Processing Job Id:" + job.Id);
	            }
	
	            if (currentBatch == batchSize)
	            {
	                skipSize += batchSize;
	                currentBatch = 0;
	            }
	            else
	            {
	                break;
	            }
	        }
	    }
	    catch (Exception ex)
	    {
	        Console.WriteLine(ex.Message);
	    }
	}

##Excluir um Ativo

O exemplo a seguir exclui um ativo.

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##Excluir um Trabalho

Para excluir um trabalho, você deve verificar o estado do trabalho, conforme indicado na propriedade Estado. Os Trabalhos que foram concluídos ou cancelados podem ser excluídos, enquanto os trabalhos que estão em alguns outros estados, como enfileirado, agendado ou em processamento devem ser cancelados primeiro e, em seguida, eles podem ser excluídos. O exemplo de código a seguir mostra um método para exclusão de um trabalho de verificação de estados de trabalho e a exclusão quando o estado é concluído ou cancelado. Esse código depende da seção anterior deste tópico para obter uma referência a um trabalho: Obter uma referência de trabalho.

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##Excluir uma Política de Acesso

O exemplo de código a seguir mostra como obter uma referência a uma política de acesso com base em uma Id de política e, em seguida, excluir a política.

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->