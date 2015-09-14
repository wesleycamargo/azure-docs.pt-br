<properties 
    pageTitle="Criar um Banco de Dados SQL do Azure usando o PowerShell"
	description="Criar um Banco de Dados SQL do Azure usando o PowerShell"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
    ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="data-management"
	ms.date="09/01/2015"
	ms.author="sstein"/>

# Criar um Banco de Dados SQL usando o PowerShell

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## Visão geral

Este artigo mostra como criar um Banco de Dados SQL usando o PowerShell.


Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- PowerShell do Azure. Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

Os cmdlets para criação e gerenciamento de Bancos de Dados SQL do Azure estão localizados no módulo do Gerenciador de Recursos do Azure. Quando você inicia o PowerShell do Azure, os cmdlets no módulo do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Se você executar o **Switch-AzureMode** e o aviso: O *cmdlet Switch-AzureMode foi substituído e será removido em uma versão futura* for exibido, não tem problema; basta passar para a próxima etapa e configurar suas credenciais.

Para obter informações detalhadas, confira [Usando o Windows PowerShell com o Gerenciador de Recursos](powershell-azure-resource-manager.md).


## Configurar suas credenciais e selecionar sua assinatura

Agora, executando o módulo Gerenciador de Recursos do Azure, você terá acesso a todos os cmdlets necessários para criar um banco de dados SQL.

Primeiramente, você deve estabelecer acesso à sua conta do Azure, de modo que ao executar o cmdlet a seguir seja exibida uma tela de conexão para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	Add-AzureAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, você precisa da ID da assinatura. Você pode copiar essas informações da etapa anterior, ou, se tiver várias assinaturas, poderá executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultado. Quando tiver sua assinatura, execute o seguinte cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureSubscription** com êxito, você retornará ao prompt do PowerShell. Se tiver mais de uma assinatura, você poderá executar **Get-AzureSubscription** e verificar se a assinatura que deseja usar mostra **IsCurrent: True**.

## Criar um grupo de recursos, servidor e regra de firewall

Agora que você tem acesso para executar cmdlets em sua assinatura do Azure selecionada, a próxima etapa é estabelecer o grupo de recursos que contém o servidor no qual o banco de dados será criado. Você pode editar o próximo comando a fim de usar qualquer local válido de sua escolha. Execute **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** para obter uma lista de locais válidos.

Execute o seguinte comando para criar um novo grupo de recursos:

	New-AzureResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Depois de criar com êxito o novo grupo de recursos, você verá informações na tela que incluem **ProvisioningState : Succeeded**.


### Criar um servidor 

Os Bancos de Dados SQL são criados dentro dos servidores de Banco de Dados SQL. Execute **New-AzureSqlServer** para criar um novo servidor. Substitua ServerName pelo nome do servidor. Esse nome deve ser exclusivo a todos os Servidores SQL do Azure, de modo que você receberá um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Você pode editar o comando para usar qualquer local válido escolhido, mas deve usar o mesmo local que usou para o grupo de recursos criado na etapa anterior.

	New-AzureSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Ao executar esse comando, uma janela é aberta solicitando um **Nome de usuário** e **Senha**. Essas não são suas credenciais do Azure, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.

Os detalhes do servidor são exibidos após a criação bem-sucedida do servidor.

### Configurar uma regra de firewall de servidor para permitir o acesso ao servidor

Estabeleça uma regra de firewall para acessar o servidor. Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu computador.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes da regra de firewall são exibidos após a criação bem-sucedida da regra.

Para permitir que outros serviços do Azure acessem o servidor, adicione uma regra de firewall e defina StartIpAddress e EndIpAddress como 0.0.0.0. Observe que isso permite que o tráfego do Azure de qualquer assinatura do Azure acesse o servidor.

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Criar um banco de dados SQL

Agora que você tem um grupo de recursos, um servidor e uma regra de firewall configurados, é possível acessar o servidor.

O comando a seguir cria um novo (em branco) banco de dados SQL na camada de serviço Standard com um nível de desempenho S1:


	New-AzureSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes do banco de dados são exibidos após a criação bem-sucedida do banco de dados.

## Criar um Banco de Dados SQL com script do PowerShell

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Próximas etapas

- [Conectar-se ao SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)


## Recursos adicionais

- [Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=September15_HO1-->