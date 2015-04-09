<properties 
	pageTitle="Restaurar um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Saiba como restaurar seu aplicativo Web por meio de um backup." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Restaurar um aplicativo Web no Serviço de Aplicativo do Azure

Este artigo mostra como restaurar um aplicativo Web do qual você fez backup anteriormente usando o recurso de dos Backup de [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714). Para obter mais informações, consulte [Backups de Aplicativos Web do Serviço de Aplicativo](web-sites-backup.md). 

O recurso de restauração de aplicativos Web permite restaurar seu aplicativo Web sob demanda para um estado anterior, ou então criar um novo aplicativo Web com base em um dos backups do seu aplicativo Web original. A criação de um novo aplicativo Web que é executado em paralelo à versão mais recente pode ser útil para testes de A/B.

O recurso de Restauração de Aplicativos Web, disponível na lâmina **Backups** do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), está disponível somente nos modos Padrão e Premium. Para obter informações sobre como dimensionar seu aplicativo usando modo Padrão ou Premium, consulte [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md). 
Observe que o modo Premium permite a realização de um número maior de backups diários do que o modo Padrão.

<a name="PreviousBackup"></a>
## Para restaurar um aplicativo Web por meio de um backup feito anteriormente

1. Na lâmina **Configurações** de seu aplicativo Web no portal do Azure, clique na opção **Backups** para exibir a lâmina **Backups**. Role por essa lâmina e selecione um dos itens de backup com base no **TEMPO DE BACKUP** e **STATUS**, por meio da lista de backup.
	
	![Choose backup source][ChooseBackupSource]
	
2. Selecione **Restaurar agora** na parte superior da lâmina **Backups**. 

	![Choose restore now][ChooseRestoreNow]]

3. Na lâmina **Restauração**, para restaurar o aplicativo Web existente, verifique todos os detalhes exibidos e, em seguida, clique em **OK**. 
	
Você também pode restaurar seu aplicativo Web para um novo aplicativo Web, selecionando a parte **APLICATIVO WEB** da lâmina **Restaurar** e selecionando a parte **Criar um novo aplicativo Web**.
	
<a name="StorageAccount"></a>
## Baixar ou excluir um backup de uma conta de armazenamento
	
1. Na página principal da lâmina **Procurar** do portal do Azure, selecione **Contas de Armazenamento**.
	
	Uma lista de suas contas de armazenamento existentes será exibida. 
	
2. Selecione a conta de armazenamento que contém o backup que você deseja baixar ou excluir.
	
	A lâmina **ARMAZENAMENTO** será exibida.

3. Selecione a parte **Contêineres** na lâmina **ARMAZENAMENTO** para exibir a lâmina **Contêineres**.
	
	Será exibida uma lista de contêineres. Essa lista também mostrará a URL e a data de quando esse contêiner foi modificado pela última vez.
	
	![View Containers][ViewContainers]

4. Na lista, selecione o contêiner e exiba a lâmina que mostra uma lista de nomes de arquivo, juntamente com o tamanho de cada arquivo.
	
5. Ao selecionar um arquivo, você pode optar por **Baixar** ou **Excluir** o arquivo. Observe que há dois tipos primários de arquivos, arquivos .zip e arquivos .xml. 

<a name="OperationLogs"></a>
## Exibir os logs de auditoria
	
1. Para ver detalhes sobre o êxito ou falha da operação de restauração de aplicativo Web, selecione a parte **Log de auditoria** da lâmina **Procurar** principal. 
	
	A lâmina **Log de áudio** exibe todas as suas operações, juntamente com detalhes de nível, status, recursos e tempo.
	
2. Role a lâmina para localizar as operações relacionadas ao seu aplicativo Web.
3. Para exibir detalhes adicionais sobre uma operação, selecione essa operação na lista.
	
A lâmina de detalhes exibirá as informações disponíveis relacionadas à operação.
	
>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
	
## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

<!--HONumber=49-->