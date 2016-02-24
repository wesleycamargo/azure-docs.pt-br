<properties 
	pageTitle="Código do Arquivo de Evento de XEvent para o Banco de Dados SQL | Microsoft Azure" 
	description="Fornece PowerShell e Transact-SQL para um exemplo de código de duas fases que demonstra o destino de Arquivo de evento em um evento estendido no Banco de Dados SQL do Azure. O Armazenamento do Azure é uma parte obrigatória deste cenário." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2015" 
	ms.author="genemi"/>


# Código de destino do Arquivo de evento para eventos estendidos no Banco de Dados SQL


Você deseja um exemplo de código completo para uma maneira robusta de capturar e relatar informações para um evento estendido.


No Microsoft SQL Server, o [destino de Arquivo de Evento](http://msdn.microsoft.com/library/ff878115.aspx) é usado para armazenar as saídas de eventos em um arquivo do disco rígido local. Porém, esses arquivos não estão disponíveis para o Banco de Dados SQL do Azure. Em vez disso, usamos o serviço de Armazenamento do Azure para oferecer suporte ao destino de Arquivo de evento.


Este tópico apresenta um exemplo de código em duas fases:


- PowerShell, a fim de criar o contêiner de Armazenamento do Azure na nuvem.

- Transact-SQL:
 - Para atribuir o contêiner de Armazenamento do Azure a um destino de Arquivo de evento.
 - Para criar e iniciar a sessão de evento etc.


## Pré-requisitos


- Uma conta e uma assinatura do Azure. Você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Qualquer banco de dados no qual você possa criar uma tabela.
 - Como alternativa, você pode [criar um banco de dados de demonstração do **AdventureWorksLT**](sql-database-get-started.md) em questão minutos.


- SQL Server Management Studio (ssms.exe), sua Visualização de agosto de 2015 ou uma versão posterior. Você pode baixar o ssms.exe mais recente de:
 - Tópico [Baixar o SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Um link direto para o download.](http://go.microsoft.com/fwlink/?linkid=616025)
 - A Microsoft recomenda que você atualize seu ssms.exe periodicamente. Em alguns casos, o ssms.exe será atualizado mensalmente.


- Você deve ter os [módulos do Azure PowerShell](http://go.microsoft.com/?linkid=9811175) instalados.
 - Os módulos fornecem comandos como: **New-AzureStorageAccount**.


## Fase 1: código do PowerShell para o contêiner de Armazenamento do Azure


Esse PowerShell é a fase 1 do exemplo de código de duas fases.

O script começa com comandos para limpeza após uma possível execução anterior, e foi projetado para ser reutilizável.



1. Cole o script do PowerShell em um editor de texto simples, como o Notepad.exe, e salve o script como um arquivo com a extensão **.ps1**.

2. Inicie o ISE do PowerShell como Administrador.

3. No prompt, digite<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e pressione Enter.

4. No ISE do PowerShell, abra seu arquivo **.ps1**. Execute o script.

5. Primeiro, o script inicia uma nova janela para efetuar logon no Azure.
 - Se você executar novamente o script sem interromper a sessão, terá a opção conveniente de comentar o comando **Add-AzureAccount**.


![ISE do PowerShell, com o módulo do Azure instalado e pronto para executar o script.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

$subscriptionName       = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken      = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean-up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Anote os valores nomeados que o script do PowerShell imprime quando termina. Você deve editar esses valores no script Transact-SQL na fase 2.


## Fase 2: código Transact-SQL que usa o contêiner de Armazenamento do Azure


- Na fase 1 deste exemplo de código, você executou um script PowerShell para criar um contêiner de Armazenamento do Azure.
- Em seguida na fase 2, o script Transact-SQL a seguir deverá usar o contêiner.


O script começa com comandos para limpeza após uma possível execução anterior, e foi projetado para ser reutilizável.


O script PowerShell imprimiu alguns valores nomeados quando terminou. Você precisa editar o script Transact-SQL a fim de usar esses valores. Localize **TODO** no script Transact-SQL para localizar os pontos de edição.


1. Abra o SQL Server Management Studio (ssms.exe).

2. Conecte-se ao seu Banco de Dados SQL do Azure.

3. Clique para abrir um novo painel de consulta.

4. Cole o script Transact-SQL a seguir no painel de consulta.

5. Localize todos os **TODO** no script e faça as edições apropriadas.

6. Salve e execute o script.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;

GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
	DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
	(SELECT * FROM sys.symmetric_keys
		WHERE symmetric_key_id = 101)
BEGIN
	CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
	(SELECT * FROM sys.database_scoped_credentials
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
	DROP DATABASE SCOPED CREDENTIAL
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
	DATABASE SCOPED
	CREDENTIAL
		-- use '.blob.',   and not '.queue.' or '.table.' etc.
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	WITH
		IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
		-- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
		SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
	;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'gmeventsessionname240b')
BEGIN
	DROP
		EVENT SESSION
			gmeventsessionname240b
	    ON DATABASE;
END
GO


CREATE
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE

	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE 'UPDATE gmTabEmployee%'
			)
	ADD TARGET
		package0.event_file
			(
			-- TODO: Assign AzureStorageAccount name, and the associated Container name.
			-- Also, tweak the .xel file name at end, if you like.
			SET filename =
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
			)
	WITH
		(MAX_MEMORY = 10 MB,
		MAX_DISPATCH_LATENCY = 3 SECONDS)
	;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 2
	WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 13
	WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
		*, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
		CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
	FROM
		sys.fn_xe_file_target_read_file
			(
				-- TODO: Fill in Storage Account name, and the associated Container name.
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
				null, null, null
			);
GO


-------------- Step 6.  Clean up. ----------

DROP
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
	-- TODO: Assign AzureStorageAccount name, and the associated Container name.
	[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Se o destino não for anexado durante a execução, você deverá parar e reiniciar a sessão de eventos:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## Saída


Após a conclusão do script Transact-SQL, clique em uma célula sob o cabeçalho da coluna **event\_data\_XML**. Um elemento **<event>** é exibido mostrando uma instrução UPDATE.

Veja um elemento **<event>** que foi gerado durante o teste:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```





&nbsp;


## Convertendo o exemplo de código para execução no SQL Server


Vamos supor que você queira executar o exemplo anterior de Transact-SQL no Microsoft SQL Server.


- Para manter a simplicidade, convém substituir completamente o uso do contêiner de Armazenamento do Azure por um arquivo simples, como **C:\\myeventdata.xel**. O arquivo seria gravado no disco rígido local do computador que hospeda o SQL Server.


- Você não precisaria de qualquer tipo de instrução Transact-SQL para **CREATE MASTER KEY** e **CREATE CREDENTIAL**.


- Na instrução **CREATE EVENT SESSION**, em sua cláusula **ADD TARGET**, você pode substituir o valor de Http atribuído a **filename =** por uma cadeia de caracteres de caminho completo, como **C:\\myfile.xel**.
 - Não é necessário envolver qualquer conta de Armazenamento do Azure.


## Mais informações


O tópico principal para eventos estendidos no Banco de Dados SQL do Azure é:

- [Eventos estendidos no Banco de Dados SQL](sql-database-xevent-db-diff-from-svr.md): é o tópico principal para eventos estendidos no Banco de Dados SQL do Azure é.
 - Ele compara aspectos dos eventos estendidos que diferem entre o Banco de Dados SQL do Azure e o Microsoft SQL Server.


- [Código de destino do Buffer de Anéis para eventos estendidos no Banco de Dados SQL](sql-database-xevent-code-ring-buffer.md): fornece um exemplo de código irmão rápido e fácil, mais adequado testes rápidos e menos robusto para atividades de grande porte.


Para saber mais sobre contas e contêineres no serviço de Armazenamento do Azure, consulte:

- [Como usar o Armazenamento de blob do .NET](storage-dotnet-how-to-use-blobs.md/)
- [Nomeando e referenciando contêineres, blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Trabalhando com o contêiner raiz](http://msdn.microsoft.com/library/azure/ee395424.aspx)


<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

<!---HONumber=AcomDC_0128_2016-->