<properties
	pageTitle="Tutorial - Introdução à Biblioteca de Aplicativos do Lote do Azure para .NET"
	description="Conheça os conceitos básicos sobre os Aplicativos do Lote do Azure e como desenvolver com um cenário simples"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# Introdução à Biblioteca de Aplicativos do Lote do Azure para .NET
Este tutorial mostra como executar cargas de trabalho de computação paralela no Azure Batch usando o serviço Batch Apps.

Batch Apps é um recurso do Azure Batch que oferece um modo, centrado em aplicativos, de gerenciar e executar cargas de trabalho em Lotes. Usando o SDK do Batch Apps, você pode criar pacotes para habilitar um aplicativo para uso em Lote, além de implantá-los em sua própria conta ou torná-los disponíveis para outros usuários do Batch. O Batch também fornece gerenciamento de dados, monitoramento de trabalhos, diagnóstico e registro em log internos e suporte para dependências entre as tarefas. Além disso, ele inclui um portal de gerenciamento no qual você pode gerenciar trabalhos, exibir logs e baixar saídas sem precisar escrever seu próprio cliente.

No cenário do Batch Apps, você escreve código usando o SDK de Nuvem do Batch Apps para dividir os trabalhos em tarefas paralelas, descrever quaisquer eventuais dependências entre essas tarefas e especificar como cada tarefa deve ser executada. Esse código é implantado na conta do Batch. Os clientes podem, em seguida, executar trabalhos especificando o tipo de trabalho e os arquivos de entrada para uma API REST.

>[AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/). Você pode usar o NuGet para obter ambos o assembly de <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Nuvem do Batch Apps</a> e o assembly de <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Cliente do Batch Apps</a>. Depois de criar seu projeto no Visual Studio, clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Você também pode baixar a extensão do Visual Studio para Batch Apps, que inclui um modelo de projeto para aplicativos de habilitação de nuvem e a capacidade de implantar um aplicativo <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">aqui</a> ou pesquisando por **Batch Apps** no Visual Studio, pelo item de menu Extensões e Atualizações. Você também pode encontrar <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">exemplos de ponta a ponta no MSDN.</a>
>

###Princípios básicos do Azure Batch Apps
O Batch é projetado para funcionar com aplicativos de computação intensiva existentes. Ele utiliza o código do aplicativo existente e executa-o em um ambiente dinâmico, virtualizado e para uso geral. Para habilitar um aplicativo para trabalhar com o Batch Apps, há algumas coisas que precisam ser feitas:

1.	Prepare um arquivo zip com os executáveis do seu aplicativo existente – os mesmos que seriam executados em um cluster ou farm de servidores tradicional – e quaisquer arquivos de suporte requeridos por esse aplicativo. Este arquivo zip é então carregado à sua conta do Batch usando o portal de gerenciamento ou a API REST.
2.	Crie um arquivo zip com os "assemblies de nuvem" que enviam suas cargas de trabalho para o aplicativo e carregue esse zip por meio do portal de gerenciamento ou da API REST. Um assembly de nuvem contém dois componentes que são compilados com o SDK de nuvem:
	1.	Divisor de Trabalho – que divide o trabalho em tarefas que podem ser processadas independentemente. Por exemplo, em um cenário de animação, o divisor de trabalho abriria um trabalho de filme e dividiria-o em quadros individuais.
	2.	Processador de Tarefa – que chama o executável do aplicativo para uma determinada tarefa. Por exemplo, em um cenário de animação, o processador de tarefa invocaria um programa de renderização para renderizar o único quadro especificado pela tarefa em questão.
3.	Fornecem uma maneira para enviar trabalhos ao aplicativo habilitado no Azure. Isso pode ser um plug-in no aplicativo da interface do usuário ou um portal da Web, ou até mesmo um serviço autônomo como parte do pipeline de execução. Consulte os <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">exemplos</a> no MSDN para obter exemplos.



###Conceitos principais do Batch Apps
O modelo de uso e programação do Batch Apps envolve os conceitos principais a seguir:

####Trabalhos
Um **trabalho** é uma unidade de trabalho enviada pelo usuário. Quando um trabalho é enviado, o usuário especifica o tipo de trabalho, quaisquer eventuais configurações para esse trabalho e os dados necessários para ele. A implementação habilitada pode resolver esses detalhes no lugar do usuário em alguns casos ou então, em outros casos, o usuário pode fornecer essas informações explicitamente por meio do cliente. Um trabalho tem resultados, que são retornados. Cada trabalho tem uma saída primária e, opcionalmente, uma saída de visualização. Trabalhos também podem retornar saídas extras se desejado.

####Tarefas
Uma **tarefa** é uma unidade de trabalho a ser executada como parte de um trabalho. Quando um usuário envia um trabalho, ele é dividido em tarefas de menor porte. O serviço então processa essas tarefas individuais e compila os resultados das tarefas em uma saída geral do trabalho. A natureza das tarefas depende do tipo de trabalho. O Divisor de Trabalho define como um trabalho é dividido em tarefas, orientado pelo conhecimento de quais partes do trabalho o aplicativo foi projetado para processar. Saídas de tarefas também podem ser baixadas individualmente e pode ser úteis em alguns casos, por exemplo, quando um usuário deseja baixar tarefas individuais provenientes de um trabalho de animação.

####Tarefas de mesclagem
Uma **tarefa de mesclagem** é um tipo especial de tarefa, que reúne os resultados das tarefas individuais nos resultados do trabalho final. Para um trabalho de renderização de filme, a tarefa de mesclagem pode montar os quadros renderizados em um filme ou zipar todos os quadros renderizados em um único arquivo. Todo trabalho inclui uma tarefa de mesclagem, mesmo que nenhuma "mesclagem" propriamente dita seja necessária.

####Arquivos
Um **arquivo** é uma unidade de dados usados como entrada para um trabalho. Um trabalho pode não ter nenhum arquivo de entrada associado a ele; pode também ter um, ou muitos. O mesmo arquivo pode ser usado em vários trabalhos, por exemplo, para um trabalho de renderização de filme, os arquivos podem ser texturas, modelos etc. Para um trabalho de análise de dados, os arquivos podem ser um conjunto de observações ou de medidas.

###Habilitando o aplicativo em nuvem
Seu aplicativo deve conter uma propriedade ou campo estático, com todos os detalhes desse aplicativo. Essa propriedade ou campo especifica o nome do aplicativo e o tipo de trabalho ou tipos de trabalho realizados pelo aplicativo. Isso é fornecido ao usar o modelo no SDK, que pode ser baixado por meio da Galeria do Visual Studio.

Aqui está um exemplo de uma declaração de aplicativos de nuvem para uma carga de trabalho paralela:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implementando o Divisor de Trabalho e o Processador de Tarefa
Para cargas de trabalho excessivamente paralelas, você deve implementar um divisor de trabalho e um processador de tarefa.

####Implementando JobSplitter.SplitIntoTasks
Sua implementação do SplitIntoTasks deve retornar como resultado uma sequência de tarefas. Cada tarefa representa um trabalho separado, que será colocado em fila para processamento por um nó de computação. Cada tarefa deverá ser independente e deverá ser configurada com todas as informações de que o processador de tarefa precisará.

As tarefas especificadas com o divisor de trabalho são representadas como objetos TaskSpecifier. O TaskSpecifier tem várias propriedades que podem ser definidas antes do retorno da tarefa.


-	O TaskIndex é ignorado, mas está disponível para processadores de tarefa. Você pode usá-lo para passar um índice ao processador de tarefa. Se você não precisar passar um índice, você não precisará definir essa propriedade.
-	A Parameters é uma coleção vazia, por padrão. Você pode adicionar itens a ela substituí-la por uma nova coleção. Você pode copiar entradas da coleção de parâmetros do trabalho usando um dos métodos WithJobParameters ou WithAllJobParameters.  


A RequiredFiles é uma coleção vazia por padrão. Você pode adicionar itens a ela substituí-la por uma nova coleção. Você pode copiar entradas da coleção de arquivos de trabalho usando um dos métodos RequiringJobFiles ou RequiringAllJobFiles.

Você pode especificar uma tarefa que depende de uma outra tarefa específica. Para fazer isso, defina a propriedade TaskSpecifier.DependsOn, passando a ID da tarefa da qual ela depende:

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

A tarefa não será executada até que a saída da tarefa da qual ela depende esteja disponível.

Você também pode especificar que todo um grupo de tarefas não inicie o processamento até que outro grupo tenha sido totalmente concluído. Nesse caso, você pode definir a propriedade TaskSpecifier.Stage. Tarefas com um determinado valor de Stage não começarão a ser processadas até que todas as tarefas com valores mais baixos de Stage tiverem sido concluídas; por exemplo, tarefas com Stage 3 não começarão seu processamento até que todas as tarefas com Stage 0, 1 ou 2 tenham terminado. As propriedades Stage precisam começar por 0 e a sequência de estágios não pode apresentar lacunas; a propriedade SplitIntoTasks deve retornar as tarefas segundo sua ordem de estágios: por exemplo, é um erro retornar uma tarefa com Stage 0 após uma tarefa com a Stage 1.

Todos os trabalhos paralelos terminam com uma tarefa especial chamada tarefa de mesclagem. A função da tarefa de mesclagem é combinar os resultados das tarefas de processamento paralelo em um resultado final. O Batch Apps cria automaticamente a tarefa de mesclagem para você.

Em casos raros, você talvez queira controlar explicitamente a tarefa de mesclagem, por exemplo, para personalizar os parâmetros dessa tarefa. Nesse caso, você pode especificar a tarefa de mesclagem, retornando um TaskSpecifier com sua propriedade IsMerge definida como true. Isso fará com que a tarefa de mesclagem automática seja substituída. Se você criar uma tarefa de mesclagem explícita:

-	Você pode criar apenas uma tarefa de mesclagem explícita
-	Ele deve ser a última tarefa na sequência
-	Ele deve ter IsMerge definida como true e todas as outras tarefas devem ter IsMerge definida como false  


No entanto, lembre-se de que normalmente você não precisa criar a tarefa de mesclagem explicitamente.

O código a seguir demonstra uma implementação simples de SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implementando ParallelTaskProcessor.RunExternalTaskProcess

A RunExternalTaskProcess é chamada para cada tarefa de mesclagem não retornada do divisor de trabalho. Ela deve invocar o aplicativo com os argumentos apropriados e retornar uma coleção de saídas, que precisam ser mantidas para uso posterior.

O fragmento a seguir mostra como chamar um programa nomeado como application.exe. Observe que você pode usar o método auxiliar ExecutablePath para criar caminhos de arquivo absolutos.

A classe ExternalProcess do SDK de nuvem fornece lógica auxiliar útil para a execução dos executáveis de seu aplicativo. A ExternalProcess pode se encarregar do cancelamento, traduzindo códigos de saída em exceções, capturando saídas/erros padrão e configurando variáveis de ambiente. Além disso, você pode usar a classe .NET Process diretamente para executar seus programas, se preferir.

O método RunExternalTaskProcess retorna um TaskProcessResult, que inclui uma lista de arquivos de saída. Isso deve incluir pelo menos todos os arquivos necessários para a mesclagem; você também pode, opcionalmente, retornar arquivos de log, arquivos de visualização e arquivos intermediários (por exemplo, para fins de diagnóstico em caso de falha na tarefa). Observe que seu método retorna os caminhos de arquivo, não o conteúdo do arquivo.

Cada arquivo deve ser identificado com o tipo de saída que contém: saída (ou seja, parte da eventual saída do trabalho), visualização, log ou intermediária. Esses valores são originados da enumeração TaskOutputFileKind. O fragmento a seguir retorna uma única saída de tarefa e nenhuma visualização ou log. O método TaskProcessResult.FromExternalProcessResult simplifica o cenário comum de capturar o código de saída, a saída do processador e arquivos de saída de um programa de linha de comando:

O código a seguir demonstra uma implementação simples de ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implementando ParallelTaskProcessor.RunExternalMergeProcess

Esse método é chamado para a tarefa de mesclagem. Ele deve invocar o aplicativo para combinar os resultados das tarefas anteriores de modo apropriado para seu aplicativo, além de retornar a saída combinada.

A implementação de RunExternalMergeProcess é muito semelhante à de RunExternalTaskProcess, exceto por:

-	RunExternalMergeProcess consumir as saídas de tarefas anteriores, em vez de arquivos de entrada do usuário. Você deve, portanto, descobrir os nomes dos arquivos que deseja processar com base na ID de tarefa, em vez de usar como referência a coleção Task.RequiredFiles.
-	RunExternalMergeProcess retorna um arquivo JobOutput e, opcionalmente, um arquivo JobPreview.


O código a seguir demonstra uma implementação simples de ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Enviando trabalhos para o Batch Apps
Um trabalho descreve uma carga de trabalho a ser executada e precisa incluir todas as informações sobre a carga de trabalho, exceto o conteúdo do arquivo. Por exemplo, o trabalho contém definições de configuração que partem do cliente, passam pelo divisor de trabalho e chegam por fim às tarefas. Os exemplos fornecidos no MSDN são exemplos de como enviar trabalhos e fornecer vários clientes, inclusive um portal da Web e um cliente de linha de comando.

<!---HONumber=July15_HO4-->