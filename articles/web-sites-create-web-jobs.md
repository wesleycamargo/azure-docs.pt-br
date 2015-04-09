<properties 
	pageTitle="Executar tarefas em segundo plano com Trabalhos Web" 
	description="Saiba como executar tarefas em segundo plano em aplicativos Web do Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Executar tarefas em segundo plano com Trabalhos Web

## Visão geral

Você pode executar programas ou scripts em Trabalhos Web em seu aplicativo Web do [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) de três maneiras: sob demanda, continuamente ou em uma agenda. Não há nenhum custo adicional para usar Trabalhos Web.

Este artigo mostra como implantar Trabalhos Web utilizando o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para obter informações sobre como implantar utilizando o Visual Studio ou um processo de entrega contínua, consulte [Como implantar Trabalhos Web do Azure para Aplicativos Web](websites-dotnet-deploy-webjobs.md).

O SDK de Trabalhos Web do Azure simplifica muitas tarefas de programação de Trabalhos Web. Para obter mais informações, consulte [O que é o SDK dos Trabalhos Web](websites-dotnet-webjobs-sdk.md).

## <a name="acceptablefiles"></a>Tipos de arquivo aceitáveis para scripts ou programas

Os seguintes tipos de arquivo são aceitos:

* .cmd, .bat, .exe (usando o cmd do Windows)
* .ps1 (usando o PowerShell)
* .sh (usando bash)
* .php (usando php)
* .py (usando o python)
* .js (usando nó)

## <a name="CreateOnDemand"></a>Criar um Trabalho Web sob demanda no portal

1. Na lâmina **Aplicativo Web** do [Portal do Azure](http://portal.azure.com), clique em **Todas as configurações > Trabalhos Web** para mostrar a lâmina **Trabalhos Web**.
	
	![WebJob blade](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Clique em **Adicionar**. A caixa de diálogo **Adicionar Trabalho Web** aparece.
	
	![Add WebJob blade](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. Em **Nome**, forneça um nome para o Trabalho Web. O nome deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "_".
	
4. Na caixa **Como Executar**, escolha **Executado sob Demanda**.
	
3. Na caixa **Carregar Arquivo**, clique no ícone de pasta e navegue até o arquivo zip que contém o script. O arquivo zip deve conter o executável (.exe, .cmd, .bat, .sh, .php, .py, .js), bem como os arquivos de suporte necessários para executar o script ou o programa.
	
5. Verifique **Criar** para carregar o script em seu aplicativo Web. 
	
	O nome especificado para o Trabalho Web é exibido na lista presente na lâmina **Trabalhos Web**.
	
6. Para executar o Trabalho Web, clique em seu nome na lista e clique em **Executar**.
	
	![Run WebJob](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Criar um Trabalho Web em execução contínua

1. Para criar um Trabalho Web em execução contínua, siga as mesmas etapas para criar Trabalho Web que é executado uma vez, mas na caixa **Como Executar**, escolha **Executar continuamente**.

2. Para iniciar ou interromper um Trabalho Web contínuo, clique com o botão direito do mouse no Trabalho Web na lista e clique em **Iniciar** ou **Parar**.
	
> [AZURE.NOTE] Se seu aplicativo Web é executado em mais de uma instância, um Trabalho Web em execução contínua será executado em todas as suas instâncias. Trabalhos Web agendados e sob demanda são executados em uma única instância selecionada para o balanceamento de carga pelo Microsoft Azure.
	
> [AZURE.NOTE] Para Trabalhos Web contínuos, é recomendável que você habilite **Sempre Ativado** para seu aplicativo Web. O recurso Sempre Ativado, disponível nos modos Básico e Padrão, impede que os aplicativos Web sejam descarregados, mesmo que tenham permanecido ociosos por algum tempo. Se seu aplicativo Web estiver sempre carregado, seu Trabalho Web em execução contínua poderá ser executado de maneira mais confiável. 

## <a name="CreateScheduled"></a>Criar um Trabalho Web agendado

O Portal de Gerenciamento do Azure ainda não tem a capacidade de criar um Trabalho Web agendado, mas até que esse recurso seja adicionado você pode fazê-lo usando o [portal antigo](http://manage.windowsazure.com).

1. No [portal antigo](http://manage.windowsazure.com) vá até a página do Trabalho Web e clique em **Adicionar**.

1. Na caixa **Como Executar**, escolha **Executar em uma agenda**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Escolha a **Região do Agendador** para seu trabalho e, em seguida, clique na seta na parte inferior direita da caixa de diálogo para prosseguir para a próxima tela.

3. Na caixa de diálogo **Criar Trabalho**, escolha o tipo de **Recorrência** desejado: **Trabalho avulso** ou **Trabalho recorrente**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. Escolha também uma hora **Inicial**: **Agora** ou **Em uma hora específica**.
	
	![Schedule Start Time][SchdStart]
	
5. Se desejar iniciar em uma hora específica, escolha os valores da hora inicial em **Início em**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Se escolher um trabalho recorrente, você tem a opção **Repetir a Cada** para especificar a frequência de ocorrência e a opção **Término em** para especificar uma hora de término.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. Se escolher **Semanas**, você poderá selecionar a caixa **Em uma agenda específica** caixa e especificar os dias da semana em que deseja que o trabalho seja executado.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. Se você escolher **Meses** e selecionar a caixa **Em uma Agenda Específica**, poderá definir o trabalho a ser executado em determinados **Dias** numerados do mês. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. Se você escolher **Dias da Semana**, poderá selecionar em qual dia ou dias da semana no mês deseja que o trabalho seja executado.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Finalmente, você também pode usar a opção **Ocorrências** para escolher em qual semana do mês (primeira, segunda, terceira etc.) deseja que o trabalho seja executado em dias da semana especificados.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Depois que você tiver criado um ou mais trabalhos, os nomes serão exibidos na guia Trabalhos Web com status, tipo de cronograma e outras informações. As informações de histórico dos 30 últimos Trabalhos Web são mantidas.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Trabalhos agendados e Agendador do Azure

Trabalhos agendados podem ser configurados mais detalhadamente nas páginas do Agendador do Azure do [portal antigo](http://manage.windowsazure.com).

1.	Na página Trabalhos Web, clique no link **cronograma** do trabalho para navegar até a página do portal do Agendador do Azure. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. Na página do Agendador, clique no trabalho.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. A página **Ação do Trabalho** é aberta, em que você pode configurar melhor o trabalho. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Exibir o histórico de trabalho

1. Para exibir o histórico de execução de um trabalho, inclusive trabalhos criados com o SDK de Trabalhos Web, clique no link correspondente na coluna **Logs** da lâmina Trabalhos Web. (Se quiser, você poderá usar o ícone da área de transferência para copiar a URL da página do arquivo de log para a área de transferência.)
	
	![Link de logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. Clicar no link abre a página de detalhes para o Trabalho Web. Esta página mostra o nome do comando executado, os horários das execuções mais recentes, além do sucesso ou da falha. Em **Execuções de trabalho recentes**, clique em uma hora para ver mais detalhes.
	
	![WebJobDetails][WebJobDetails]
	
3. A página **Detalhes da Execução do Trabalho Web** é exibida. Clique em **Alternar Saída** para ver o texto do conteúdo do log. O log de saída está em formato de texto. 
	
	![Web job run details][WebJobRunDetails]
	
4. Para ver o texto de saída em uma janela separada do navegador, clique no link **download**. Para baixar o texto propriamente dito, com o botão direito do mouse no link e use as opções do navegador para salvar o conteúdo do arquivo.
	
	![Download log output][DownloadLogOutput]
	
5. O link **Trabalhos Web** na parte superior da página oferece uma maneira prática de obter a uma lista de Trabalhos Web no painel de histórico.
	
	![Link to WebJobs list][WebJobsLinkToDashboardList]
	
	![List of WebJobs in history dashboard][WebJobsListInJobsDashboard]
	
	O clique em um desses links leva você até a página Detalhes do Trabalho Web do trabalho selecionado.


## <a name="WHPNotes"></a>Observações
	
- Desde março de 2014, os aplicativos Web no modo Gratuito podem atingir tempo limite depois de 20 minutos se não houver solicitações para o site do scm (implantação) e o portal do aplicativo Web não estiver aberto no Azure. As solicitações para o site real não redefinirão isso.
- O código para um trabalho contínuo precisa ser escrito para ser executado em um loop infinito.
- Trabalhos contínuos só são executados continuamente quando o aplicativo Web estiver funcionando.
- Os modos Básico e Padrão oferecem o recurso Sempre Ativo que, quando habilitado, evita que os aplicativos Web fiquem ociosos.

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## <a name="NextSteps"></a>Próximas etapas
 
Para obter mais informações, consulte [Recursos recomendados de Trabalhos Web do Azure][WebJobsRecommendedResources].

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

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

<!--HONumber=49-->