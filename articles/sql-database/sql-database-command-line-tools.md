<properties 
	pageTitle="Gerenciar recursos de banco de dados SQL do Azure com PowerShell" 
	description="Gerenciar banco de dados do SQL do Azure com linha de comando" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# Gerenciar recursos de banco de dados SQL do Azure com PowerShell


Neste tópico, você usa um script do PowerShell para criar um servidor lógico do banco de dados SQL do Azure, um banco de dados e uma regra de firewall.

## Etapa 1: instalar o SDK do Azure

Se ainda não fez isso, use as instruções em [Como instalar e configurar o PowerShell do Azure para instalar o PowerShell do Azure](../powershell-install-configure.md) em seu computador local. Em seguida, abra um prompt de comando do PowerShell do Azure.


## Etapa 2: configurar os Scripts do PowerShell
Este script do PowerShell cria um servidor, um banco de dados e uma regra de firewall do servidor.


1. Copie o seguinte bloco de cmdlets do PowerShell abaixo em seu editor de texto.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. Substitua tudo o que estiver entre < > pelos valores desejados. Para obter uma lista de locais de servidor de banco de dados SQL do Azure válidos, você pode executar os seguintes cmdlets em seu prompt de comando do Powershell do Azure

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##Etapa 3: executar o script do PowerShell

Analise os cmdlets do PowerShell do Azure elaborados.

Copie os cmdlets do PowerShell configurados na etapa 2 e cole-os no prompt de comando do PowerShell do Azure. Isso emitirá os cmdlets como uma série de comandos do PowerShell e criará seu servidor SQL do Azure, o banco de dados e a regra de firewall do servidor.

Se você estiver criando novamente esses recursos do SQL do  Azure ou semelhantes, é possível:

- Salvar como um arquivo de script do PowerShell (*. ps1)
- Salvar como um runbook de automação do Azure, na seção Automação do Portal de Gerenciamento do Azure 

##Exemplos

Esse script do PowerShell criará os recursos no Oeste dos EUA

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##Recursos

Para obter mais informações sobre os Cmdlets do PowerShell do SQL do Azure, [clique aqui](https://msdn.microsoft.com/library/dn546726.aspx)

<!---HONumber=58--> 