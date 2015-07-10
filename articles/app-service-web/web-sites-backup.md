<properties 
	pageTitle="Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Saiba como criar backups de seus aplicativos Web no Serviço de Aplicativo do Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
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

# Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure


O recurso de Backup e Restauração em [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) permite que você crie backups de aplicativos Web facilmente, de modo manual ou automático. É possível restaurar seu aplicativo Web para um estado anterior ou criar um novo aplicativo Web baseado em um dos backups do seu aplicativo original.

Para saber como restaurar um aplicativo Web do Azure pelo backup, consulte [Restaurar um aplicativo Web](web-sites-restore.md).

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

* O recurso de Backup e Restauração requer um contêiner e uma conta de armazenamento do Azure, que devem pertencer à mesma assinatura que o aplicativo Web cujo backup você realizará. Se ainda não tiver uma conta de armazenamento, você pode criar uma clicando em **Conta de Armazenamento** na folha **Backups** do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) e, em seguida, escolhendo **Conta de Armazenamento** e o **Contêiner** na folha **Destino**. Para obter mais informações sobre contas de armazenamento do Azure, consulte os [links](#moreaboutstorage) no final deste artigo.

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
## Fazer backup de apenas uma parte do seu site

Às vezes, você não quer fazer backup de tudo em seu site, especialmente se fizer backup do site regularmente ou se o site tiver mais de 10 GB de conteúdo (ou seja, a quantidade máxima que pode ser incluída no backup de cada vez).

Por exemplo, provavelmente você não deseja fazer backup dos arquivos de log. Ou se você [configurar backups semanais](https://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/#configure-automated-backups), não desejará preencher sua conta de armazenamento com conteúdo estático que nunca muda, como postagens de blog e imagens antigas.

Os backups parciais permitirão que você escolha exatamente quais arquivos deseja incluir no backup.

###Especifique os arquivos que você não deseja incluir no backup
Você pode criar uma lista de arquivos e de pastas a serem excluídas do backup.

A lista é salva como um arquivo de texto chamado _backup.filter na pasta wwwroot do seu site. É uma maneira fácil de acessá-lo por meio do [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) em `http://{yoursite}.scm.azurewebsites.net/DebugConsole`. 

As instruções a seguir usarão o Console Kudu para criar o arquivo _backup.filter, mas você pode usar seu método de implantação favorito para colocar o arquivo ali.

###O que fazer
Tenho um site que contém os arquivos de log e imagens estáticas dos últimos anos que nunca serão alteradas.

Eu já tenho um backup completo do site que inclui as imagens antigas. Agora, desejo fazer um backup diário do site, mas não quero pagar pelo armazenamento de arquivos de log ou de arquivos de imagem estática que nunca mudam.

![Pasta Logs][LogsFolder]
![Pasta Imagens][ImagesFolder]
	
As etapas a seguir mostram como eu poderia excluir esses arquivos do backup.

####Identificar os arquivos e as pastas que você não deseja incluir no backup
Isso é fácil. Já sei que eu não quero fazer backup de arquivos de log e, portanto quero excluir `D:\home\site\wwwroot\Logs`.

Há outra pasta de arquivos de log com todos os aplicativos Web do Azure em `D:\home\LogFiles`. Vamos excluí-los também.

Também não quero fazer backup das imagens de anos anteriores repetidamente. E, portanto, vamos adicionar `D:\home\site\wwwroot\Images\2013` e `D:\home\site\wwwroot\Images\2014` à lista também.

Por fim, não vamos fazer backup do arquivo brand.png na pasta Imagens, somente para mostrarmos que também podemos incluir arquivos individuais na lista negra. Ele está localizado no `D:\home\site\wwwroot\Images\brand.png`

Isso nos dará as pastas a seguir, que não queremos incluir no backup:

* D:\home\site\wwwroot\Logs
* D:\home\LogFiles
* D:\home\site\wwwroot\Images\2013
* D:\home\site\wwwroot\Images\2014
* D:\home\site\wwwroot\Images\brand.png

#### Criar a lista de exclusões
Salvar a lista negra de arquivos e de pastas que você não deseja incluir no backup em um arquivo especial chamado _backup.filter. Crie o arquivo e o coloque em `D:\home\site\wwwroot_backup.filter`.

Liste todos os arquivos e as pastas que você não deseja incluir no backup no arquivo _backup.filter. Adicione o caminho completo relativo a D:\home da pasta ou do arquivo que você deseja excluir do backup, um caminho por linha.

Então, para o meu site, `D:\home\site\wwwroot\Logs` se torna `\site\wwwroot\Logs`, `D:\home\LogFiles` se torna `\LogFiles` e assim por diante, resultando no seguinte conteúdo para meu _backup.filter:

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

Observe o `` inicial no começo de cada linha. Isso é importante.

###Executar um backup
Agora você pode executar backups da mesma forma como faria normalmente. [Manualmente](https://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/#create-a-manual-backup), [automaticamente](https://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/#configure-automated-backups), qualquer uma das duas opções funciona bem.

Os arquivos e as pastas que recaírem nos filtros listados no _backup.filter serão excluídos do backup. Isso significa que agora os arquivos de log e os arquivos de imagem de 2013 e de 2014 não serão incluídos no backup.

###Restaurando o site incluído no backup
Você restaura backups parciais de seu site da mesma maneira como [restauraria um backup regular](https://azure.microsoft.com/pt-br/documentation/articles/web-sites-restore/). Isso funcionará bem.

####Os detalhes técnicos
Com backups completos (não parciais), normalmente todo o conteúdo do site será substituído pelo que estiver no backup. Se um arquivo estiver no site mas não no backup, será excluído.

Mas ao restaurar backups parciais, qualquer conteúdo localizado em uma das pastas da lista negra (como `D:\home\site\wwwroot\images\2014` para o meu site) será deixado como está. E se os arquivos individuais estiverem na lista negra, também serão deixados como estão durante a restauração.

<a name="aboutbackups"></a>
## Como os backups são armazenados

Depois de ter feito um ou mais backups, os backups ficarão visíveis na folha **Contêineres** de sua **Conta de Armazenamento**, bem como em seu aplicativo Web. Na **Conta de Armazenamento**, cada backup consiste em um arquivo .zip que contém os dados armazenados em backup e um arquivo .xml que contém um manifesto do conteúdo do arquivo .zip.

Os nomes dos arquivos .zip e .xml do backup consistem no nome do aplicativo Web seguido de um sublinhado e um carimbo de data/hora de quando o backup foi feito. O carimbo de data/hora contém a data no formato AAAAMMDD (em dígitos sem espaços) mais a hora no formato UTC de 24 horas (por exemplo, fabrikam_201402152300.zip). O conteúdo desses arquivos pode ser descompactado e apresentado, caso você deseje acessar os backups sem realmente executar uma restauração do aplicativo Web.

O arquivo que é armazenado com o arquivo zip indica o nome do arquivo do banco de dados em *backupdescription* > *databases* > *databasebackupdescription* > *filename*.

O mesmo arquivo de backup do banco de dados é armazenado na raiz do arquivo .zip. Para um banco de dados SQL, este é um arquivo BACPAC (sem extensão de arquivo) e pode ser importado. Para criar um novo banco de dados SQL baseado na exportação do BACPAC, você pode seguir as etapas no artigo [Importar um arquivo BACPAC para criar um novo banco de dados do usuário](http://technet.microsoft.com/library/hh710052.aspx).

Para saber como restaurar um aplicativo Web (incluindo bancos de dados) usando o Portal do Azure, consulte [Restaurar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-restore.md).

> [AZURE.NOTE]A alteração de qualquer um dos arquivos no contêiner **websitebackups** pode fazer com que o backup se torne inválido e, portanto, não restaurável.

<a name="bestpractices"></a>
##Práticas Recomendadas
O que fazer quando houver um desastre e você tiver de restaurar seu site? Verifique se você está preparado com antecedência.

Sim, você pode ter backups parciais, mas faça pelo menos um backup completo do site primeiro para ter todo o conteúdo do site inserido no backup (esse é o pior cenário de planejamento). Em seguida, quando estiver restaurando seus backups, poderá restaurar primeiro o backup completo do site e então restaurar o backup parcial mais recente em cima dele.

Eis o porquê: ele permite que você use [Slots de implantação](https://azure.microsoft.com/pt-br/documentation/articles/web-sites-staged-publishing/) para testar seu site restaurado. Você pode até mesmo testar o processo de restauração sem usar o site de produção. E testar seu processo de restauração é [uma ótima ideia](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/). Você nunca vai saber quando poderá encontrar algum problema sutil, como quando tentei restaurar meu blog e terminei perdendo metade do conteúdo.

###Uma história de horror

Meu blog usa a plataforma de blogs [Ghost](https://ghost.org/). Como um desenvolvedor responsável, criei um backup do meu site e tudo estava ótimo. Então, um dia recebi uma mensagem dizendo que havia uma nova versão do Ghost disponível e que eu poderia atualizar meu blog para ela. Ótimo!

Criei mais um backup do meu site para incluir minhas postagens de blog mais recentes e comecei a atualizar o Ghost.

No meu site de produção.

Tremendo erro.

Algo deu errado com a atualização, minha tela inicial mostrava apenas uma tela em branco. “Sem problemas”, pensei, “Vou restaurar o backup que acabei de fazer”.

Eu restaurei a atualização, vi que tudo tinha voltado... Exceto as postagens do blog.

O QUÊ????

Acontece que, nas [notas de atualização do Ghost](http://support.ghost.org/how-to-upgrade/) há este aviso:

![Você pode fazer uma cópia do banco de dados de conteúdo/dados, mas não deverá fazer isso enquanto o Ghost estiver em execução. Pare-o primeiro][GhostUpgradeWarning]

Se você tentar fazer backup dos dados enquanto o Ghost estiver em execução... Na verdade, os dados não são incluídos no backup.

Deprimente.

Se eu tivesse tentado restaurar em um slot de teste primeiro, teria visto esse problema e não teria perdido todas as minhas postagens.

A vida é assim. Pode acontecer [aos melhores de nós](http://blog.codinghorror.com/international-backup-awareness-day/).

Teste seus backups.

<a name="nextsteps"></a>
## Próximas etapas
Para saber como restaurar um aplicativo Web do Azure pelo backup, consulte [Restaurar um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-restore.md).

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
 

<!----HONumber=62-->