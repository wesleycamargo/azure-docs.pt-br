<properties 
	pageTitle="Executar tarefas em segundo plano com Trabalhos Web" 
	description="Saiba como executar tarefas em segundo plano em aplicativos Web do Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Executar tarefas em segundo plano com Trabalhos Web

## Visão geral

Você pode executar programas ou scripts em Trabalhos Web em seu aplicativo Web do [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) de três maneiras: sob demanda, continuamente ou em uma agenda. Não há nenhum custo adicional para usar Trabalhos Web.

Este artigo mostra como implantar Trabalhos Web utilizando o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para obter informações sobre como implantar utilizando o Visual Studio ou um processo de entrega contínua, consulte [Como implantar Trabalhos Web do Azure em Aplicativos Web](websites-dotnet-deploy-webjobs.md).

O SDK de Trabalhos Web do Azure simplifica muitas tarefas de programação de Trabalhos Web. Para obter mais informações, consulte [O que é o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="acceptablefiles"></a>Tipos de arquivo aceitáveis para scripts ou programas

Os seguintes tipos de arquivo são aceitos:

* .cmd, .bat, .exe (usando o cmd do Windows)
* .ps1 (usando o PowerShell)
* .sh (usando bash)
* .php (usando php)
* .py (usando o python)
* .js (usando nó)
* .jar (usando java)

## <a name="CreateOnDemand"></a>Criar um Trabalho Web sob demanda no portal

1. Na folha **Aplicativo Web** do [Portal do Azure](http://portal.azure.com), clique em **Todas as configurações > Trabalhos Web** para mostrar a folha **Trabalhos Web**.
	
	![Lâmina do Trabalho Web](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Clique em **Adicionar**. A caixa de diálogo **Adicionar Trabalho Web** aparece.
	
	![Adicionar lâmina do Trabalho Web](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. Em **Nome**, forneça um nome para o Trabalho Web. O nome deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "\_".
	
4. Na caixa **Como Executar**, escolha **Executado sob Demanda**.
	
3. Na caixa **Carregamento de Arquivo**, clique no ícone de pasta e navegue até o arquivo zip que contém o script. O arquivo zip deve conter o executável (.exe, .cmd, .bat, .sh, .php, .py, .js), bem como os arquivos de suporte necessários para executar o script ou o programa.
	
5. Marque **Criar** para carregar o script em seu aplicativo Web.
	
	O nome especificado para o Trabalho Web é exibido na lista da folha **Trabalhos Web**.
	
6. Para executar o Trabalho Web, clique com o botão direito do mouse em seu nome na lista e clique em **Executar**.
	
	![Executar o Trabalho Web](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Criar um Trabalho Web em execução contínua

1. Para criar um Trabalho Web em execução contínua, siga as mesmas etapas para criar um Trabalho Web que é executado uma vez, mas, na caixa **Como Executar**, escolha **Executar Continuamente**.

2. Para iniciar ou interromper um Trabalho Web contínuo, clique com o botão direito do mouse no Trabalho Web na lista e clique em **Iniciar** ou **Parar**.
	
> [AZURE.NOTE]Se seu aplicativo Web for executado em mais de uma instância, um Trabalho Web em execução contínua será executado em todas as suas instâncias. Trabalhos Web agendados e sob demanda são executados em uma única instância selecionada para o balanceamento de carga pelo Microsoft Azure.
	
> Para que WebJobs contínuos sejam executados de forma confiável e em todas as instâncias, ative a configuração Sempre Ativado* para o aplicativo Web, caso contrário, ele poderá interromper a execução quando o site de host do SCM ficar ocioso por muito tempo.

## <a name="CreateScheduled"></a>Criar um Trabalho Web agendado

O portal de gerenciamento do Azure ainda não tem a capacidade de criar um Trabalho Web agendado, mas até que esse recurso seja adicionado você pode fazê-lo usando o [portal antigo](http://manage.windowsazure.com).

1. No [portal antigo](http://manage.windowsazure.com) vá até a página do Trabalho Web e clique em **Adicionar**.

1. Na caixa **Como Executar**, escolha **Executar em uma agenda**.
	
	![Novo trabalho agendado][NewScheduledJob]
	
2. Escolha a **Região do Agendador** para seu trabalho e, em seguida, clique na seta na parte inferior direita da caixa de diálogo para prosseguir para a próxima tela.

3. Na caixa de diálogo **Criar Trabalho**, escolha o tipo de **Recorrência** desejado: **Trabalho único** ou **Trabalho recorrente**.
	
	![Agendar recorrência][SchdRecurrence]
	
4. Também escolha uma hora **Inicial**: **Agora** ou **Em uma hora específica**.
	
	![Agendar hora inicial][SchdStart]
	
5. Se desejar iniciar em uma hora específica, escolha os valores da hora inicial em **Início em**.
	
	![Agendar o início em uma hora específica][SchdStartOn]
	
6. Se escolher um trabalho recorrente, você tem a opção **Repetir a Cada** para especificar a frequência de ocorrência e a opção **Término em** para especificar uma hora de término.
	
	![Agendar recorrência][SchdRecurEvery]
	
7. Se escolher **Semanas**, você poderá selecionar a caixa **Em uma agenda específica** caixa e especificar os dias da semana em que deseja que o trabalho seja executado.
	
	![Agendar em dias da semana][SchdWeeksOnParticular]
	
8. Se você escolher **Meses** e selecionar a caixa **Em uma Agenda Específica**, poderá definir o trabalho a ser executado em determinados **Dias** numerados do mês.
	
	![Agendar datas específicas no mês][SchdMonthsOnPartDays]
	
9. Se você escolher **Dias da Semana**, poderá selecionar em qual dia ou dias da semana no mês deseja que o trabalho seja executado.
	
	![Agendar dias da semana específicos em um mês][SchdMonthsOnPartWeekDays]
	
10. Finalmente, você também pode usar a opção **Ocorrências** para escolher em qual semana do mês (primeira, segunda, terceira etc.) deseja que o trabalho seja executado em dias da semana especificados.
	
	![Agendar dias da semana específicos em semanas específicas em um mês][SchdMonthsOnPartWeekDaysOccurences]
	
11. Depois que você tiver criado um ou mais trabalhos, os nomes serão exibidos na guia Trabalhos Web com status, tipo de cronograma e outras informações. As informações de histórico dos 30 últimos Trabalhos Web são mantidas.
	
	![Lista de trabalhos][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Trabalhos agendados e o Agendador do Azure

Os trabalhos agendados podem ser configurados mais detalhadamente nas páginas do Agendador do Azure do [portal antigo](http://manage.windowsazure.com).

1.	Na página Trabalhos Web, clique no link **cronograma** do trabalho para navegar até a página do portal do Agendador do Azure. 
	
	![Link para Agendador do Azure][LinkToScheduler]
	
2. Na página do Agendador, clique no trabalho.
	
	![Trabalho na página do portal do Agendador][SchedulerPortal]
	
3. A página **Ação de Trabalho** é aberta, para que você possa configurá-lo.
	
	![Ação de trabalho PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Exibir o histórico do trabalho

1. Para exibir o histórico de execução de um trabalho, incluindo trabalhos criados com o SDK de Trabalhos Web, clique no link correspondente na coluna **Logs** da lâmina Trabalhos Web. (Se quiser, você poderá usar o ícone da área de transferência para copiar a URL da página do arquivo de log para a área de transferência.)
	
	![Link Logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. Clicar no link abre a página de detalhes para o Trabalho Web. Esta página mostra o nome do comando executado, os horários das execuções mais recentes, além do sucesso ou da falha. Em **Execuções de trabalho recentes**, clique em uma hora para ver mais detalhes.
	
	![WebJobDetails][WebJobDetails]
	
3. A página **Detalhes da Execução do Trabalho Web** é exibida. Clique em **Alternar Saída** para ver o texto do conteúdo do log. O log de saída está em formato de texto.
	
	![Detalhes da execução do trabalho Web][WebJobRunDetails]
	
4. Para ver o texto de saída em uma janela separada do navegador, clique no link **download**. Para baixar o texto propriamente dito, com o botão direito do mouse no link e use as opções do navegador para salvar o conteúdo do arquivo.
	
	![Baixar saída do log][DownloadLogOutput]
	
5. O link **Trabalhos Web** na parte superior da página oferece uma maneira prática de obter a uma lista de Trabalhos Web no painel de histórico.
	
	![Vincular à lista de Trabalhos Web][WebJobsLinkToDashboardList]
	
	![Lista de Trabalhos Web no painel de histórico][WebJobsListInJobsDashboard]
	
	O clique em um desses links leva você até a página Detalhes do Trabalho Web do trabalho selecionado.


## <a name="WHPNotes"></a>Observações
	
- Os aplicativos Web no modo Gratuito podem atingir tempo limite depois de 20 minutos se não houver solicitações para o site do scm (implantação) e o portal do aplicativo Web não estiver aberto no Azure. As solicitações para o site real não redefinirão isso.
- O código para um trabalho contínuo precisa ser escrito para ser executado em um loop infinito.
- Trabalhos contínuos só são executados continuamente quando o aplicativo Web estiver funcionando.
- Os modos Básico e Padrão oferecem o recurso Sempre Ativo que, quando habilitado, evita que os aplicativos Web fiquem ociosos.
- Só é possível depurar continuamente os Trabalhos Web em execução. Não há suporte para a depuração Trabalhos Web agendados ou sob demanda.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## <a name="NextSteps"></a>Próximas etapas
 
Para obter mais informações, consulte [Recursos Recomendados para Trabalhos Web do Azure][WebJobsRecommendedResources].

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[PSonWebJobs]: http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]: http://go.microsoft.com/fwlink/?LinkId=390226

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
 

<!---HONumber=Sept15_HO4-->