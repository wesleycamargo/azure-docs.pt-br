<properties linkid="web-sites-backup" urlDisplayName="Backups de Sites do Azure" pageTitle="Backups de Sites do Azure" metaKeywords="Sites do Azure, Backups" description="Saiba como criar backups de seus Sites Azure." metaCanonical="" services="web-sites" documentationCenter="" title="Backups de Sites do Azure" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

#Backups de Sites do Azure

O recurso de Backup e Restauração de Sites do Azure permite criar facilmente backups de sites manual ou automaticamente. É possível restaurar um site para um estado anterior ou criar um novo site baseado em um dos backups do seu site original. 


Para obter informações sobre como restaurar um Site do Azure a partir do backup, consulte [Restaurar um Site do Azure](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-restore/).

##Neste artigo

- [Do que é feito backup](#whatsbackedup)
- [Requisitos e restrições](#requirements)
- [Para criar um Backup Manual](#manualbackup)
- [Para configurar backups automatizados](#automatedbackups)
- [Como os Backups são armazenados](#aboutbackups)
- [Observações](#notes)
- [Próximas etapas](#nextsteps)
	- [Mais informações sobre contas de armazenamento](#moreaboutstorage)

<a name="whatsbackedup"></a>
##Do que é feito backup É feito o backup das seguintes informações dos Sites do Azure:

* Configuração do Site
* Conteúdo do arquivo do Site
* Quaisquer bancos de dados SQL Server ou MySQL conectados ao seu site (você pode escolher quais incluir no backup)

O backup dessas informações é feito na conta de armazenamento do Azure especificada. 

> [WACOM.NOTE] Cada backup é uma cópia offline completa do site, não uma atualização incremental.

<a name="requirements"></a>
##Requisitos e restrições

* O recurso de Backup e Restauração requer que o site esteja em uma camada Standard. Para obter mais informações adicionais sobre como dimensionar seu site, consulte [Como dimensionar sites](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/). 

* O recurso de Backup e Restauração requer uma conta de armazenamento do Azure que deve pertencer à mesma assinatura que o site do qual você vai fazer backup. Se ainda não tiver uma conta de armazenamento, você poderá criar uma clicando no botão **Armazenamento** (ícone de grade) no painel esquerdo do portal do Azure e, em seguida, escolhendo **Novo** na barra de comandos na parte inferior. Para obter mais informações sobre contas de armazenamento do Azure, consulte os [links](#moreaboutstorage) no final deste artigo.

<a name="manualbackup"></a>
## Para criar um backup manual

1. No portal do Azure de seu site, escolha a guia **Backups**.
	
	![Página Backups][ChooseBackupsPage]
	
2. Selecione a conta de armazenamento na qual você deseja fazer backup de seu site. A conta de armazenamento deve pertencer à mesma assinatura do site do qual você irá fazer backup.
	
	![Escolher uma conta de armazenamento][ChooseStorageAccount]
	
3. Na opção **Bancos de Dados Incluídos**, selecione os bancos de dados que estão conectados ao site (SQL Server ou MySQL) dos quais você deseja fazer backup. 
	
	![Escolher bancos de dados a serem incluídos][IncludedDatabases]

	> [WACOM.NOTE] 	Para que um banco de dados apareça nessa lista, sua cadeia de conexão deve constar da seção **Cadeias de Conexão** da guia Configurar no portal.
	
4. Na barra de comandos, clique em **Fazer Backup Agora**.
	
	![Botão BackUpNow][BackUpNow]
	
	Você verá uma mensagem sobre o andamento do processo de backup:
	
	![Mensagem sobre o andamento do backup][BackupProgress]
	
Você pode fazer um backup manual a qualquer momento. Durante a Visualização, não podem ser feitos mais de dois backups manuais em um período de 24 horas (sujeito a alterações).  

<a name="automatedbackups"></a>
## Para configurar backups automatizados

1. Na página Backups, defina **Backup Automatizado** como ATIVADO.
	
	![Habilitar backups automatizados][SetAutomatedBackupOn]
	
2. Selecione a conta de armazenamento na qual você deseja fazer backup de seu site. A conta de armazenamento deve pertencer à mesma assinatura do site do qual você irá fazer backup.
	
	![Escolher uma conta de armazenamento][ChooseStorageAccount]
	
3. Na caixa **Frequência**, especifique a frequência na qual você deseja que os backups automatizados sejam feitos. (Durante a Visualização, o número de dias é a única unidade de tempo disponível.)
	
	![Escolher a frequência de backup][Frequency]
	
	O número de dias deve ser entre 1 e 90, inclusive (de uma vez por dia a uma vez a cada 90 dias).
	
4. Use a opção **Data de Início** para especificar uma data e hora em que deseja que os backups automatizados sejam iniciados; 
	
	![Escolher a data de início][StartDate]
	
	Os horários estão disponíveis em incrementos de meia hora.
	
	![Escolher a hora de início][StartTime]
	
	> [WACOM.NOTE] O Azure armazena as horas de backup no formato UTC, mas as exibe de acordo com a hora do sistema no computador que está sendo usado para exibir o portal.
	
5. Na seção **Bancos de Dados Incluídos**, selecione os bancos de dados que estão conectados ao site (SQL Server ou MySQL) dos quais você deseja fazer backup. Para que um banco de dados apareça na lista, sua cadeia de conexão deve constar da seção **Cadeias de Conexão** da guia Configurar no portal.
	
	![Escolher bancos de dados a serem incluídos][IncludedDatabases]
	
	> [WACOM.NOTE] Se você optar por incluir um ou mais bancos de dados no backup e tiver especificado uma frequência de menos de sete dias, você será avisado de que backups frequentes podem aumentar os custos do banco de dados.
	
6. Na barra de comandos, clique no botão **Salvar** para salvar as alterações da configuração (ou escolha **Descartar** se você decidir não salvá-las).
	
	![Botão Salvar][SaveIcon]

<a name="aboutbackups"></a>
## Como os Backups são armazenados

Depois que você fizer um ou mais backups, eles estarão visíveis na guia Contêineres da sua conta de armazenamento. Os backups estarão em um contêiner chamado **websitebackups**. Cada backup consiste em um arquivo .zip que contém os dados do backup e um arquivo .xml que contém um manifesto do conteúdo do arquivo .zip. 

Os nomes dos arquivos .zip e .xml do backup consistem no nome do site seguido de um sublinhado e um carimbo de data/hora de quando o backup foi feito. O carimbo de data/hora contém a data no formato AAAAMMDD (em dígitos sem espaços) mais a hora no formato UTC de 24 horas (por exemplo, fabrikam_201402152300.zip). O conteúdo desses arquivos pode ser descompactado e apresentado, caso você deseje acessar os backups sem realmente executar uma restauração do site.

> [WACOM.NOTE] A alteração de qualquer um dos arquivos no contêiner **websitebackups** pode fazer com que o backup se torne inválido e, portanto, não restaurável.

<a name="notes"></a>
## Observações

* Verifique se você configurou as cadeias de conexão de cada um de seus bancos de dados corretamente na guia Configurar do site para que o recurso de Backup e Restauração possa incluir seus bancos de dados.
* Durante a Visualização, você é responsável por gerenciar o conteúdo salvo em backup em sua conta de armazenamento. Se você excluir um backup de sua conta de armazenamento e não fez uma cópia em outro local, não poderá restaurar o backup posteriormente. 
* Embora você possa fazer backup de mais de um site na mesma conta de armazenamento, para facilitar a manutenção, considere criar uma conta de armazenamento separada para cada site.
* Durante a Visualização, as operações de backup e restauração estão disponíveis apenas por meio do Portal de Gerenciamento do Azure.

<a name="nextsteps"></a>
## Próximas etapas
Para obter informações sobre como restaurar um Site do Azure a partir do backup, consulte [Restaurar um site do Azure](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-restore/).

<a name="moreaboutstorage"></a>
### Mais informações sobre contas de armazenamento

[O que é uma conta de armazenamento?](http://www.windowsazure.com/pt-br/documentation/articles/storage-whatis-account/)

[Como criar uma conta de armazenamento](http://www.windowsazure.com/pt-br/documentation/articles/storage-create-storage-account/)

[Como monitorar uma conta de armazenamento](http://www.windowsazure.com/pt-br/documentation/articles/storage-monitor-storage-account/)

[Noções básicas sobre cobrança de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png

