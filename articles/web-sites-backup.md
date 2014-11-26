<properties linkid="web-sites-backup" urlDisplayName="Azure Websites Backups" pageTitle="Azure Websites Backups" metaKeywords="Azure Web Sites, Backups" description="Learn how to create backups of your Azure websites." metaCanonical="" services="web-sites" documentationCenter="" title="Azure Websites Backups" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Backups de sites do Azure

O recurso de Backup e Restauração de Sites do Azure permite criar facilmente backups de sites manual ou automaticamente. É possível restaurar um site para um estado anterior ou criar um novo site baseado em um dos backups do seu site original.

Para obter informações sobre como restaurar um Site do Azure a partir do backup, consulte [Restaurar um Site do Azure][Restaurar um Site do Azure].

## Neste artigo

-   [Backup automático e fácil (Vídeo)][Backup automático e fácil (Vídeo)]
-   [Do que é feito backup][Do que é feito backup]
-   [Requisitos e restrições][Requisitos e restrições]
-   [Para criar um Backup Manual][Para criar um Backup Manual]
-   [Para configurar backups automatizados][Para configurar backups automatizados]
-   [Como os Backups são armazenados][Como os Backups são armazenados]
-   [Observações][Observações]
-   [Próximas etapas][Próximas etapas]

    -   [Mais informações sobre contas de armazenamento][Mais informações sobre contas de armazenamento]

<a name="video"></a>

## Backup automático e fácil (Vídeo)

Neste vídeo, Eduardo Laureano e Scott Hanselman introduzem os backups de sites do Azure. (Duração: 11:43)

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>

## Do que é feito backup

É feito o backup das seguintes informações dos Sites do Azure:

-   Configuração do site
-   Conteúdo de arquivos do site
-   Quaisquer bancos de dados SQL Server ou MySQL conectados ao seu site (você pode escolher quais incluir no backup)

O backup dessas informações é feito na conta de armazenamento do Azure especificada.

> [WACOM.NOTE] Cada backup é uma cópia offline completa do site, não uma atualização incremental.

<a name="requirements"></a>

## Requisitos e restrições

-   O recurso de Backup e Restauração requer que o site esteja em uma camada Standard. Para obter mais informações adicionais sobre como dimensionar seu site, consulte [Como dimensionar sites][Como dimensionar sites].

-   O recurso de Backup e Restauração requer uma conta de armazenamento do Azure que deve pertencer à mesma assinatura que o site do qual você vai fazer backup. Se ainda não tiver uma conta de armazenamento, você poderá criar uma clicando no botão **Armazenamento** (ícone de grade) no painel esquerdo do portal do Azure e, em seguida, escolhendo **Novo** na barra de comandos na parte inferior. Para obter mais informações sobre contas de armazenamento do Azure, consulte os [links][Mais informações sobre contas de armazenamento] no final deste artigo.

<a name="manualbackup"></a>

## Para criar um Backup Manual

1.  No portal do Azure de seu site, escolha a guia **Backups**.

    ![Página Backups][Página Backups]

2.  Selecione a conta de armazenamento na qual você deseja fazer backup de seu site. A conta de armazenamento deve pertencer à mesma assinatura do site do qual você irá fazer backup.

    ![Escolher uma conta de armazenamento][Escolher uma conta de armazenamento]

3.  Na opção **Bancos de Dados Incluídos**, selecione os bancos de dados que estão conectados ao site (SQL Server ou MySQL) dos quais você deseja fazer backup.

    ![Escolher bancos de dados a serem incluídos][Escolher bancos de dados a serem incluídos]

    > [WACOM.NOTE] Para que um banco de dados apareça nessa lista, sua cadeia de conexão deve constar da seção **Cadeias de Conexão** da guia Configurar no portal.

4.  Na barra de comandos, clique em **Fazer Backup Agora**.

    ![Botão BackUpNow][Botão BackUpNow]

    Você verá uma mensagem sobre o andamento do processo de backup:

    ![Mensagem sobre o andamento do backup][Mensagem sobre o andamento do backup]

Você pode fazer um backup manual a qualquer momento. Durante a Visualização, não podem ser feitos mais de dois backups manuais em um período de 24 horas (sujeito a alterações).

<a name="automatedbackups"></a>

## Para configurar backups automatizados

1.  Na página Backups, defina **Backup Automatizado** como ATIVADO.

    ![Habilitar backups automatizados][Habilitar backups automatizados]

2.  Selecione a conta de armazenamento na qual você deseja fazer backup de seu site. A conta de armazenamento deve pertencer à mesma assinatura do site do qual você irá fazer backup.

    ![Escolher uma conta de armazenamento][Escolher uma conta de armazenamento]

3.  Na caixa **Frequência**, especifique a frequência na qual você deseja que os backups automatizados sejam feitos. (Durante a Visualização, o número de dias é a única unidade de tempo disponível.)

    ![Escolher a frequência de backup][Escolher a frequência de backup]

    O número de dias deve ser entre 1 e 90, inclusive (de uma vez por dia a uma vez a cada 90 dias).

4.  Use a opção **Data de Início** para especificar uma data e hora em que deseja que os backups automatizados sejam iniciados.

    ![Escolher a data de início][Escolher a data de início]

    Os horários estão disponíveis em incrementos de meia hora.

    ![Escolher a hora de início][Escolher a hora de início]

    > [WACOM.NOTE] O Azure armazena as horas de backup no formato UTC, mas as exibe de acordo com a hora do sistema no computador que está sendo usado para exibir o portal.

5.  Na seção **Bancos de Dados Incluídos**, selecione os bancos de dados que estão conectados ao site (SQL Server ou MySQL) dos quais você deseja fazer backup. Para que um banco de dados apareça na lista, sua cadeia de conexão deve constar da seção **Cadeias de Conexão** da guia Configurar no portal.

    ![Escolher bancos de dados a serem incluídos][Escolher bancos de dados a serem incluídos]

    > [WACOM.NOTE] Se você optar por incluir um ou mais bancos de dados no backup e tiver especificado uma frequência de menos de sete dias, você será avisado de que backups frequentes podem aumentar os custos do banco de dados.

6.  Na barra de comandos, clique no botão **Salvar** para salvar as alterações da configuração (ou escolha **Descartar** se você decidir não salvá-las).

    ![Botão Salvar][Botão Salvar]

<a name="aboutbackups"></a>

## Como os Backups são armazenados

Depois que você fizer um ou mais backups, eles estarão visíveis na guia Contêineres da sua conta de armazenamento. Os backups estarão em um contêiner chamado **websitebackups**. Cada backup consiste em um arquivo .zip que contém os dados do backup e um arquivo .xml que contém um manifesto do conteúdo do arquivo .zip.

Os nomes dos arquivos .zip e .xml do backup consistem no nome do site seguido de um sublinhado e um carimbo de data/hora de quando o backup foi feito. O carimbo de data/hora contém a data no formato AAAAMMDD (em dígitos sem espaços) mais a hora no formato UTC de 24 horas (por exemplo, fabrikam\_201402152300.zip). O conteúdo desses arquivos pode ser descompactado e apresentado, caso você deseje acessar os backups sem realmente executar uma restauração do site.

O arquivo que é armazenado com o arquivo zip indica o nome do arquivo do banco de dados em *backupdescription* \> *databases* \> *databasebackupdescription* \> *filename*.

O mesmo arquivo de backup do banco de dados é armazenado na raiz do arquivo .zip. Para um banco de dados SQL, este é um arquivo BACPAC (sem extensão de arquivo) e pode ser importado. Para criar um novo banco de dados SQL baseado na exportação do BACPAC, você pode seguir as etapas no artigo [Importar um arquivo BACPAC para criar um novo banco de dados do usuário][Importar um arquivo BACPAC para criar um novo banco de dados do usuário].

Para obter mais informações sobre restaurar um site do Azure (incluindo bancos de dados) usando o portal de gerenciamento do Azure, consulte [Restaurar um site do Microsoft Azure][Restaurar um site do Microsoft Azure].

> [WACOM.NOTE] A alteração de qualquer um dos arquivos no contêiner **websitebackups** pode fazer com que o backup se torne inválido e, portanto, não restaurável.

<a name="notes"></a>

## Observações

-   Verifique se você configurou as cadeias de conexão de cada um de seus bancos de dados corretamente na guia Configurar do site para que o recurso de Backup e Restauração possa incluir seus bancos de dados.
-   Durante a Visualização, você é responsável por gerenciar o conteúdo salvo em backup em sua conta de armazenamento. Se você excluir um backup de sua conta de armazenamento e não fez uma cópia em outro local, não poderá restaurar o backup posteriormente.
-   Embora você possa fazer backup de mais de um site na mesma conta de armazenamento, para facilitar a manutenção, considere criar uma conta de armazenamento separada para cada site.
-   Durante a Visualização, as operações de backup e restauração estão disponíveis apenas por meio do Portal de Gerenciamento do Azure.

<a name="nextsteps"></a>

## Próximas etapas

Para obter informações sobre como restaurar um Site do Azure a partir do backup, consulte [Restaurar um Site do Azure][Restaurar um Site do Azure].

Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure][Avaliação Gratuita do Microsoft Azure].

<a name="moreaboutstorage"></a>

### Mais informações sobre contas de armazenamento

[O que é uma conta de armazenamento?][O que é uma conta de armazenamento?]

[Como: Criar uma conta de armazenamento][Como: Criar uma conta de armazenamento]

[Como monitorar uma conta de armazenamento][Como monitorar uma conta de armazenamento]

[Noções básicas sobre cobrança de armazenamento do Azure][Noções básicas sobre cobrança de armazenamento do Azure]

<!-- IMAGES -->

  [Restaurar um Site do Azure]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-restore/
  [Backup automático e fácil (Vídeo)]: #video
  [Do que é feito backup]: #whatsbackedup
  [Requisitos e restrições]: #requirements
  [Para criar um Backup Manual]: #manualbackup
  [Para configurar backups automatizados]: #automatedbackups
  [Como os Backups são armazenados]: #aboutbackups
  [Observações]: #notes
  [Próximas etapas]: #nextsteps
  [Mais informações sobre contas de armazenamento]: #moreaboutstorage
  [Como dimensionar sites]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/
  [Página Backups]: ./media/web-sites-backup/01ChooseBackupsPage.png
  [Escolher uma conta de armazenamento]: ./media/web-sites-backup/02ChooseStorageAccount.png
  [Escolher bancos de dados a serem incluídos]: ./media/web-sites-backup/03IncludedDatabases.png
  [Botão BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
  [Mensagem sobre o andamento do backup]: ./media/web-sites-backup/05BackupProgress.png
  [Habilitar backups automatizados]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
  [Escolher a frequência de backup]: ./media/web-sites-backup/07Frequency.png
  [Escolher a data de início]: ./media/web-sites-backup/08StartDate.png
  [Escolher a hora de início]: ./media/web-sites-backup/09StartTime.png
  [Botão Salvar]: ./media/web-sites-backup/10SaveIcon.png
  [Importar um arquivo BACPAC para criar um novo banco de dados do usuário]: http://technet.microsoft.com/pt-br/library/hh710052.aspx
  [Restaurar um site do Microsoft Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-restore/
  [Avaliação Gratuita do Microsoft Azure]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [O que é uma conta de armazenamento?]: http://www.windowsazure.com/pt-br/documentation/articles/storage-whatis-account/
  [Como: Criar uma conta de armazenamento]: http://www.windowsazure.com/pt-br/documentation/articles/storage-create-storage-account/
  [Como monitorar uma conta de armazenamento]: http://www.windowsazure.com/pt-br/documentation/articles/storage-monitor-storage-account/
  [Noções básicas sobre cobrança de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx
