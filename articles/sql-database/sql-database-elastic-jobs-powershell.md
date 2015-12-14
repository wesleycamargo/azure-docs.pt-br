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
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# Criar e gerenciar trabalhos de banco de dados elástico de Banco de Dados SQL usando o PowerShell (visualização)

> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



As APIs do PowerShell para o recurso **trabalhos de Banco de Dados Elástico** (em visualização) permitem que você defina um grupo de bancos de dados no qual os scripts serão executados. Este artigo mostra como criar e gerenciar o recurso **trabalhos de Banco de Dados Elástico** usando cmdlets do PowerShell. Consulte [Visão geral dos trabalhos elásticos](sql-database-elastic-jobs-overview.md).

## Pré-requisitos
* Uma assinatura do Azure. Para obter uma avaliação gratuita, confira [Um mês de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
* Um conjunto de bancos de dados criados com as ferramentas do Banco de Dados Elástico. Consulte [Introdução às ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).
* PowerShell do Azure. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).
* Pacote do PowerShell para **trabalhos de Banco de Dados Elástico**: consulte [Instalando trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md)

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
	<p>O recurso trabalhos de banco de dados elástico inclui uma política de execução de trabalho padrão que gera, essencialmente, infinitas repetições de tarefas de trabalho com falha, com retirada exponencial de intervalos entre cada repetição.</p>
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
O trabalho executa os scripts Transact-SQL (T-SQL) ou o aplicativo de DACPACs em um grupo de bancos de dados. Quando um trabalho for enviado para ser executado em um grupo de bancos de dados, o trabalho se “expandirá” em trabalhos filhos, onde cada um deles realizará a execução solicitada em um único banco de dados no grupo.
 
Há dois tipos de grupos que você pode criar:

* Grupo [Mapa de Fragmentos](sql-database-elastic-scale-shard-map-management.md): quando um trabalho é enviado para um mapa de fragmentos, o trabalho consulta o mapa de fragmentos para determinar seu conjunto atual de fragmentos e cria trabalhos filho para cada fragmento no mapa de fragmentos.
* Grupo Coleção Personalizada: um conjunto personalizado definido de bancos de dados. Quando um trabalho tem como alvo uma coleção personalizada, ele cria trabalhos filho para cada banco de dados atualmente na coleção personalizada.

## Para definir a conexão com o recurso trabalhos de Banco de Dados Elástico

Uma conexão deve ser definida para o *banco de dados de controle* dos trabalhos antes de usar as APIs dos trabalhos. Executar esse cmdlet dispara uma janela de credencial para solicitar o nome de usuário e a senha criados durante a instalação do recurso trabalhos de Banco de Dados Elástico. Todos os exemplos fornecidos neste tópico pressupõem que a primeira etapa já foi executada.

Abrir uma conexão ao recurso trabalhos de Banco de Dados Elástico:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Credenciais criptografadas no recurso trabalhos de Banco de Dados Elástico

As credenciais do banco de dados podem ser inseridas no *banco de dados de controle* dos trabalhos com a sua senha criptografada. É necessário armazenar as credenciais para habilitar os trabalhos que serão executados posteriormente (usando planos de trabalho).
 
Criptografia funciona por meio de um certificado criado como parte do script de instalação. O script de instalação cria e carrega o certificado no Serviço de Nuvem do Azure para descriptografia das senhas criptografadas armazenadas. O Serviço de Nuvem do Azure armazena posteriormente a chave pública no *banco de dados de controle* dos trabalhos, o que permite que a interface do Portal Clássico do Azure ou a API do PowerShell criptografe uma senha fornecida sem exigir que o certificado seja instalado localmente.
 
As senhas das credenciais são criptografadas e protegidas contra usuários com acesso somente leitura a objetos do recurso trabalhos de Banco de Dados Elástico. Mas é possível que um usuário mal-intencionado com acesso de leitura/gravação aos objetos do recurso trabalhos de Banco de Dados Elástico extraia uma senha. As credenciais são projetadas para ser reutilizadas em execuções de trabalho. As credenciais são passadas aos bancos de dados de destino durante o estabelecimento de conexões. Atualmente, não existem restrições nos bancos de dados de destino usados para cada credencial. Um usuário mal-intencionado poderia adicionar um destino de banco de dados a um banco de dados sob o controle do usuário mal-intencionado. O usuário poderia em seguida iniciar um trabalho visando esse banco de dados para obter a senha da credencial.

As práticas recomendadas de segurança para o recurso trabalhos de Banco de Dados Elástico incluem:

* Limite o uso das APIs somente a pessoas confiáveis.
* As credenciais devem ter os privilégios mínimos necessários para executar a tarefa de trabalho. Mais informações podem ser vistas dentro desse artigo [Autorização e Permissões](https://msdn.microsoft.com/library/bb669084.aspx) do MSDN do SQL Server.

### Para criar uma credencial criptografada para a execução de trabalhos nos bancos de dados

Para criar uma nova credencial criptografada, o cmdlet [**Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) solicita um nome de usuário e senha que podem ser passados para o cmdlet [**New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx).

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Para atualizar as credenciais

Quando alterar as senhas, use o cmdlet [**Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) e defina o parâmetro **CredentialName**

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Para definir um destino para o mapa de fragmentos de Banco de Dados Elástico

Para executar um trabalho em todos os bancos de dados em um conjunto de fragmentos (criado usando a [biblioteca do cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md)) use um mapa de fragmentos como destino para o bancos de dados. Este exemplo requer que você crie um aplicativo fragmentado usando a biblioteca do cliente de Banco de Dados Elástico. Consulte [Introdução ao exemplo de ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).

###Criar um gerenciador de mapa de fragmentos usando o aplicativo de exemplo

Este exemplo cria um gerenciador de mapas de fragmentos juntamente com vários fragmentos, seguido pela inserção de dados nos fragmentos.

1. Compile e execute o aplicativo de exemplo da **Introdução às ferramentas de Banco de Dados Elástico**. Siga as etapas até a etapa 7 na seção [Baixar e executar o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). No final da etapa 7, você verá o seguinte prompt de comando:

	![prompt de comando][1]

2.  Na janela de comando, digite "1" e pressione **Enter**. Isso cria o gerenciador de mapa de fragmentos e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e pressione **Enter**. Repita a ação quatro vezes. Isso insere linhas de dados de exemplo no seus fragmentos.
  
3.  O [portal do Azure](https://portal.azure.com) deve mostrar três novos bancos de dados em seu servidor v12:

	![Confirmação do Visual Studio][2]

Crie um destino para o mapa de fragmentos usando o cmdlet [**New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx). O banco de dados do gerenciador do mapa de fragmentos deve ser definido como um destino de banco de dados e, em seguida, o mapa de fragmentos específico deve ser especificado como um destino.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Criar um script T-SQL para execução em bancos de dados

Ao criar scripts T-SQL para execução, é altamente recomendável criá-los para que sejam [idempotentes](https://en.wikipedia.org/wiki/Idempotence) e resistentes contra falhas. O recurso trabalhos de Banco de Dados Elástico tentará novamente a execução de um script sempre que ocorrer uma falha nessa execução, independentemente da classificação da falha.

Use o cmdlet [**New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx) para criar e salvar um script para execução e defina os parâmetros **-ContentName** e **-CommandText**.

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
Se o script T-SQL é definido dentro de um arquivo, use-o para importar o script:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Para atualizar um script T-SQL para execução em bancos de dados  

Esse script de PowerShell atualiza o texto do comando T-SQL para um script existente.

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

### Para atualizar a definição para um script existente

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## Para criar um trabalho para executar um script em um mapa de fragmentos

Esse script de PowerShell inicia um trabalho para execução de um script em cada fragmento de um mapa de fragmentos de Escala Elástica.

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

## Para executar um trabalho 

Esse script de PowerShell executa um trabalho existente:

Atualize a variável a seguir para refletir o nome do trabalho desejado a ser executado:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Para recuperar o estado de uma única execução de trabalho

Use o cmdlet [**Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) e defina o parâmetro **JobExecutionId** para exibir o estado de execução do trabalho.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Use o mesmo cmdlet **Get-AzureSqlJobExecution** com o parâmetro **IncludeChildren** para exibir o estado de execuções de trabalhos filho, ou seja, o estado específico de cada execução do trabalho em relação a cada banco de dados a que o trabalho se destina.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Para exibir o estado em várias execuções de trabalho

O cmdlet [**Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de trabalho, filtradas por meio dos parâmetros fornecidos. O exemplo a seguir demonstra algumas das possíveis maneiras de usar o Get-AzureSqlJobExecution:

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

## Para recuperar falhas em execuções de tarefa de trabalho

O objeto **JobTaskExecution** inclui uma propriedade para o ciclo de vida da tarefa, junto com uma propriedade de mensagem. Se uma execução de tarefa de trabalho falhar, a propriedade de ciclo de vida será definida como *Falha*, e a propriedade de mensagem será definida como a mensagem de exceção resultante e sua pilha. Se um trabalho não foi bem-sucedido, é importante exibir os detalhes das tarefas de trabalho que não foram bem-sucedidas para um determinado trabalho.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Para aguardar a conclusão da execução de um trabalho

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
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
	-RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Cancelar tarefas atualmente em execução: se um cancelamento for detectado enquanto uma tarefa estiver em execução, será realizada uma tentativa de cancelamento no aspecto da tarefa atualmente em execução. Por exemplo: se houver uma consulta de execução longa sendo executada atualmente, quando houver uma tentativa de cancelamento, haverá também uma tentativa de cancelar a consulta.
2. Tentativas de cancelar tarefa: se um cancelamento for detectado pelo thread de controle antes de uma tarefa ser iniciada para execução, o thread de controle evitará iniciar a tarefa e declarará a solicitação como cancelada.

Se for solicitado um cancelamento de trabalho para um trabalho pai, a solicitação de cancelamento será atendida para o trabalho pai e todos os seus trabalhos filho.
 
Para enviar uma solicitação de cancelamento, use o cmdlet [**Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) e defina o parâmetro **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Para excluir um trabalho e o histórico do trabalho de forma assíncrona

O recurso trabalhos de Banco de Dados Elástico dá suporte à exclusão assíncrona de trabalhos. Um trabalho pode ser marcado para exclusão e o sistema vai excluir o trabalho e todo o seu histórico de trabalho, depois que todas as execuções de trabalho para o trabalho em questão tenham sido concluídas. O sistema não cancelará automaticamente execuções de trabalhos ativos.

Invoque [**Stop AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) para cancelar as execuções do trabalho ativo.

Para disparar a exclusão de trabalho, use o cmdlet [**Remove-AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) e defina o parâmetro **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Para criar um destino de banco de dados personalizado

Você pode definir os destinos de banco de dados personalizado para execução direta ou para inclusão em um grupo de bancos de dados personalizado. Por exemplo, como os **pools de Banco de Dados Elástico** ainda não têm suporte direto usando as APIs do PowerShell, você pode criar um destino de banco de dados personalizado e um destino de coleção de bancos de dados personalizada que englobe todos os bancos de dados no pool.

Defina as variáveis a seguir para refletirem as informações de banco de dados desejadas:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Para criar um destino para a coleção de bancos de dados personalizada

Use o cmdlet [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para definir um destino de coleção de banco de dados personalizada para habilitar a execução em vários destinos de banco de dados definidos. Após criar um grupo de banco de dados,os bancos de dados podem ser associados ao destino da coleção personalizada.

Defina as variáveis a seguir para refletir a configuração desejada para destino da coleção personalizada:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Para adicionar bancos de dados a um destino da coleção de bancos de dados personalizada

Para adicionar um banco de dados a uma coleção personalizada específica, use o cmdlet [**Add-AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx).

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Examinar os bancos de dados contidos em um destino de coleção de bancos de dados personalizada

Use o cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para recuperar os bancos de dados filho dentro de um destino de coleção de bancos de dados personalizada.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Criar um trabalho para executar um script em um destino de coleção de bancos de dados personalizada

Use o cmdlet [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) para criar um trabalho para um grupo de bancos de dados definidos por um destino de coleção de bancos de dados personalizada. O recurso trabalhos de Banco de Dados Elástico expandirá o trabalho em vários trabalhos filho, cada um correspondendo a um banco de dados associado ao destino de coleção de bancos de dados personalizada e assegurando que o script seja executado em cada banco de dados. Novamente, é importante que os scripts sejam idempotentes para que sejam resistentes em relação a novas tentativas.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Coleta de dados em bancos de dados

Você pode usar um trabalho para executar uma consulta em um grupo de bancos de dados e enviar os resultados para uma tabela específica. A tabela pode ser consultada após o fato para ver os resultados da consulta provenientes de cada banco de dados. Isso fornece um método assíncrono para executar uma consulta em vários bancos de dados. Tentativas fracassadas são processadas automaticamente por meio de novas tentativas.

A tabela de destino especificada será criada automaticamente se ainda não existir. A nova tabela coincide com o esquema do conjunto de resultados retornado. Se um script retornar vários conjuntos de resultados, o recurso trabalhos de Banco de Dados Elástico enviará somente o primeiro à tabela de destino.

O script de PowerShell a seguir executa um script e coleta os resultados em uma tabela especificada. Esse script presume que foi criado um script T-SQL, que produz um único conjunto de resultados, e que um destino de coleção de bancos de dados personalizada foi criado.

Esse script usa o cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx). Defina os parâmetros para script, credenciais e destino de execução:

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

### Para criar e iniciar um trabalho para cenários de coleta de dados

Esse script usa o cmdlet [**Start-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx).
 
	$job = New-AzureSqlJob -JobName $jobName 
	-CredentialName $executionCredentialName 
	-ContentName $scriptName 
	-ResultSetDestinationServerName $destinationServerName 
	-ResultSetDestinationDatabaseName $destinationDatabaseName 
	-ResultSetDestinationSchemaName $destinationSchemaName 
	-ResultSetDestinationTableName $destinationTableName 
	-ResultSetDestinationCredentialName $destinationCredentialName 
	-TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Para agendar um gatilho de execução de trabalho

O script de PowerShell a seguir pode ser usado para criar uma agenda recorrente. Esse script usa um intervalo de minutos, mas o [**New-AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) também dá suporte aos parâmetros -DayInterval, -HourInterval, -MonthInterval e -WeekInterval. Agendas que são executadas apenas uma vez podem ser criadas pela passagem de -OneTime.

Crie uma nova agenda:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
	-ScheduleName $scheduleName 
	-StartTime $startTime 
	Write-Output $schedule

### Para disparar um trabalho executado em um cronograma

Um gatilho de trabalho pode ser definido para fazer com que um trabalho seja executado segundo um cronograma. O script de PowerShell a seguir pode ser usado para criar um gatilho de trabalho.

Use o [New-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) e defina as variáveis a seguir para corresponder ao trabalho e à agenda desejados:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger
	-ScheduleName $scheduleName
	–JobName $jobName
	Write-Output $jobTrigger

### Para remover uma associação agendada para impedir o trabalho de ser executado segundo a agenda

Para interromper a execução do trabalho recorrente por meio de um gatilho de trabalho, esse gatilho pode ser removido. Remova um gatilho de trabalho para impedir que um trabalho seja executado de acordo com um agendamento usando o cmdlet [**Remove-AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx).

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger 
	-ScheduleName $scheduleName 
	-JobName $jobName

### Recuperar gatilhos de trabalho associados a um cronograma

O seguinte script PowerShell pode ser usado para obter e exibir os gatilhos de trabalho registrados para um horário de agendamento específico.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Para recuperar gatilhos de trabalho associados a um trabalho 

Use o [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) para obter e exibir agendas que contenham um trabalho registrado.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Para criar um DACPAC (aplicativo da camada de dados) para execução em bancos de dados

Para criar um DACPAC, consulte [Aplicativos de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx). Para implantar um DACPAC, use o cmdlet [New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). O DACPAC deve ser acessado pelo serviço. É recomendável carregar um DACPAC criado para o Armazenamento do Azure e criar uma [Assinatura de Acesso Compartilhado](storage-dotnet-shared-access-signature-part-1.md) para o DACPAC.

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Para atualizar um DACPAC (aplicativo da camada de dados) para execução em bancos de dados

DACPACs existentes registrados em Trabalhos do Banco de Dados Elástico podem ser atualizados para apontar para os novos URIs. Use o cmdlet [**Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) para atualizar o URI do DACPAC em um DACPAC existente registrado:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Para criar um trabalho para aplicar um DACPAC (aplicativo da camada de dados) em bancos de dados

Após um DACPAC ter sido criado no recurso trabalhos de Banco de Dados Elástico, um trabalho poderá ser criado para aplicar o DACPAC em um grupo de bancos de dados. O seguinte script PowerShell pode ser usado para criar um trabalho DACPAC em uma coleção de bancos de dados personalizada:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget 
	-CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob 
	-JobName $jobName 
	-CredentialName $credentialName 
	-ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=AcomDC_1203_2015-->