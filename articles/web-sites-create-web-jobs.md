<properties 
	pageTitle="Use o Trabalhos Web para executar tarefas em segundo plano nos Sites do Microsoft Azure" 
	description="Saiba como executar tarefas em segundo plano em sites do Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="timamm" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2014" 
	ms.author="cephalin"/>

#Utilize os Trabalhos Web para executar tarefas em segundo plano nos Sites do Azure

Websites do Azure permitem que você execute programas ou scripts em seu site da Web de três maneiras: sob demanda, continuamente ou em uma agenda. Não há nenhum custo adicional para utilizar Trabalhos Web do Microsoft Azure.

Este artigo mostra como implantar Trabalhos Web utilizando o Portal de gerenciamento do Azure. Para obter informações sobre como implantar utilizando o Visual Studio ou um processo de entrega contínua, consulte [Como implantar Trabalhos Web do Azure para sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/websites-dotnet-deploy-webjobs).

O SDK de Trabalhos Web do Azure simplifica muitas tarefas de programação de Trabalhos Web. Para obter mais informações, consulte [O que é o SDK de Trabalhos Web](../websites-dotnet-webjobs-sdk).

## Sumário ##
- [Tipos de arquivo aceitáveis para scripts](#acceptablefiles)
- [Criar uma tarefa sob demanda](#CreateOnDemand)
- [Criar uma tarefa em execução continuamente](#CreateContinuous)
- [Criar uma tarefa agendada](#CreateScheduled)
	- [Trabalhos agendados e Agendador do Azure](#Scheduler)
- [Exibir o histórico do trabalho](#ViewJobHistory)
- [Observações](#WHPNotes)
- [Próximas etapas](#NextSteps)

## <a name="acceptablefiles"></a>Tipos de arquivo aceitáveis para scripts ou programas

Os seguintes tipos de arquivo são aceitos:

* .cmd, .bat, .exe (usando o cmd do Windows)
* .ps1 (usando o PowerShell)
* .sh (usando bash)
* .php (usando php)
* .py (usando o python)
* .js (usando nó)

## <a name="CreateOnDemand"></a>Criar uma tarefa sob demanda

1. Na barra de comandos da página **Trabalhos da Web**, clique em **Adicionar**. A caixa de diálogo **Novo Trabalho** é exibida.
	
	![Tarefa sob demanda][OnDemandWebJob]
	
2. Em **Nome**, forneça um nome para a tarefa. O nome deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "_".
	
3. Na caixa **Conteúdo (Arquivos ZIP Máximo de 100 MB)**, navegue até o arquivo zip que contém o script. O arquivo zip deve conter o executável (.exe, .cmd, .bat, .sh, .php, .py, .js), bem como os arquivos de suporte necessários para executar o script ou o programa.
	
4. Na caixa **Como Executar**, escolha **Executado sob Demanda**.
	
5. Marque a caixa de seleção no canto inferior direito da caixa de diálogo para carregar o script no site. O nome especificado para a tarefa aparece na lista:
	
	![Lista de tarefas][WebJobsList]
	
6. Para executar o script, selecione seu nome na lista e clique em **Executar uma vez** na barra de comandos na parte inferior da página do portal.
	
	![Executar uma vez][RunOnce]

## <a name="CreateContinuous"></a>Criar uma tarefa em execução continuamente

1. Para criar uma tarefa em execução continuamente, siga as mesmas etapas para criar uma tarefa que é executada uma vez, mas na caixa **Como Executar**, escolha **Executar continuamente**.
	
	![Nova tarefa contínua][NewContinuousJob]
	
2. Para iniciar ou parar uma tarefa em execução continuamente, selecione a tarefa na lista e clique em **Iniciar** ou **Parar** na barra de comandos.

> [AZURE.NOTE]  Se seu site for executado em mais de uma instância, uma tarefa em execução contínua será executada em todas as suas instâncias. Tarefas sob demanda e agendadas são executadas em uma única instância selecionada para o balanceamento de carga pelo Microsoft Azure.

> [AZURE.NOTE] Para tarefas contínuas, é recomendável habilitar **Sempre ativo** na página Configurar de seu site. O recurso Sempre Ativo, disponível nos modos Básico e Padrão, impede que os sites sejam descarregados, mesmo que tenham permanecido ociosos por algum tempo. Se seu site estiver sempre carregado, a tarefa em execução contínua poderá ser executada de maneira mais confiável. 

## <a name="CreateScheduled"></a>Criar uma tarefa agendada

1. Para criar uma tarefa agendada, siga as mesmas etapas anteriores, mas, na caixa **Como Executar**, escolha **Executar segundo um cronograma**.
	
	![Novo trabalho agendado][NewScheduledJob]
	
2. Escolha a **Região do Agendador** para seu trabalho e, em seguida, clique na seta na parte inferior direita da caixa de diálogo para prosseguir para a próxima tela.

3. Na caixa de diálogo **Criar trabalho**, escolha o tipo de **Recorrência** desejado: **Trabalho avulso** ou **Trabalho recorrente**.
	
	![Agendar recorrência][SchdRecurrence]
	
4. Escolha também uma hora **Inicial**: **Agora** ou **Em uma hora específica**.
	
	![Agendar hora inicial][SchdStart]
	
5. Se desejar iniciar em uma hora específica, escolha os valores da hora inicial em **Início em**.
	
	![Agendar o início em uma hora específica][SchdStartOn]
	
6. Se escolher um trabalho recorrente, você tem a opção **Repetir a Cada** para especificar a frequência de ocorrência e a opção **Término em** para especificar uma hora de término.
	
	![Agendar recorrência][SchdRecurEvery]
	
7. Se escolher **Semanas**, você poderá selecionar a caixa **Em uma agenda específica** caixa e especificar os dias da semana em que deseja que o trabalho seja executado.
	
	![Agendar dias da semana][SchdWeeksOnParticular]
	
8. Se você escolher **Meses** e selecionar a caixa **Em uma Agenda Específica**, poderá definir o trabalho a ser executado em determinados **Dias** numerados do mês. 
	
	![Agendar datas específicas no mês][SchdMonthsOnPartDays]
	
9. Se você escolher **Dias da Semana**, poderá selecionar em qual dia ou dias da semana no mês deseja que o trabalho seja executado.
	
	![Agendar dias da semana específicos em um mês][SchdMonthsOnPartWeekDays]
	
10. Finalmente, você também pode usar a opção **Ocorrências** para escolher em qual semana do mês (primeira, segunda, terceira etc.) deseja que o trabalho seja executado em dias da semana especificados.
	
	![Agendar dias da semana específicos em semanas específicas em um mês][SchdMonthsOnPartWeekDaysOccurences]
	
11. Depois que você tiver criado um ou mais trabalhos, os nomes serão exibidos na guia Trabalhos Web com status, tipo de cronograma e outras informações. As informações históricas das 30 últimas tarefas são mantidas.
	
	![Lista de trabalhos][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Trabalhos agendados e Agendador do Azure

Os trabalhos agendados também podem ser configurados no portal do Agendador do Azure.

1.	Na página Trabalhos Web, clique no link **cronograma** do trabalho para navegar até a página do portal do Agendador do Azure. 
	
	![Link para Agendador do Azure][LinkToScheduler]
	
2. Na página do Agendador, clique no trabalho.
	
	![Trabalho na página do portal do Agendador][SchedulerPortal]
	
3. A página **Ação do Trabalho** é aberta, onde você pode configurar melhor o trabalho. 
	
	![Ação de trabalho PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Exibir o histórico do trabalho

1. Para visualizar o histórico de execução de um trabalho, inclusive trabalhos criados com o SDK de Trabalhos Web, clique no link correspondente na coluna **Logs**. (Se quiser, você poderá usar o ícone da área de transferência para copiar a URL da página do arquivo de log para a área de transferência.)
	
	![Registra o Link][WebJobLogs]
		
2. O clique no link abre a página de detalhes de trabalhos Web da tarefa. Esta página mostra o nome do comando executado, os horários das execuções mais recentes, além do sucesso ou da falha. Em **Execuções de trabalho recentes**, clique em uma hora para ver mais detalhes.
	
	![WebJobDetails][WebJobDetails]
	
3. A página **Detalhes da Execução do Trabalho Web** é exibida. Clique em **Alternar Saída** para ver o texto do conteúdo do log. O log de saída está em formato de texto. 
	
	![Detalhes da execução do trabalho de Web][WebJobRunDetails]
	
4. Para ver o texto de saída em uma janela separada do navegador, clique no link **download**. Para baixar o texto propriamente dito, com o botão direito do mouse no link e use as opções do navegador para salvar o conteúdo do arquivo.
	
	![Baixar a saída de log][DownloadLogOutput]
	
5. O link **WebJobs** na parte superior da página oferece uma maneira prática de obter a uma lista de trabalhos Web no painel de histórico.
	
	![Link para a lista de trabalhos da Web][WebJobsLinkToDashboardList]
	
	![Lista de trabalhos no painel de histórico][WebJobsListInJobsDashboard]
	
	O clique em um desses links leva você até a página Detalhes do Trabalho Web do trabalho selecionado.


## <a name="WHPNotes"></a>Observações
	
- Desde março de 2014, os sites no modo Disponível podem atingir tempo limite depois de 20 minutos se não houver solicitações para o site do scm (implantação) e o portal do site não estiver aberto no Azure. As solicitações para o site real não redefinirão isso.
- O código para um trabalho contínuo precisa ser escrito para ser executado em um loop infinito.
- Trabalhos contínuos só são executados continuamente quando o site estiver funcionando.
- Os modos Básico e Padrão oferecem o recurso Sempre Ativo que, quando habilitado, evita que os sites fiquem ociosos.

## <a name="NextSteps"></a>Próximas etapas
 
Para obter mais informações, consulte [Recursos recomendados para WebJobs do Azure][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png


<!--HONumber=42-->
