<properties 
	pageTitle="Criar e gerenciar trabalhos do Banco de Dados Elástico usando o PowerShell" 
	description="PowerShell usado para gerenciar pools do Banco de Dados SQL do Azure" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="ddove; sidneyh" />

# Criar e gerenciar trabalhos de banco de dados elástico de Banco de Dados SQL usando o PowerShell (visualização)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## Visão geral

O recurso **trabalhos de Banco de Dados Elástico** (visualização) permite que você, de modo confiável, execute um script Transact-SQL (T-SQL) ou aplique um DACPAC em um grupo de bancos de dados, incluindo uma coleção de bancos de dados definida de modo personalizado, todos os bancos de dados em um [Pool de Banco de Dados Elástico (visualização)](sql-database-elastic-pool.md) ou um conjunto de fragmentos (criado usando a [biblioteca de cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md)). Durante a visualização, o recurso **trabalhos de Banco de Dados Elástico** é atualmente um serviço de nuvem do Azure hospedado pelo cliente, que permite a execução de tarefas administrativas ad hoc e agendadas, as quais são chamadas de trabalhos. Usando este recurso, você pode gerenciar de modo fácil e confiável grandes volumes de Bancos de Dados SQL do Azure em escala executando scripts Transact-SQL para realizar operações administrativas como alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, coleta de dados de desempenho ou coleta de telemetria do locatário (cliente). Para obter mais informações sobre trabalhos de Banco de Dados Elástico, consulte [Visão geral de trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md).

Com as APIs do PowerShell para **trabalhos de Banco de Dados Elástico**, você tem flexibilidade para definir o grupo de bancos de dados no qual os scripts serão executados. Atualmente, a funcionalidade de **trabalhos de Banco de Dados Elástico** por meio do portal do Azure tem um conjunto de recursos reduzido e limita-se à execução em pools de banco de dados elástico.

O recurso **trabalhos de Banco de Dados Elástico** (visualização) usa vários componentes do Azure para definir os trabalhos a serem executados, definir quando executar os trabalhos, executá-los, acompanhar o êxito ou a falha dos trabalhos e, opcionalmente, especificar um destino de resultados para os resultados retornando consultas. Como as APIs do Powershell incluídas nesta visualização contêm funcionalidade adicional desde a visualização inicial por meio do Portal, é recomendável que você instale a versão mais recente dos componentes do recurso **trabalhos de Banco de Dados Elástico**. Se já estiver instalado, você pode simplesmente atualizar os componentes do recurso **trabalhos de Banco de Dados Elástico**. Para obter mais informações sobre a instalação do [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs), consulte [Instalar os componentes do trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md).

Este artigo mostrará como criar tudo o que você precisa para criar e gerenciar **trabalhos de Banco de Dados Elástico**, exceto pela assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em AVALIAÇÃO GRATUITA na parte superior desta página e, em seguida, voltar para concluir este artigo. Este tópico estende o exemplo encontrado na [Introdução às ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md). Quando concluído, você aprenderá como criar e gerenciar trabalhos para executar operações administrativas em relação a um grupo de bancos de dados fragmentados definidos por um **conjunto de fragmentos** e, alternativamente, por uma coleção de bancos de dados personalizada.

## Pré-requisitos
* Uma assinatura do Azure. Para obter uma avaliação gratuita, confira [Um mês de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
* O pacote do PowerShell para **trabalhos de Banco de Dados Elástico** deve primeiro ser baixado e importado e, em seguida, os componentes do trabalhos de Banco de Dados Elástico devem ser instalados. Siga essas etapas: [Instalando trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md)
* Azure PowerShell, versão 0.8.16 ou posterior. Instale a versão mais recente (0.9.5) por meio do [Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

### Selecionar sua assinatura do Azure

Para selecionar a assinatura é necessário ter a ID ou o nome da assinatura (**-SubscriptionId** ou **-SubscriptionName**). Se você tiver várias assinaturas, poderá executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do conjunto de resultados. Uma vez que você tenha suas informações de assinatura, execute o cmdlet a seguir para definir esta assinatura como padrão, ou seja, o destino para a criação e gerenciamento de trabalhos:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

O uso do [ISE do PowerShell](https://technet.microsoft.com/library/dd315244.aspx) é recomendado ao desenvolver e executar scripts do PowerShell em trabalhos de Banco de Dados Elástico.

## Objetos de trabalhos de Banco de Dados Elástico

A tabela a seguir lista todos os tipos de objeto de **trabalhos de Banco de Dados Elástico** junto com sua descrição e as APIs do PowerShell relevantes.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descrição</th>
    <th>APIs do PowerShell relacionadas</th>
  </tr>
  <tr>
    <td>Credencial</td>
    <td>Nome de usuário e senha para usar ao se conectar a bancos de dados para execução de scripts ou aplicação de DACPACs. <p>A senha é criptografada antes de enviar para e armazenar no banco de dados de trabalhos de banco de dados elástico. A senha é descriptografada pelo serviço trabalhos de Banco de Dados Elástico por meio da credencial criada e carregada por meio do script de instalação.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>O script do Transact-SQL a ser usado para execução em bancos de dados. O script deve ser criado para ser idempotente, já que o serviço tentará novamente executar o script após a ocorrência de quaisquer falhas.
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>O pacote de <a href="https://msdn.microsoft.com/library/ee210546.aspx">aplicativo da camada de dados</a> a ser aplicado a bancos de dados.

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>Destino do Banco de Dados</td>
    <td>Nome do banco de dados e do servidor apontando para um Banco de Dados SQL do Azure.

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>Destino do mapa de fragmentos</td>
    <td>Combinação de um destino de banco de dados e uma credencial a ser usada para determinar as informações armazenadas em um mapa de fragmentos de banco de dados elástico.
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destino de coleção personalizada</td>
    <td>Grupo definido de bancos de dados a serem usados coletivamente para execução.</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destino filho de coleção personalizada</td>
    <td>Destino do banco de dados que é referenciado em uma coleção personalizada.</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Trabalho</td>
    <td>
	<p>Definição de parâmetros para um trabalho que pode ser usado para disparar a execução ou para atender a um cronograma.</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>Execução do Trabalho</td>
    <td>
	<p>Contêiner de tarefas necessárias para a execução de um script ou então para a aplicação de um DACPAC em um destino usando credenciais para conexões de banco de dados com falhas tratadas de acordo com uma política de execução.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Execução de tarefa de trabalho</td>
    <td>
	<p>Unidade de trabalho individual para concluir um trabalho.</p>
	<p>Se uma tarefa de trabalho não for capaz de executar com êxito, a mensagem de exceção resultante será registrada e uma nova tarefa de trabalho correspondente será criada e executada de acordo com a política de execução especificada.</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Política de execução de trabalho</td>
    <td>
	<p>Controla os tempos limite de execução do trabalho, os limites de repetição e os intervalos entre as tentativas.</p>
	<p>O recurso trabalhos de banco de dados elástico inclue uma política de execução de trabalho padrão que gera, essencialmente, infinitas repetições de tarefas de trabalho com falha, com retirada exponencial de intervalos entre cada repetição.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>Agenda</td>
    <td>
	<p>Especificação baseada em tempo para execução, a ocorrer em um intervalo recorrente ou uma única vez.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>Gatilhos de trabalho</td>
    <td>
	<p>Um mapeamento entre um trabalho e um cronograma, para disparar a execução do trabalho de acordo com esse cronograma.</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## Tipos de grupo de trabalhos de Banco de Dados Elástico com suporte
O recurso **trabalhos de Banco de Dados Elástico** permite a execução de scripts Transact-SQL (T-SQL) ou aplicativo de DACPACs em um grupo de bancos de dados. Quando um trabalho for enviado para ser executado em um grupo de bancos de dados, o recurso trabalhos de Banco de Dados Elástico "expandirá" o trabalho em trabalhos filho onde cada um desses trabalhos filho realiza a execução solicitada em um único banco de dados no grupo.
 
Veja a seguir uma lista dos tipos de grupo com suporte atualmente:

* [Mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md): quando um trabalho for enviado para um mapa de fragmentos, o recurso trabalhos consultará primeiro o mapa de fragmentos para determinar seu conjunto de fragmentos atual e então expandirá o trabalho para trabalhos filho, cada um dos quais corresponderá a um fragmento contido no mapa de fragmentos.
* Coleção personalizada: especificada para indicar um conjunto personalizado definido de bancos de dados. Quando um trabalho é enviado para destinar-se a uma coleção personalizada, o recurso trabalhos expandirá o trabalho para trabalhos filho correspondentes a cada banco de dados definido atualmente na coleção personalizada.

## Configurando a conexão do recurso trabalhos de Banco de Dados Elástico
Depois de carregar o módulo do PowerShell, a conexão precisa ser definida para o recurso *Banco de Dados de Controle* do trabalhos de Banco de Dados Elástico antes de usar as APIs de trabalhos. Chamar esse cmdlet fará com que uma janela de credencial surja, solicitando o nome de usuário/senha fornecidos ao instalar o recurso trabalhos de Banco de Dados Elástico. Todos os exemplos fornecidos neste tópico pressupõem que a primeira etapa já foi executada.

Abrir uma conexão ao recurso trabalhos de Banco de Dados Elástico:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Credenciais criptografadas no recurso trabalhos de Banco de Dados Elástico
Credenciais de banco de dados podem ser inseridas no recurso *Banco de Dados de Controle* do trabalhos de Banco de Dados Elástico com sua senha criptografada. É necessário armazenar credenciais para habilitar os trabalhos a serem executados posteriormente, incluindo o uso de planos de trabalho.
 
Criptografia funciona por meio de um certificado criado como parte do script de instalação. O script de instalação cria e carrega o certificado no Serviço de Nuvem do Azure para descriptografia das senhas criptografadas armazenadas. O Serviço de Nuvem do Azure armazena posteriormente a chave pública no *Banco de Dados de Controle* dos trabalhos de Banco de Dados Elástico, que permite que a interface do Portal do Azure ou API do PowerShell criptografe uma senha fornecida sem exigir que o certificado seja instalado localmente.
 
As senhas de credenciais são criptografadas e protegidas contra usuários com acesso somente leitura para objetos de trabalhos de Banco de Dados Elástico; no entanto, é possível que um usuário mal-intencionado, com acesso de leitura/gravação para objetos de trabalhos de Banco de Dados Elástico, seja capaz de extrair uma senha. As credenciais são projetadas para ser reutilizadas em execuções de trabalho. As credenciais são passadas aos bancos de dados de destino durante o estabelecimento de conexões. Atualmente, não há restrições quanto aos bancos de dados de destino usados para cada credencial; portanto, é possível que um usuário mal-intencionado adicione um destino de banco de dados para um determinado banco de dados sob seu controle e, subsequentemente, inicie um trabalho direcionado a esse banco de dados para descobrir a senha da credencial.

Há práticas recomendadas para trabalhos de Banco de Dados Elástico:

* Limite o uso das APIs somente a pessoas confiáveis.
* As credenciais devem ter os privilégios mínimos necessários para executar a tarefa de trabalho. Mais informações podem ser vistas dentro desse artigo [Autorização e Permissões](https://msdn.microsoft.com/library/bb669084.aspx) do MSDN do SQL Server.

### Criando uma credencial criptografada para execução do trabalho em bancos de dados
Para criar uma nova credencial criptografada, o cmdlet Get-Credential solicitará um nome de usuário e senha que podem ser passados para o cmdlet **New-AzureSqlJobCredential**.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Atualizando Credenciais
Para atualizar uma credencial existente para dar suporte ao caso quando alterar senhas, use o cmdlet **Set-AzureSqlJobCredential** e defina o parâmetro **CredentialName**.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Definindo um Destino de Mapa de Fragmentos de Banco de Dados Elástico
Executar um trabalho em todos os bancos de dados em um conjunto de fragmentos (criado usando a [biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-database-client-library.md)) usando um mapa de fragmentos como o destino dos bancos de dados. Este exemplo depende de você criar um aplicativo fragmentado usando a biblioteca de cliente do Banco de Dados Elástico. Baixe e execute o [exemplo da Introdução às ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).

###Criar um gerenciador de mapa de fragmentos usando o aplicativo de exemplo

Aqui você vai criar um gerenciador de mapa de fragmentos juntamente com vários fragmentos, seguido pela inserção de dados nos fragmentos. Se você já tem fragmentos configurados, poderá ignorar as etapas a seguir e ir para a próxima seção.

1. Compile e execute o aplicativo de exemplo da **Introdução às ferramentas de Banco de Dados Elástico**. Siga as etapas até a etapa 7 na seção [Baixe e execute o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). No final da etapa 7, você verá o seguinte prompt de comando:

	![prompt de comando][1]

2.  Na janela de comando, digite "1" e pressione **Enter**. Isso cria o gerenciador de mapa de fragmentos e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e pressione **Enter**. Repita a ação mais quatro vezes. Isso insere linhas de dados de exemplo no seus fragmentos.
  
3.  O [Portal de Visualização do Azure](https://portal.azure.com) deve mostrar três novos bancos de dados em seu servidor v12:

	![Confirmação do Visual Studio][2]

Agora crie um destino para o mapa de fragmentos, usando o cmdlet **New-AzureSqlJobTarget**. O banco de dados do gerenciador do mapa de fragmentos deve ser definido como um destino de banco de dados e, em seguida, o mapa de fragmentos específico é especificado como um destino.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Criar um script T-SQL para execução em bancos de dados

Ao criar scripts T-SQL para execução, é altamente recomendável criá-los para que sejam idempotentes e resistentes contra falhas. O recurso trabalhos de Banco de Dados Elástico tentará novamente a execução de um script sempre que ocorrer uma falha nessa execução, independentemente da classificação da falha.

Use o cmdlet **New-AzureSqlJobContent** para criar e salvar um script para execução e defina os parâmetros **-ContentName** e **-CommandText**.

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Criar um novo script com base em um arquivo
Se o script T-SQL é definido dentro de um arquivo, o script a seguir pode ser usado para importar o script:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Atualizar um Script T-SQL para Execução em Bancos de Dados  

O script de PowerShell a seguir pode ser usado para atualizar o texto do comando T-SQL para um script existente.

Defina as variáveis a seguir para refletirem a definição de script que deseja configurar:

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### Atualizar a definição para um script existente

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##Criar um trabalho para executar um script em um mapa do fragmentos

O script do PowerShell a seguir pode ser usado para iniciar um trabalho para execução de um script em cada fragmento em um mapa de fragmentos de Escala Elástica.

Defina as variáveis a seguir para refletirem a definição de script e o destino desejados:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

##Executar um trabalho 

O script do PowerShell a seguir pode ser usado para executar um trabalho existente:

Atualize a variável a seguir para refletir o nome do trabalho desejado a ser executado:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Recuperar o estado de uma única execução de trabalho

Use o cmdlet **Get-AzureSqlJobExecution** e defina o parâmetro **JobExecutionId** para exibir o estado de execução do trabalho.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Use o mesmo cmdlet **Get-AzureSqlJobExecution** com o parâmetro **IncludeChildren** para exibir o estado de execuções de trabalhos filho, ou seja, o estado específico de cada execução do trabalho em relação a cada banco de dados a que o trabalho se destina.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Exibir o estado em várias execuções de trabalho

O cmdlet **Get-AzureSqlJobExecution** tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de trabalho, filtradas por meio dos parâmetros fornecidos. O exemplo a seguir demonstra algumas das possíveis maneiras de usar o Get-AzureSqlJobExecution:

Recupere todas as execuções de trabalhos ativos de nível superior:

	Get-AzureSqlJobExecution

Recupere todas as execuções do trabalho de nível superior, incluindo execuções de trabalhos inativos:

	Get-AzureSqlJobExecution -IncludeInactive

Recupere todas as execuções de trabalhos filho de uma ID de execução de trabalho fornecida, incluindo execuções de trabalhos inativos:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recupere todas as execuções de trabalho criadas usando uma combinação de agenda/trabalho, incluindo trabalhos inativos:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recupere todos os trabalhos direcionados a um mapa de fragmentos especificado, incluindo trabalhos inativos:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recupere todos os trabalhos direcionados a uma coleção personalizada especificada, incluindo trabalhos inativos:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recupere a lista de execuções de tarefas de trabalho contidas na execução de um trabalho específico:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Recupere detalhes de execução de tarefa de trabalho:

O script do PowerShell a seguir pode ser usado para exibir os detalhes de uma execução de tarefa de trabalho, o que é especialmente útil ao depurar falhas de execução.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Recuperar falhas em execuções de tarefa de trabalho

O objeto JobTaskExecution inclui uma propriedade Ciclo de Vida da tarefa, junto com uma propriedade Mensagem. Se uma execução de tarefa de trabalho falhar, a propriedade Ciclo de Vida será definida como *Falha* e a propriedade Mensagem será definida como a mensagem de exceção resultante e sua pilha. Se um trabalho não foi bem-sucedido, é importante exibir os detalhes das tarefas de trabalho que não foram bem-sucedidas para um determinado trabalho.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Aguardando a conclusão da execução de um trabalho

O script do PowerShell a seguir pode ser usado para aguardar a conclusão de uma tarefa de trabalho:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Criar uma política de execução personalizada

O recurso trabalhos de Banco de Dados Elástico dá suporte à criação de políticas de execução personalizadas, que podem ser aplicadas ao iniciar trabalhos.
  
Atualmente, as políticas de execução permitem definir:

* Nome: o identificador para a política de execução.
* Tempo Limite do Trabalho: tempo total antes que um trabalho seja cancelado pelo recurso Trabalhos de Banco de Dados Elástico.
* Intervalo de Repetição Inicial: o intervalo de espera antes de primeira repetição de tentativa.
* Intervalo Máximo de Repetição: limite de intervalos de repetição a usar.
* Coeficiente de Retirada de Intervalo de Repetição: coeficiente usado para calcular o próximo intervalo entre as repetições de tentativas. A fórmula a seguir é usada: (Intervalo de Repetição Inicial) * Math.pow((Coeficiente de Retirada do Intervalo), (Número de Novas Tentativas) - 2). 
* Máximo de Tentativas: o número máximo de novas tentativas a repetir em um trabalho.

A política de execução padrão usa os seguintes valores:

* Nome: política de execução padrão
* Tempo Limite do Trabalho: 1 semana
* Intervalo de Repetição Inicial: 100 milissegundos
* Intervalo Máximo de Repetição: 30 minutos
* Coeficiente de Intervalo de Repetição: 2
* Máximo de Tentativas: 2.147.483.647

Crie a política de execução desejada:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Atualizar uma política de execução personalizada

Atualize a política de execução que deseja atualizar:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Cancelar um trabalho

O recurso trabalhos de Banco de Dados Elástico dá suporte a solicitações de cancelamento de trabalhos. Se o recurso trabalhos de Banco de Dados Elástico detecta uma solicitação de cancelamento de um trabalho que está atualmente em execução, ele tenta interromper o trabalho.

Há duas maneiras diferentes pelas quais o recurso Trabalhos de Banco de Dados Elástico pode executar um cancelamento:

1. Cancelando tarefas atualmente em execução: se um cancelamento for detectado enquanto uma tarefa estiver em execução, será realizada uma tentativa de cancelamento no aspecto da tarefa atualmente em execução. Por exemplo: se houver uma consulta de execução longa sendo executada atualmente, quando houver uma tentativa de cancelamento, haverá também uma tentativa de cancelar a consulta.
2. Tentativas de Cancelar Tarefa: se um cancelamento for detectado pelo thread de controle antes de uma tarefa ser iniciada para execução, o thread de controle evitará iniciar a tarefa e declarará a solicitação como cancelada.

Se for solicitado um cancelamento de trabalho para um trabalho pai, a solicitação de cancelamento será atendida para o trabalho pai e todos os seus trabalhos filho.
 
Para enviar uma solicitação de cancelamento, use o cmdlet **Stop-AzureSqlJobExecution** e defina o parâmetro **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Excluir um trabalho por nome e pelo histórico do trabalho

O recurso trabalhos de Banco de Dados Elástico dá suporte à exclusão assíncrona de trabalhos. Um trabalho pode ser marcado para exclusão e o sistema vai excluir o trabalho e todo o seu histórico de trabalho, depois que todas as execuções de trabalho para o trabalho em questão tenham sido concluídas. O sistema não cancelará automaticamente execuções de trabalhos ativos.

Em vez disso, Stop-AzureSqlJobExecution deve ser chamado para cancelar as execuções de trabalhos ativos.

Para disparar a exclusão de trabalho, use o cmdlet **Remove-AzureSqlJob** e defina o parâmetro **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Criar um destino de banco de dados personalizado
Destinos personalizados de banco de dados podem ser definidos no recurso trabalhos de Banco de Dados Elástico, que podem ser usados para execução direta ou para inclusão em um grupo personalizado de bancos de dados. Uma vez que **pools de Banco de Dados Elástico** ainda não têm suporte direto por meio das APIs do PowerShell, basta simplesmente criar um destino de banco de dados personalizado e um destino de coleção de bancos de dados personalizada que englobe todos os bancos de dados no pool.

Defina as variáveis a seguir para refletirem as informações de banco de dados desejadas:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Criar um destino para a coleção de bancos de dados personalizada
Um destino para coleção de bancos de dados personalizada pode ser definido para habilitar a execução de vários destinos de banco de dados definidos. Após a criação de um grupo de banco de dados, bancos de dados podem ser associados ao destino da coleção personalizada.

Defina as variáveis a seguir para refletir a configuração desejada para destino da coleção personalizada:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Adicionar bancos de dados a um destino da coleção de bancos de dados personalizada

Destinos de banco de dados podem ser associados com destino de coleção de bancos de dados personalizada para criar um grupo de bancos de dados. Sempre que é criado um trabalho que tem como alvo um destino de coleção de bancos de dados personalizada, esse trabalho será expandido para bancos de dados associados ao grupo no momento da execução.

Adicione o banco de dados desejado a uma coleção personalizada específica:

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Examinar os bancos de dados contidos em um destino de coleção de bancos de dados personalizada

Use o cmdlet **Get-AzureSqlJobTarget** para recuperar os bancos de dados filho dentro de um destino de coleção de bancos de dados personalizada.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Criar um trabalho para executar um script em um destino de coleção de bancos de dados personalizada

Use o cmdlet **New-AzureSqlJob** para criar um trabalho para um grupo de bancos de dados definidos por um destino de coleção de bancos de dados personalizada. O recurso trabalhos de Banco de Dados Elástico expandirá o trabalho em vários trabalhos filho, cada um correspondendo a um banco de dados associado ao destino de coleção de bancos de dados personalizada e assegurando que o script seja executado em cada banco de dados. Novamente, é importante que os scripts sejam idempotentes para que sejam resistentes em relação a novas tentativas.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Coleta de dados em bancos de dados

O recurso **Trabalhos do Banco de Dados Elástico** dá suporte à execução de uma consulta em um grupo de bancos de dados e envia os resultados a uma tabela do banco de dados especificado. A tabela pode ser consultada após o fato para ver os resultados da consulta provenientes de cada banco de dados. Isso fornece um mecanismo assíncrono para executar uma consulta em vários bancos de dados. Casos de falha - como, por exemplo, um dos bancos de dados estar temporariamente indisponível - são tratados automaticamente por meio de novas tentativas.

A tabela de destino especificada será criada automaticamente se ela ainda não existir correspondendo ao esquema do conjunto de resultados retornado. Se uma execução de script retornar vários conjuntos de resultados, o recurso trabalhos de Banco de Dados Elástico enviará somente o primeiro à tabela de destino fornecida.

O script PowerShell a seguir pode ser usado para executar um script coletando os resultados em uma tabela especificada. Este script presume que foi criado um script T-SQL, que produz um único conjunto de resultados; além disso, um destino de coleção de bancos de dados personalizada foi criado.

Defina o seguinte para refletir o script, credenciais e destino de execução desejados:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### Criar e iniciar um trabalho para cenários de coleta de dados
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Criar um agendamento para execução de trabalho usando um gatilho de trabalho

O script de PowerShell a seguir pode ser usado para criar uma agenda recorrente. Esse script usa um intervalo de minutos, mas o New-AzureSqlJobSchedule também dá suporte aos parâmetros -DayInterval, -HourInterval, -MonthInterval e -WeekInterval. Agendas que são executadas apenas uma vez podem ser criadas pela passagem de -OneTime.

Crie uma nova agenda:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Criar um gatilho de trabalho para que um trabalho seja executado segundo um cronograma

Um gatilho de trabalho pode ser definido para fazer com que um trabalho seja executado segundo um cronograma. O script de PowerShell a seguir pode ser usado para criar um gatilho de trabalho.

Defina as variáveis a seguir para corresponder ao trabalho e agenda desejados:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Remover uma associação agendada para impedir o trabalho de ser executado segundo a agenda

Para interromper a execução do trabalho recorrente por meio de um gatilho de trabalho, esse gatilho pode ser removido. Remova um gatilho de trabalho para impedir que um trabalho seja executado de acordo com um agendamento usando o cmdlet **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName

### Recuperar gatilhos de trabalho associados a um cronograma

O seguinte script PowerShell pode ser usado para obter e exibir os gatilhos de trabalho registrados para um horário de agendamento específico.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Recuperar gatilhos de trabalho associados a um trabalho 

O script do PowerShell a seguir pode ser usado para obter e exibir agendas contendo um determinado trabalho registrado.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Criar uma DACPAC (implantação de aplicativo da camada de dados) para execução em bancos de dados

O recurso trabalhos de Banco de Dados Elástico pode ser usado para implantar um DACPAC (aplicativo da camada de dados) em um grupo de bancos de dados. Para criar um DACPAC, consulte esta documentação. Para o recurso trabalhos de banco de dados elástico implantar um DACPAC em um grupo de bancos de dados, o DACPAC deverá estar acessível para o serviço. É recomendável carregar um DACPAC criado para o armazenamento do Azure e criar um URI assinado para o DACPAC.

O seguinte script PowerShell pode ser usado para inserir um DACPAC em Trabalhos do Banco de Dados Elástico:

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Atualizando uma DACPAC (implantação de aplicativo da camada de dados) para execução em bancos de dados

DACPACs existentes registrados em Trabalhos do Banco de Dados Elástico podem ser atualizados para apontar para os novos URIs. O script de PowerShell a seguir pode ser usado para atualizar o URI do DACPAC em um DACPAC registrado existente:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Criar um trabalho para aplicar uma implantação de aplicativo da camada de dados (DACPAC) em bancos de dados

Após um DACPAC ter sido criado no recurso trabalhos de Banco de Dados Elástico, um trabalho poderá ser criado para aplicar o DACPAC em um grupo de bancos de dados. O seguinte script PowerShell pode ser usado para criar um trabalho DACPAC em uma coleção de bancos de dados personalizada:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=Oct15_HO3-->