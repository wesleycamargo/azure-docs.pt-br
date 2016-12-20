---
title: Fazer backup de seu aplicativo no Azure
description: "Saiba como criar backups de seus aplicativos no Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5675c2e0c44356002595620df9b06c38a771af56


---
# <a name="back-up-your-app-in-azure"></a>Fazer backup de seu aplicativo no Azure
O recurso de Backup e Restauração no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) permite que você crie backups de aplicativos facilmente, de modo manual ou automático. É possível restaurar seu aplicativo para um estado anterior ou criar um novo aplicativo baseado em um dos backups de seu aplicativo original. 

Para obter informações sobre como restaurar um aplicativo por um backup, veja [Restaurar um aplicativo no Serviço de Aplicativo do Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Do que é feito backup
O Serviço de Aplicativo pode fazer backup das seguintes informações:

* Configuração do aplicativo
* Conteúdo do arquivo
* Quaisquer Bancos de Dados SQL do Azure ou bancos de dados MySQL do Azure (ClearDB) conectados ao aplicativo (é possível escolher quais serão incluídos no backup)

O backup dessas informações é feito no contêiner e na conta de armazenamento do Azure especificados por você. 

> [!NOTE]
> Cada backup é uma cópia offline completa do aplicativo, não uma atualização incremental.
> 
> 

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos e restrições
* O recurso de Backup e Restauração exige que o plano do Serviço de Aplicativo esteja na camada **Standard** ou superior. Para obter mais informações sobre como dimensionar seu plano do Serviço de Aplicativo para usar uma camada superior, veja [Escalar verticalmente um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md). Observe que a camada **Premium** permite um número maior de backups diários do que a camada **Standard**.
* Você precisa de uma conta de armazenamento do Azure e do contêiner na mesma assinatura do aplicativo do qual você deseja fazer backup. Para obter mais informações sobre contas de armazenamento do Azure, consulte os [links](#moreaboutstorage) no final deste artigo.
* Backups podem ter até 10 GB de conteúdo do aplicativo e do banco de dados. Você obterá um erro se o tamanho do backup exceder esse limite. 

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Criar um backup manual
1. No [portal do Azure](https://portal.azure.com), navegue até a folha do aplicativo, escolha **Configurações** e **Backups**. A folha **Backups** será exibida.
   
    ![Página Backups][ChooseBackupsPage]
   
   > [!NOTE]
   > Se você receber a mensagem abaixo, clique nela para atualizar seu plano do Serviço de Aplicativo antes de continuar com os backups.
   > Veja [Escalar verticalmente um aplicativo no Azure](web-sites-scale.md) para obter mais informações.  
   > ![Escolher uma conta de armazenamento](./media/web-sites-backup/01UpgradePlan.png)
   > 
   > 
2. Na folha **Backups**, clique em **Armazenamento: não configurado** para configurar uma conta de armazenamento.
   
    ![Escolher uma conta de armazenamento][ChooseStorageAccount]
3. Escolha o destino de seu backup selecionando uma **Conta de Armazenamento** e um **Contêiner**. A conta de armazenamento deve pertencer à mesma assinatura do aplicativo do qual você deseja fazer backup. Se desejar, você pode criar uma nova conta de armazenamento ou um novo contêiner nas respectivas folhas. Quando terminar, clique em **Selecionar**.
   
    ![Escolher uma conta de armazenamento](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. Na folha **Definir Configurações de Backup** que ainda está aberta, clique em **Configurações de Banco de Dados** e escolha os bancos de dados que deseja incluir nos backups (banco de dados SQL ou MySQL) e clique em **OK**.  
   
    ![Escolher uma conta de armazenamento](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > Para que um banco de dados apareça nessa lista, sua cadeia de conexão deve existir na seção **Cadeias de conexão** da folha **Configurações do aplicativo** do aplicativo.
   > 
   > 
5. Na folha **Definir Configurações de Backup**, clique em **Salvar**.    
6. Na barra de comando da folha **Backups**, clique em **Fazer Backup Agora**.
   
    ![Botão BackUpNow][BackUpNow]
   
    Você verá uma mensagem informando o andamento do processo de backup.

Depois de configurar uma conta de armazenamento e o contêiner para backups, é possível fazer um backup manual a qualquer momento.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurar backups automáticos
1. Na folha **Backups**, clique em **Agenda: não configurada**. 
   
    ![Escolher uma conta de armazenamento](./media/web-sites-backup/05ScheduleBackup.png)
2. Na folha **Configurações de Agendamento de Backups**, defina **Backup Agendado** para **Ativado**, configure o agendamento de backup conforme desejado e clique em **OK**.
   
    ![Habilitar backups automatizados][SetAutomatedBackupOn]
3. Na folha **Definir Configurações de Backup** que ainda está aberta, clique em **Configurações de Armazenamento** e escolha o destino do backup selecionando uma **Conta de Armazenamento** e um **Contêiner**. A conta de armazenamento deve pertencer à mesma assinatura do aplicativo do qual você deseja fazer backup. Se desejar, você pode criar uma nova conta de armazenamento ou um novo contêiner nas respectivas folhas. Quando terminar, clique em **Selecionar**.
   
    ![Escolher uma conta de armazenamento](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. Na folha **Definir Configurações de Backup**, clique em **Configurações de Banco de Dados** e selecione os bancos de dados que deseja incluir nos backups (banco de dados SQL ou MySQL) e clique em **OK**.  
   
    ![Escolher uma conta de armazenamento](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > Para que um banco de dados apareça nessa lista, sua cadeia de conexão deve existir na seção **Cadeias de conexão** da folha **Configurações do aplicativo** do aplicativo.
   > 
   > 
5. Na folha **Definir Configurações de Backup**, clique em **Salvar**.    

<a name="partialbackups"></a>

## <a name="backup-just-part-of-your-app"></a>Fazer backup de apenas uma parte de seu aplicativo
Às vezes, você não quer fazer backup de tudo em seu aplicativo. Veja alguns exemplos:

* Você [configura backups semanais](web-sites-backup.md#configure-automated-backups) do aplicativo que contém conteúdo estático que nunca muda, como imagens ou postagens antigas no blog.
* Seu aplicativo tem mais de 10 GB de conteúdo (que é o volume máximo de backup por vez).
* Você não deseja fazer backup dos arquivos de log.

Os backups parciais permitirão que você escolha exatamente quais arquivos deseja incluir no backup.

### <a name="exclude-files-from-your-backup"></a>Excluir arquivos do backup
Para excluir arquivos e pastas dos backups, crie um arquivo `_backup.filter` na pasta D:\home\site\wwwroot do aplicativo e especifique a lista de arquivos e pastas que você deseja excluir. Um modo fácil de acessá-la é pelo [Console do Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). 

Suponha que você tenha um aplicativo que contém arquivos de log e imagens estáticas de anos anteriores que nunca vão mudar. Você já tem um backup completo do aplicativo que inclui as imagens antigas. Agora, você quer fazer backup do aplicativo todos os dias, mas não quer pagar para armazenar arquivos de log ou de imagens estáticas que nunca mudam.

![Pasta de Logs][LogsFolder]
![Pasta de Imagens][ImagesFolder]

As etapas abaixo mostram como você pode excluir esses arquivos do backup.

1. Vá para `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e identifique as pastas que deseja excluir dos backups. Neste exemplo, você quer excluir os seguintes arquivos e pastas, mostrados nesta interface de usuário:
   
        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png
   
    [AZURE.NOTE] A última linha mostra que você pode excluir arquivos individualmente, bem como pastas.
2. Crie um arquivo chamado `_backup.filter` e coloque a lista acima no arquivo, mas remova `D:\home`. Liste um diretório ou arquivo por linha. Portanto, o conteúdo do arquivo deve ser:
   
    \site\wwwroot\Logs  \LogFiles  \site\wwwroot\Images\2013  \site\wwwroot\Images\2014  \site\wwwroot\Images\brand.png
3. Carregue esse arquivo no diretório `D:\home\site\wwwroot\` de seu site usando o [ftp](web-sites-deploy.md#ftp) ou qualquer outro método. Se desejar, você pode criar o arquivo diretamente em `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e inserir o conteúdo lá mesmo.
4. Execute backups da mesma maneira que faria normalmente, de modo [manual](#create-a-manual-backup) ou [automático](#configure-automated-backups).

Agora, todos os arquivos e pastas especificados em `_backup.filter` serão excluídos do backup. Nesse exemplo, os arquivos de log e os arquivos de imagem 2013 e 2014 não entrarão mais no backup, bem como brand.png.

> [!NOTE]
> Você restaura backups parciais de seu site da mesma maneira como [restauraria um backup regular](web-sites-restore.md). O processo de restauração fará a coisa certa.
> 
> Quando um backup completo é restaurado, todo o conteúdo do site é substituído por tudo o que está no backup. Se um arquivo estiver no site mas não no backup, será excluído. Mas, quando um backup parcial é restaurado, qualquer conteúdo que esteja localizado em um dos diretórios não autorizados ou qualquer arquivo não autorizado, é deixado como está.
> 
> 

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Como os backups são armazenados
Depois de criar um ou mais backups para seu aplicativo, os backups ficarão visíveis na folha **Contêineres** de sua conta de armazenamento, bem como em seu aplicativo. Na conta de armazenamento, cada backup consiste em um arquivo .zip que contém os dados de backup e um arquivo .xml que contém um manifesto do conteúdo do arquivo .zip. Será possível descompactar e procurar esses arquivos se você quiser acessar seus backups sem realmente executar uma restauração do aplicativo.

O backup de banco de dados do aplicativo é armazenado na raiz do arquivo .zip. Para um banco de dados SQL, este é um arquivo BACPAC (sem extensão de arquivo) e pode ser importado. Para criar um novo banco de dados SQL com base na exportação do BACPAC, veja [Importar um arquivo BACPAC para criar um novo banco de dados de usuário](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> A alteração de qualquer um dos arquivos no contêiner **websitebackups** pode fazer com que o backup se torne inválido e, portanto, não restaurável.
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como restaurar um aplicativo por um backup, veja [Restaurar um aplicativo no Serviço de Aplicativo do Azure](web-sites-restore.md). Você também pode fazer backup e restaurar aplicativos do Serviço de Aplicativo usando a API REST (veja [Usar a REST para fazer backup e restaurar aplicativos do Serviço de Aplicativo](websites-csm-backup.md)).

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequência]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png




<!--HONumber=Nov16_HO3-->


