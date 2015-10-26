<properties 
	pageTitle="Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Saiba como criar backups de seus aplicativos Web no Serviço de Aplicativo do Azure" 
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
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure


O recurso de Backup e Restauração em [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) permite que você crie backups de aplicativos Web facilmente, de modo manual ou automático. É possível restaurar seu aplicativo Web para um estado anterior ou criar um novo aplicativo Web baseado em um dos backups do seu aplicativo original.

Para saber como restaurar um aplicativo Web do Azure pelo backup, consulte [Restaurar um aplicativo Web](web-sites-restore.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="whatsbackedup"></a>
## Do que é feito backup 
Aplicativos Web podem fazer backup das seguintes informações:

* Configuração de aplicativo Web
* Conteúdo do arquivo de aplicativo Web
* Quaisquer bancos de dados de SQL Server ou MySQL conectados ao seu aplicativo (você pode escolher quais incluir no backup)

O backup dessas informações é feito no contêiner e na conta de armazenamento do Azure especificados por você.

> [AZURE.NOTE]Cada backup é uma cópia offline completa do aplicativo, não uma atualização incremental.

<a name="requirements"></a>
## Requisitos e restrições

* O recurso de Backup e Restauração requer que o site esteja em um modo Padrão. Para saber mais sobre como dimensionar seu aplicativo Web para usar o modo Padrão, consulte [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-scale.md). Observe que o modo Premium permite a realização de um número maior de backups diários que o modo Padrão.

* O recurso de Backup e Restauração requer um contêiner e uma conta de armazenamento do Azure, que devem pertencer à mesma assinatura que o aplicativo Web cujo backup você realizará. Se ainda não tiver uma conta de armazenamento, você poderá criar uma clicando em **Conta de Armazenamento** na folha **Backups** do [portal de visualização do Azure](http://portal.azure.com) e escolhendo **Conta de Armazenamento** e o **Contêiner** na folha **Destino**. Para obter mais informações sobre contas de armazenamento do Azure, consulte os [links](#moreaboutstorage) no final deste artigo.

* O recurso de Backup e Restauração oferece suporte para até 10 GB de conteúdo de site e banco de dados. Um erro será indicado nos Logs de Operação se a operação do recurso de backup for interrompida porque a carga excede esse limite.

<a name="manualbackup"></a>
## Criar um backup manual

1. No portal do Azure, escolha seu aplicativo Web na lâmina aplicativos Web. Isso exibirá os detalhes do seu aplicativo Web em uma nova lâmina.
2. Selecione a opção **Configurações**. A folha **Configurações** será exibida, permitindo que você modifique seu aplicativo Web.
	
	![Página Backups][ChooseBackupsPage]

3. Escolha a opção **Backups** na folha **Configurações**. A folha **Backups** será exibida.
	
4. Na folha **Backups**, escolha o destino de seu backup selecionando uma **Conta de Armazenamento** e um **Contêiner**. A conta de armazenamento deve pertencer à mesma assinatura do aplicativo Web do qual você vai fazer backup.
	
	![Escolher uma conta de armazenamento][ChooseStorageAccount]
	
5. Na opção **Bancos de Dados Incluídos** na folha **Backups**, selecione os bancos de dados que estão conectados a seu aplicativo Web (SQL Server ou MySQL) dos quais você deseja fazer backup.

	> [AZURE.NOTE]Para que um banco de dados apareça nessa lista, sua cadeia de conexão deve constar da seção **Cadeias de conexão** da folha **Configurações do aplicativo Web**, presente no portal.
	
6. Na folha **Backups**, selecione o **Destino do backup**. Você deve escolher uma conta de armazenamento e um contêiner existentes.
7. Na barra de comandos, clique em **Fazer Backup Agora**.
	
	![Botão BackUpNow][BackUpNow]
	
	Você verá uma mensagem informando o andamento do processo de backup.
	

Você pode fazer um backup manual a qualquer momento.

<a name="automatedbackups"></a>
## Configurar backups automáticos

1. Na folha **Backups**, defina **Backup Agendado** como ATIVADO.
	
	![Habilitar backups automatizados][SetAutomatedBackupOn]
	
2. Selecione a conta de armazenamento na qual você deseja fazer backup de seu aplicativo Web. A conta de armazenamento deve pertencer à mesma assinatura do aplicativo Web do qual você vai fazer backup.
	
	![Escolher uma conta de armazenamento][ChooseStorageAccount]
	
3. Na caixa **Frequência**, especifique a frequência na qual você deseja que os backups automatizados sejam feitos. O número de dias deve ser entre 1 e 90, inclusive (de uma vez por dia a uma vez a cada 90 dias).
	
4. Use a opção **Início** para especificar uma data e hora em que deseja que os backups automatizados sejam iniciados.
	
	> [AZURE.NOTE]O Azure armazena as horas de backup no formato UTC, mas as exibe de acordo com a hora do sistema no computador que está sendo usado para exibir o portal.
	
5. Na seção **Bancos de Dados Incluídos**, selecione os bancos de dados que estão conectados ao aplicativo Web (SQL Server ou MySQL) dos quais você deseja fazer backup. Para que um banco de dados apareça na lista, sua cadeia de conexão deve constar na seção **Cadeias de conexão** da folha **Configurações do aplicativo Web**, presente no portal.
	
	> [AZURE.NOTE]Se você optar por incluir um ou mais bancos de dados no backup e tiver especificado uma frequência de menos de sete dias, você será avisado de que backups frequentes podem aumentar os custos do banco de dados.
	
6. Além disso, defina o valor **Retenção (dias)** para o número de dias pelos quais você deseja manter o backup.
7. Na barra de comandos, clique no botão **Salvar** para salvar as alterações da configuração (ou escolha **Descartar** se você decidir não salvá-las).
	
	![Botão Salvar][SaveIcon]

<a name="notes"></a>
## Observações

* Não deixe de configurar as cadeias de conexão para cada um dos seus bancos de dados adequadamente na folha **Configurações de aplicativo Web**, dentro da opção **Configurações** no próprio aplicativo Web, para que o recurso de Backup e Restauração possa incluir seus bancos de dados.
* Embora você possa fazer backup de mais de um aplicativo Web na mesma conta de armazenamento, para facilitar a manutenção, considere criar uma conta de armazenamento separada para cada aplicativo Web.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<a name="partialbackups"></a>
## Fazer backup de apenas parte do aplicativo Web

Às vezes, você não quer fazer backup de tudo em seu aplicativo Web. Veja alguns exemplos:

-	Você [configura backups semanais](web-sites-backup.md#configure-automated-backups) do aplicativo Web que inclui conteúdo estático que nunca muda, como postagens ou imagens antigas de blog.
-	O aplicativo Web tem mais de 10 GB de conteúdo (que é o volume máximo de backup por vez).
-	Você não deseja fazer backup dos arquivos de log.

Os backups parciais permitirão que você escolha exatamente quais arquivos deseja incluir no backup.

### Excluir arquivos do backup

Para excluir arquivos e pastas dos backups, crie um arquivo `_backup.filter` na pasta wwwroot do aplicativo Web e especifique a lista de arquivos e pastas que deseja excluir. Um modo fácil de acessá-la é pelo [Console do Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).

Suponha que você tenha um aplicativo Web que contenha arquivos de log e imagens estáticas de anos anteriores que nunca vão mudar. Você já tem um backup completo do aplicativo Web que inclui as imagens antigas. Agora, você quer fazer backup do aplicativo Web todos os dias, mas não quer pagar para armazenar arquivos de log ou de imagens estáticas que nunca mudam.

![Pasta Logs][LogsFolder] ![Pasta Imagens][ImagesFolder]
	
As etapas abaixo mostram como você pode excluir esses arquivos do backup.

1. Vá para `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e identifique as pastas que deseja excluir dos backups. Neste exemplo, você quer excluir os seguintes arquivos e pastas, mostrados nesta interface de usuário:

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]A última linha ilustra que você pode excluir os arquivos individualmente, assim como as pastas.

2. Crie um arquivo chamado `_backup.filter` e coloque a lista acima no arquivo, mas remova `D:\home`. Liste um diretório ou arquivo por linha. Portanto, o conteúdo do arquivo deve ser:

    \\site\\wwwroot\\Logs \\LogFiles \\site\\wwwroot\\Images\\2013 \\site\\wwwroot\\Images\\2014 \\site\\wwwroot\\Images\\brand.png

3. Carregue esse arquivo no diretório `D:\home\site\wwwroot` do seu site usando o [ftp](web-sites-deploy.md#ftp) ou qualquer outro método. Se desejar, você pode criar o arquivo diretamente em `http://{yourapp}.scm.azurewebsites.net/DebugConsole` e inserir o conteúdo lá mesmo.

4. Execute backups da mesma maneira que faria normalmente, [manual](#create-a-manual-backup) ou [automaticamente](#configure-automated-backups).

Agora, todos os arquivos e pastas especificados em `_backup.filter` serão excluídos do backup. Nesse exemplo, os arquivos de log e os arquivos de imagem 2013 e 2014 não entrarão mais no backup, bem como brand.png.

>[AZURE.NOTE]Você restaura backups parciais de seu site da mesma maneira como [restauraria um backup regular](web-sites-restore.md). O processo de restauração fará a coisa certa.
>
>Quando um backup completo é restaurado, todo o conteúdo do site é substituído por tudo o que está no backup. Se um arquivo estiver no site mas não no backup, será excluído. Mas, quando um backup parcial é restaurado, qualquer conteúdo que esteja localizado em um dos diretórios não autorizados ou qualquer arquivo não autorizado, é deixado como está.

<a name="aboutbackups"></a>

## Como os backups são armazenados

Depois de ter criado um ou mais backups do seu aplicativo Web, os backups ficarão visíveis na folha **Contêineres** de sua conta de armazenamento, bem como em seu aplicativo Web. Na conta de armazenamento, cada backup consiste em um arquivo .zip que contém os dados de backup e um arquivo .xml que contém um manifesto do conteúdo do arquivo .zip. Você pode descompactar e procurar esses arquivos se quiser acessar seus backups sem realmente executar uma restauração do aplicativo Web.

O backup do banco de dados para o aplicativo Web é armazenado na raiz do arquivo. zip. Para um banco de dados SQL, este é um arquivo BACPAC (sem extensão de arquivo) e pode ser importado. Para criar um novo banco de dados SQL com base na exportação do BACPAC, consulte [Importar um arquivo BACPAC para criar um novo banco de dados de usuário](http://technet.microsoft.com/library/hh710052.aspx).

> [AZURE.WARNING]A alteração de qualquer um dos arquivos no contêiner **websitebackups** pode fazer com que o backup se torne inválido e, portanto, não restaurável.

<a name="bestpractices"></a>
##Práticas Recomendadas

Em caso de falha ou desastre natural, convém ter a certeza de que você se preparou antecipadamente tendo uma estratégia de backup e restauração.

A estratégia de backup deve ser semelhante à seguinte:

-	Faça pelo menos um backup completo do seu aplicativo Web.
-	Faça backups parciais do seu aplicativo Web depois de ter um backup completo.

A estratégia de restauração deve ser semelhante à seguinte:
 
-	Crie um [slot de preparo](web-sites-staged-publishing.md) para seu aplicativo Web.
-	Restaure o backup completo do aplicativo Web no slot de preparo.
-	Restaure o último backup parcial sobre a restauração do backup completo, também no slot de preparo.
-	Teste a restauração para ver se o aplicativo preparado está funcionando corretamente.
-	[Troque](web-sites-staged-publishing.md#Swap) o aplicativo Web preparado no slot de produção.

>[AZURE.NOTE]Sempre teste o processo de restauração. Para saber mais, consulte [Algo muito bom](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Por exemplo, determinadas plataformas de blog, como [Ghost](https://ghost.org/), têm avisos explícitos sobre como elas se comportam durante um backup. Ao testar o processo de restauração, você pode capturar esses avisos quando ainda não tiver sido atingido por uma falha ou um desastre.

<a name="nextsteps"></a>
## Próximas etapas
Para saber como restaurar o aplicativo Web do backup, consulte [Restaurar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-restore.md).

Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure](/pricing/free-trial/).


<a name="moreaboutstorage"></a>
### Mais informações sobre contas de armazenamento

[O que é uma Conta de Armazenamento?](../storage-whatis-account.md)

[Como criar uma conta de armazenamento](../storage-create-storage-account/)

[Como monitorar uma conta de armazenamento](../storage-monitor-storage-account.md)

[Noções básicas sobre cobrança de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

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
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!---HONumber=Oct15_HO3-->