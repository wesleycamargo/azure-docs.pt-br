<properties linkid="web-sites-create-web-jobs" urlDisplayName="Como criar trabalhos Web no Microsoft Azure" pageTitle="Como criar trabalhos Web em sites do Microsoft Azure" metaKeywords="Sites do Microsoft Azure, trabalhos Web" description="Aprenda a criar trabalhos Web em sites do Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="" title="Como criar trabalhos Web em sites do Microsoft Azure" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

#Como usar o recurso Trabalhos Web em sites do Microsoft Azure#

Os sites do Microsoft Azure permitem executar programas ou scripts no site de uma destas três maneiras: sob demanda, continuamente ou segundo um cronograma. Não há custo adicional em usar Trabalhos Web do Microsoft Azure, a menos que você queira habilitar o recurso Sempre Visível descrito posteriormente neste artigo.


## Sumário ##
- [Tipos de arquivo aceitáveis para scripts](#acceptablefiles)
- [Criar uma tarefa sob demanda](#CreateOnDemand)
- [Criar uma tarefa em execução continuamente](#CreateContinuous)
- [Criar uma tarefa agendada](#CreateScheduled)
	- [Trabalhos agendados e Agendador do Azure](#Scheduler)
- [Exibir o histórico do trabalho](#ViewJobHistory)
- [Observações](#WHPNotes)
- [Próximas etapas](#NextSteps)
	- [Fazer mais com o SDK dos Trabalhos Web do Microsoft Azure](#WebJobsSDK)
	- [Métodos alternativos de implantação](#AlternateDeployments)
	- [Recursos adicionais](#AdditionalResources)

<a name="acceptablefiles"></a>
##Tipos de arquivo aceitáveis para scripts##
Os seguintes tipos de arquivo são aceitos como scripts executáveis:

.cmd, .bat, .exe (usando o cmd do Windows)

.ps1 (usando o PowerShell)

.sh (usando bash)

.php (usando php)

.py (usando o python)

.js (usando nó)

<a name="CreateOnDemand"></a>
##Criar uma tarefa sob demanda##

1. Na barra de comandos da página **Trabalhos Web**, clique em **Adicionar**. A caixa de diálogo **Novo Trabalho** é exibida.
	
	![Tarefa sob demanda][OnDemandWebJob]
	
2. Em **Nome**, dê um nome para a tarefa. O nome deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "_".
	
3. Na caixa **Conteúdo (Arquivos ZIP - Máximo de 100 MB)**, navegue até o arquivo zip que contém o script. O arquivo zip deve conter o executável (.exe, .cmd, .bat, .sh, .php, .py, .js), bem como os arquivos de suporte necessários para executar o script ou o programa.
	
4. Na caixa **Como Executar**, escolha **Executar sob Demanda**.
	
5. Marque a caixa de seleção no canto inferior direito da caixa de diálogo para carregar o script no site. O nome especificado para a tarefa aparece na lista:
	
	![Lista de tarefas][WebJobsList]
	
6. Para executar o script, selecione seu nome na lista e clique em **Executar Uma Vez** na barra de comandos na parte inferior da página do portal.
	
	![Executar uma vez][RunOnce]

<a name="CreateContinuous"></a>
##Criar uma tarefa em execução continuamente##

1. Para criar uma tarefa em execução continuamente, siga as mesmas etapas para criar uma tarefa executada uma vez, mas na caixa **Como Executar**, escolha **Executar continuamente**.
	
	![Nova tarefa contínua][NewContinuousJob]
	
2. Para iniciar ou parar uma tarefa em execução continuamente, selecione a tarefa na lista e clique em **Iniciar** ou **Parar** na barra de comandos.

> [WACOM.NOTE] Se seu site for executado em mais de uma instância, uma tarefa em execução contínua será executada em todas as suas instâncias. Tarefas sob demanda e agendadas são executadas em uma única instância selecionada para o balanceamento de carga pelo Microsoft Azure.

> [WACOM.NOTE]
> Para tarefas contínuas, é recomendável habilitar **Sempre Ativo** na página Configurar de seu site. O recurso Sempre Visível, disponível nos modos Básico e Padrão, impede que os sites sejam descarregados, mesmo que tenham permanecido ociosos por algum tempo. Se seu site estiver sempre carregado, a tarefa em execução contínua poderá ser executada de maneira mais confiável. 

<a name="CreateScheduled"></a>
##Criar uma tarefa agendada##
1. Para criar uma tarefa agendada, siga as mesmas etapas anteriores, mas, na caixa **Como Executar**, escolha **Executar segundo um cronograma**.
	
	![Novo trabalho agendado][NewScheduledJob]
	
2. Escolha a **Região do Agendador** para o trabalho e clique na seta na parte inferior direita da caixa de diálogo para passar à próxima tela.

3. Na caixa de diálogo **Criar Trabalho**, escolha o tipo de **Recorrência** desejado: **Trabalho avulso** ou **Trabalho recorrente**.
	
	![Agendar recorrência][SchdRecurrence]
	
4. Também escolha uma hora **Inicial**: **Agora** ou **Em uma hora específica**.
	
	![Agendar hora inicial][SchdStart]
	
5. Se você quiser iniciar em uma hora específica, escolha os valores de hora inicial em **Começando em**.
	
	![Agendar o início em uma hora específica][SchdStartOn]
	
6. Se escolher um trabalho recorrente, você terá a opção **Repetir a Cada** para especificar a frequência da ocorrência e a opção **Terminando em** para especificar uma hora de término.
	
	![Agendar recorrência][SchdRecurEvery]
	
7. Se escolher **Semanas**, você poderá marcar a caixa **Em um Cronograma Específico** e especificar os dias da semana em que deseja que o trabalho seja executado.
	
	![Agendar em dias da semana][SchdWeeksOnParticular]
	
8. Se escolher **Meses** e marcar a caixa **Em um Cronograma Específico**, você poderá definir o trabalho a ser executado em determinados **Dias** numerados do mês. 
	
	![Agendar datas específicas no mês][SchdMonthsOnPartDays]
	
9. Se escolher **Dias da Semana**, você poderá selecionar em qual dia ou dias da semana no mês deseja que o trabalho seja executado.
	
	![Agendar dias da semana específicos em um mês][SchdMonthsOnPartWeekDays]
	
10. Por fim, você também pode usar a opção **Ocorrências** para escolher qual semana do mês (primeira, segunda, terceira etc.) deseja que o trabalho seja executado em dias da semana especificados.
	
	![Agendar dias da semana específicos em semanas específicas em um mês][SchdMonthsOnPartWeekDaysOccurences]
	
11. Depois que você tiver criado um ou mais trabalhos, seus nomes serão exibidos na guia Trabalhos Web com seu status, tipo de cronograma e outras informações. As informações históricas das 30 últimas tarefas são mantidas.
	
	![Lista de trabalhos][WebJobsListWithSeveralJobs]
	
<a name="Scheduler"></a>
###Trabalhos agendados e Agendador do Azure
Os trabalhos agendados também podem ser configurados no portal do Agendador do Azure.

1.	Na página Trabalhos Web, clique no link **cronograma** do trabalho para navegar até a página do portal do Agendador do Azure. 
	
	![Link para Agendador do Azure][LinkToScheduler]
	
2. Na página do Agendador, clique no trabalho.
	
	![Trabalho na página do portal do Agendador][SchedulerPortal]
	
3. A página **Ação do Trabalho** é aberta, onde você pode configurar melhor o trabalho. 
	
	![Ação de trabalho PageInScheduler][JobActionPageInScheduler]
	

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>
##Exibir o histórico do trabalho##
1. Para exibir o histórico de execução de um trabalho, inclusive trabalhos criados com o SDK de Trabalhos Web, clique no link correspondente na coluna **Logs**. (Se quiser, você poderá usar o ícone da área de transferência para copiar a URL da página do arquivo de log para a área de transferência.)
	
	![Link de logs][WebJobLogs]
		
2. O clique no link abre a página de detalhes de trabalhos Web da tarefa. Esta página mostra o nome do comando executado, os horários das execuções mais recentes, além do sucesso ou da falha. Em **Execuções de trabalho recentes**, clique em uma hora para ver mais detalhes.
	
	![WebJobDetails][WebJobDetails]
	
3. A página **Detalhes da Execução do Trabalho Web** é exibida. Clique em **Alternar Saída** para ver o texto do conteúdo do log. O log de saída está em formato de texto. 
	
	![Detalhes da execução do trabalho Web][WebJobRunDetails]
	
4. Para ver o texto de saída em uma janela separada do navegador, clique no link **download**. Para baixar o texto propriamente dito, com o botão direito do mouse no link e use as opções do navegador para salvar o conteúdo do arquivo.
	
	![Baixar saída do log][DownloadLogOutput]
	
5. O link **WebJobs** na parte superior da página oferece uma maneira prática de obter a uma lista de trabalhos Web no painel de histórico.
	
	![Vincular à lista de trabalhos Web][WebJobsLinkToDashboardList]
	
	![Lista de trabalhos no painel de histórico][WebJobsListInJobsDashboard]
	
	O clique em um desses links leva você até a página Detalhes do Trabalho Web do trabalho selecionado.

<a name="WHPNotes"></a>
##Observações
	
- Desde março de 2014, os sites no modo Disponível podem atingir tempo limite depois de 20 minutos se não houver solicitações para o site do scm (implantação) e o portal do site não estiver aberto no Azure. As solicitações para o site real não redefinirão isso.

- O código para um trabalho contínuo precisa ser escrito para ser executado em um loop infinito.

- Trabalhos contínuos só são executados continuamente quando o site está funcionando.

- Os modos Básico e Padrão oferecem o recurso Sempre Visível que, quando habilitado, evita que os sites fiquem ociosos.

<a name="NextSteps"></a>
##Próximas etapas##
 
<a name="WebJobsSDK"></a>
###Fazer mais com o SDK dos Trabalhos Web do Microsoft Azure###
O SDK dos Trabalhos Web do Microsoft Azure simplifica a tarefa de adicionar o processamento em segundo plano aos sites do Microsoft Azure. O SDK integra o Armazenamento do Microsoft Azure, disparando uma função no programa quando itens são adicionados a filas, blobs ou tabelas. O painel, agora integrado ao portal do Azure, oferece monitoramento e diagnóstico avançados para os programas que você escreve usando o SDK. Os recursos de monitoramento e diagnóstico fazem parte do SDK e não requerem que você adicione nenhum código especial em seu programa.
 
Para obter mais informações, consulte o tutorial [Introdução ao SDK dos Trabalhos Web do Microsoft Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). O tutorial fornece uma visão geral dos recursos do SDK do Trabalhos da Web e explica como criar e executar um simples processo Hello World em segundo plano. 

Para ver uma explicação passo a passo de um aplicativo de linha de comando de exemplo criado com o SDK dos Trabalhos Web do Microsoft Azure, consulte [Introdução aos Trabalhos Web do Windows Azure][HanselIntro]

<a name="AlternateDeployments"></a>
###Métodos alternativos de implantação###
Se não quiser usar a página Trabalhos da Web do portal para carregar seus scripts, você poderá usar FTP, git ou implantação da Web. Para obter mais informações, consulte [Como implantar os Trabalhos da Web do Windows Azure][AmitDeploy] e [Implantando um aplicativo de console do .NET em Git no Azure usando Trabalhos da Web][AmitConsole].

<a name="AdditionalResources"></a>
###Recursos adicionais###
- Para obter uma lista anotada de links sobre o recurso Trabalhos Web, consulte [Usando o recurso Trabalhos Web de Sites do Windows Azure][RickWebJobsCurah]. 
	
- Vídeos relacionados a Trabalhos Web:

	[Trabalhos Web do Azure 101 - Trabalhos Web básicos com Jaime Espinosa](http://www.windowsazure.com/pt-br/documentation/videos/azure-webjobs-basics/)
	
	[Trabalhos Web do Azure 102 - Trabalhos Web agendados e o Painel dos Trabalhos Web com Jaime Espinosa](http://www.windowsazure.com/pt-br/documentation/videos/azure-webjobs-schedule-and-dashboard/)
	
	[Agendador do Azure 101 - Kevin Lam explica como programar coisas](http://www.windowsazure.com/pt-br/documentation/videos/azure-scheduler-how-to/)


<!-- LINKS -->
[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx

[HanselIntro]:http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx

[AmitDeploy]:http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs

[AmitConsole]:http://blog.amitapple.com/post/73574681678/git-deploy-console-app

[RickWebJobsCurah]:http://go.microsoft.com/fwlink/?LinkId=390226

<!-- IMAGES -->
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


