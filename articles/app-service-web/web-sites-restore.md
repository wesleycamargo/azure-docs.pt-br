<properties 
	pageTitle="Restaurar um aplicativo no Serviço de Aplicativo do Azure" 
	description="Saiba como restaurar seu aplicativo de um backup." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="cephalin"/>

# Restaurar um aplicativo no Serviço de Aplicativo do Azure

Este artigo mostra como restaurar um aplicativo do Serviço de Aplicativo do qual você fez backup anteriormente usando o recurso de Backup do [Serviço de Aplicativo](../app-service/app-service-value-prop-what-is.md). Para obter mais informações, veja [Backups do Serviço de Aplicativo](web-sites-backup.md).

O recurso de Restauração do Serviço de Aplicativo permite restaurar seu aplicativo com seus bancos de dados vinculados (Banco de Dados SQL ou MySQL) sob demanda para um estado anterior ou criar um novo aplicativo com base em um backup de seu aplicativo original. A criação de um novo aplicativo que é executado em paralelo à versão mais recente pode ser útil para testes A/B.

O recurso de Restauração do Serviço de Aplicativo, disponível na folha **Backups** do [Portal do Azure](https://portal.azure.com), está disponível somente nos tipos de preço Standard e Premium. Para obter informações sobre como colocar em escala seu aplicativo usando a camada Standard ou Premium, veja [Escalar um aplicativo no Serviço de Aplicativo do Azure](web-sites-scale.md). Observe que a camada Premium permite a realização de um número maior de backups diários do que a camada Premium.

<a name="PreviousBackup"></a>
## Para restaurar um aplicativo de um backup feito anteriormente

1. Na folha **Configurações** de seu aplicativo no portal do Azure, clique em **Backups** para exibir a folha **Backups**. Em seguida, clique em **Restaurar Agora** na barra de comandos. 
	
	![Escolha restaurar agora][ChooseRestoreNow]

3. Na folha **Restauração**, primeiro selecione a fonte do backup.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	A opção **Backup do aplicativo** mostra todos os backups que são criados diretamente pelo próprio aplicativo, pois esses são os únicos que são reconhecidos pelos aplicativos. Você pode facilmente selecionar um. A opção **Armazenamento** permite que você selecione o arquivo ZIP do backup real da conta de armazenamento e do contêiner que está configurado na folha **Backups**. Se houver arquivos de backup de quaisquer outros aplicativos no contêiner, você pode selecioná-los para restaurar também.

4. Em seguida, especifique o destino para a restauração de aplicativo em **Destino de restauração**.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING] Se você escolher **Substituir**, todos os dados relacionados a seu aplicativo existente serão apagados. Antes de clicar em **OK**, certifique-se de que isso é exatamente o que você deseja fazer.
	
	Você pode selecionar um **Aplicativo Existente** para restaurar o backup do aplicativo para outro aplicativo no mesmo grupo de recursos. Antes de usar essa opção, você já precisa ter criado outro aplicativo em seu grupo de recursos com o espelhamento da configuração de banco de dados para aquele definido no backup do aplicativo.
	
5. Clique em **OK**.

<a name="StorageAccount"></a>
## Baixar ou excluir um backup de uma conta de armazenamento
	
1. Na folha principal **Procurar** do Portal do Azure, selecione **Contas de Armazenamento**.
	
	Uma lista de suas contas de armazenamento existentes será exibida.
	
2. Selecione a conta de armazenamento que contém o backup que você deseja baixar ou excluir.
	
	A folha **ARMAZENAMENTO** será exibida.

3. Selecione a parte **Contêineres** na folha **ARMAZENAMENTO** para exibir a folha **Contêineres**.
	
	Será exibida uma lista de contêineres. Essa lista também mostrará a URL e a data de quando esse contêiner foi modificado pela última vez.
	
	![Exibir contêineres][ViewContainers]

4. Na lista, selecione o contêiner e exiba a folha que mostra uma lista de nomes de arquivo, juntamente com o tamanho de cada arquivo.
	
5. Ao selecionar um arquivo, você pode optar por **Baixar** ou **Excluir** o arquivo. Observe que há dois tipos primários de arquivos, arquivos .zip e arquivos .xml.

<a name="OperationLogs"></a>
## Exibir os logs de auditoria
	
1. Para ver detalhes sobre o êxito ou a falha da operação de restauração do aplicativo, selecione a parte **Log de auditoria** da folha principal **Procurar**. 
	
	A folha **Log de auditoria** exibe todas as suas operações, juntamente com detalhes de nível, de status, de recursos e de tempo.
	
2. Role a folha para localizar as operações relacionadas a seu aplicativo.
3. Para exibir detalhes adicionais sobre uma operação, selecione essa operação na lista.
	
A folha de detalhes exibirá as informações disponíveis relacionadas à operação.
	
>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas

Você também pode usar a API REST para fazer backup e restaurar aplicativos do Serviço de Aplicativo (consulte [Usar REST para fazer backup e restaurar aplicativos do Serviço de Aplicativo](websites-csm-backup.md)).

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
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
 

<!---HONumber=AcomDC_0504_2016-->