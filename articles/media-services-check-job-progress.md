<properties pageTitle="Como verificar o andamento do trabalho nos Serviços de Mídia - Azure" description="Saiba como usar o código do manipulador de eventos para acompanhar o andamento do trabalho e enviar atualizações de status. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





<h1>Como: Verificar o andamento do trabalho</h1>
Este artigo faz parte de uma série de introdução à programação dos Serviços de Mídia do Azure. O tópico anterior era [Como: Codificar um ativo](http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409).

Quando você executa trabalhos, geralmente precisa de uma maneira de acompanhar o andamento do trabalho. O exemplo de código a seguir define o manipulador de eventos StateChanged. Esse manipulador de eventos acompanha o andamento do trabalho e fornece o status atualizado, dependendo do estado. O código também define o método LogJobStop. Esse método auxiliar registra os detalhes de erros.

<pre><code>
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
}

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    builder.AppendLine("Media Services account location: " + _accountLocation);
    // Log job errors if they exist.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
</code></pre>
<h2>Próximas etapas</h2>
Agora que você sabe como criar um trabalho e acompanhar seu andamento, a próxima etapa é proteger os ativos. Para obter mais informações, consulte [Como proteger um ativo com os Serviços de Mídia do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).


<!--HONumber=42-->
