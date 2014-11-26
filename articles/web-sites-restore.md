<properties linkid="web-sites-restore" urlDisplayName="Restore a Microsoft Azure website" pageTitle="Restore a Microsoft Azure website" metaKeywords="Azure Websites, Restore, restoring" description="Learn how to restore your Azure websites from backup." metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure website" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Restaurar um Site do Microsoft Azure

Este artigo mostra como restaurar um site do qual você fez backup anteriormente usando o recurso Backup de Sites do Azure. Para obter mais informações, consulte [Backups de Sites do Microsoft Azure][Backups de Sites do Microsoft Azure].

O recurso de Restauração de Sites do Azure permite restaurar seu site sob demanda para um estado anterior ou criar um novo site baseado em um dos backups do seu site original. A criação de um novo site que é executado em paralelo para a versão mais recente pode ser útil para teste de A/B.

O recurso de restauração, disponível na guia Backups no portal de Sites do Azure, está disponível apenas no modo Standard.

## Neste artigo

-   [Para restaurar um Site do Azure a partir de um backup feito anteriormente][Para restaurar um Site do Azure a partir de um backup feito anteriormente]
-   [Para restaurar um Site do Azure diretamente de uma conta de armazenamento][Para restaurar um Site do Azure diretamente de uma conta de armazenamento]
-   [Escolher as configurações de restauração do Site e iniciar a operação de restauração][Escolher as configurações de restauração do Site e iniciar a operação de restauração]
-   [Exibir os logs de operação][Exibir os logs de operação]

<a name="PreviousBackup"></a>

## Para restaurar um Site do Azure a partir de um backup feito anteriormente

1.  Na guia **Backups**, clique em **Restaurar agora** na barra de comandos na parte inferior da página do portal. A caixa de diálogo **Restaurar agora** é exibida.

    ![Selecione a origem do backup][Selecione a origem do backup]

2.  Em **Escolha a origem do backup**, selecione **Backup Anterior deste Site**.
3.  Selecione a data do backup que deseja restaurar e, em seguida, clique na seta para a direita para continuar.
4.  Siga as etapas da seção [Escolher suas configurações de restauração de site][Escolher as configurações de restauração do Site e iniciar a operação de restauração] mais adiante neste artigo.

<a name="StorageAccount"></a>

## Para restaurar um Site do Azure diretamente de uma conta de armazenamento

1.  Na guia **Backups**, clique em **Restaurar agora** na barra de comandos na parte inferior da página do portal. A caixa de diálogo **Restaurar agora** é exibida.

    ![Selecione a origem do backup][Selecione a origem do backup]

2.  Em **Escolher a origem do backup**, selecione **Arquivo da Conta de Armazenamento**. Aqui você pode especificar diretamente a URL do arquivo da conta de armazenamento ou clicar no ícone de pasta para navegar para o armazenamento de blob e especificar o arquivo de backup. Este exemplo escolhe o ícone de pasta.

    ![Arquivo da Conta de Armazenamento][Arquivo da Conta de Armazenamento]

3.  Clique no ícone de pasta para abrir a caixa de diálogo **Procurar Armazenamento em Nuvem**.

    ![Procurar Armazenamento em Nuvem][Procurar Armazenamento em Nuvem]

4.  Expanda o nome da conta de armazenamento que você deseja usar e, em seguida, selecione **websitebackups**, que contém seus backups.
5.  Selecione o arquivo zip que contém o backup que deseja restaurar e, em seguida, clique em **Abrir**.
6.  O arquivo da conta de armazenamento foi selecionado e é mostrado na caixa Conta de Armazenamento. Clique na seta direita para continuar.

    ![Arquivo da Conta de Armazenamento selecionado][Arquivo da Conta de Armazenamento selecionado]

7.  Continue com a seção a seguir, [Escolher as configurações de restauração do site e iniciar a operação de restauração][Escolher as configurações de restauração do Site e iniciar a operação de restauração].

<a name="RestoreSettings"></a>

## Escolher as configurações de restauração do Site e iniciar a operação de restauração

1.  Em **Selecionar as configurações de restauração do seu site**, **Restaurar para**, selecionar **Site atual** ou **Nova instância do site**.

    ![Selecione suas configurações de restauração de site][Selecione suas configurações de restauração de site]

    Se você selecionar **Site atual**, o site existente será substituído pelo backup selecionado (restauração destrutiva). Todas as alterações feitas no site desde o momento do backup escolhido serão removidas permanentemente, e a operação de restauração não poderá ser desfeita. Durante a operação de restauração, seu site atual estará temporariamente indisponível e você será avisado sobre isso.

    Se você selecionar **Nova instância do site**, um novo site será criado na mesma região com o nome especificado. (Por padrão, o novo nome é **restored-*oldWebSiteName***.)

    O site que você restaurar irá conter o mesmo conteúdo e configuração que foram feitos no portal para o site original. Também incluirá todos os bancos de dados que você optar por incluir na próxima etapa.

2.  Se você desejar restaurar um banco de dados juntamente com o site, em **Bancos de Dados Incluídos**, selecione o nome do servidor de banco de dados para o qual você deseja restaurar o banco de dados usando o menu suspenso sob **Restaurar para**. Você também pode optar por criar um novo servidor de banco de dados para o qual restaurar ou escolher o padrão **Não Restaurar** para não restaurar o banco de dados.

    Depois de escolher o nome do servidor, especifique o nome do banco de dados de destino para a restauração na caixa **Nome do Banco de Dados**.

    Se a restauração incluir um ou mais bancos de dados, você pode selecionar **Ajustar cadeias de conexão automaticamente** para atualizar suas cadeias de conexão armazenadas no backup para apontarem para o novo banco de dados ou servidor de banco de dados, conforme apropriado. Você deve verificar se todas as funcionalidades relacionadas aos bancos de dados funcionam conforme o esperado após a conclusão da restauração.

    ![Escolher um host de servidor de banco de dados][Escolher um host de servidor de banco de dados]

    > [WACOM.NOTE] Você não pode restaurar um banco de dados SQL com o mesmo nome para o mesmo SQL Server. Você deve escolher outro nome de banco de dados ou outro host do SQL Server no qual restaurar o banco de dados.

    > [WACOM.NOTE] Você pode restaurar um banco de dados MySQL com o mesmo nome no mesmo servidor, mas lembre-se de que isso apagará o conteúdo existente armazenado no banco de dados MySQL.

3.  Se você optar por restaurar um banco de dados existente, precisará fornecer um nome de usuário e uma senha. Se você optar por restaurar para um novo banco de dados, você precisará fornecer um novo nome de banco de dados:

    ![Restaurar para um novo banco de dados SQL][Restaurar para um novo banco de dados SQL]

    Clique na seta direita para continuar.

4.  Se você optar por criar um novo banco de dados, precisará fornecer as credenciais e outras informações de configuração inicial do banco de dados na próxima caixa de diálogo. O exemplo mostra um novo banco de dados SQL. (As opções para um novo banco de dados MySQL são um pouco diferentes).

    ![Configurações do novo Banco de Dados SQL][Configurações do novo Banco de Dados SQL]

5.  Clique na marca de seleção para iniciar o processo de restauração. Quando concluir, a nova instância do site (se essa for a opção de restauração que você escolheu) ficará visível na lista de sites no portal.

    ![Site Contoso restaurado][Site Contoso restaurado]

<a name="OperationLogs"></a>

## Exibir os logs de operação

1.  Para ver detalhes sobre o sucesso ou a falha da operação de restauração de site, vá para a guia Painel do site. Na seção **Visão Rápida**, em **Serviços de Gerenciamento**, clique em **Logs de Operação**.

    ![Painel - Link dos Logs de Operação][Painel - Link dos Logs de Operação]

2.  Você é levado para a página **Logs de Operação** do portal de Serviços de Gerenciamento, onde pode ver o log da operação de restauração na lista de logs de operação:

    ![Página Logs de Operação dos Serviços de Gerenciamento][Página Logs de Operação dos Serviços de Gerenciamento]

3.  Para exibir detalhes sobre a operação, selecione a operação na lista e, em seguida, clique no botão **Detalhes** na barra de comandos.

    ![Botão Detalhes][Botão Detalhes]

    Ao fazer isso, a janela **Detalhes das Operações** é aberta e mostra o conteúdo que pode ser copiado do arquivo de log:

    ![Detalhes da Operação][Detalhes da Operação]

<!-- IMAGES -->

  [Backups de Sites do Microsoft Azure]: http://www.windowsazure.com/pt-BR/documentation/articles/web-sites-backup/
  [Para restaurar um Site do Azure a partir de um backup feito anteriormente]: #PreviousBackup
  [Para restaurar um Site do Azure diretamente de uma conta de armazenamento]: #StorageAccount
  [Escolher as configurações de restauração do Site e iniciar a operação de restauração]: #RestoreSettings
  [Exibir os logs de operação]: #OperationLogs
  [Selecione a origem do backup]: ./media/web-sites-restore/01ChooseBackupSource.png
  [Arquivo da Conta de Armazenamento]: ./media/web-sites-restore/02StorageAccountFile.png
  [Procurar Armazenamento em Nuvem]: ./media/web-sites-restore/03BrowseCloudStorage.png
  [Arquivo da Conta de Armazenamento selecionado]: ./media/web-sites-restore/04StorageAccountFileSelected.png
  [Selecione suas configurações de restauração de site]: ./media/web-sites-restore/05ChooseRestoreSettings.png
  [Escolher um host de servidor de banco de dados]: ./media/web-sites-restore/06ChooseDBServer.png
  [Restaurar para um novo banco de dados SQL]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
  [Configurações do novo Banco de Dados SQL]: ./media/web-sites-restore/08NewSQLDBConfig.png
  [Site Contoso restaurado]: ./media/web-sites-restore/09RestoredContosoWebSite.png
  [Painel - Link dos Logs de Operação]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
  [Página Logs de Operação dos Serviços de Gerenciamento]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
  [Botão Detalhes]: ./media/web-sites-restore/12DetailsButton.png
  [Detalhes da Operação]: ./media/web-sites-restore/13OperationDetails.png
