<properties 
	pageTitle="Gerenciar recursos de banco de dados SQL do Azure com PowerShell" 
	description="Gerenciamento do Banco de Dados SQL do Azure com o PowerShell." 
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
	ms.date="07/01/2015" 
	ms.author="vinsonyu"/>

# Gerenciar recursos de banco de dados SQL do Azure com PowerShell


Este tópico fornece comandos do PowerShell para executar muitas tarefas do Banco de Dados SQL do Azure usando os cmdlets do Gerenciador de Recursos do Azure.


## Pré-requisitos

Para executar cmdlets do PowerShell, você precisa ter o PowerShell do Azure instalado e em execução e, dependendo da versão, talvez seja preciso alterná-lo para o modo de gerenciador de recursos a fim de acessar os cmdlets do PowerShell do Gerenciador de Recursos do Azure.

Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

Os cmdlets para criação e gerenciamento de Bancos de Dados SQL do Azure estão localizados no módulo do Gerenciador de Recursos do Azure. Quando você inicia o PowerShell do Azure, os cmdlets no módulo do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet **Switch-AzureMode**.

	PS C:>Switch-AzureMode -Name AzureResourceManager

Se for exibido um aviso informando “O cmdlet Switch-AzureMode foi substituído e será removido em uma versão futura”, você poderá ignorá-lo e passar para a próxima seção.

Para obter informações detalhadas, confira [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).



## Configurar credenciais

Para executar os cmdlets do PowerShell em sua assinatura do Azure, primeiramente você deve estabelecer acesso à sua conta do Azure. Execute o seguinte e você receberá um sinal na tela para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	PS C:>Add-AzureAccount

Após entrar, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


## Selecionar sua assinatura do Azure

Para selecionar a assinatura com a qual deseja trabalhar, você precisa da ID (**-SubscriptionId**) ou do nome da assinatura (**-SubscriptionName**). Você pode copiar essas informações da etapa anterior, ou, se tiver várias assinaturas, poderá executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultado.

Execute o cmdlet a seguir com as informações da sua assinatura para definir a assinatura atual:

	PS C:>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Os comandos a seguir serão executados na assinatura que você acabou de selecionar acima.

## Criar um grupos de recursos

Crie um grupo de recursos que conterá o servidor. Você pode editar o próximo comando para usar qualquer local válido.

Para obter uma lista de locais válidos de servidor do Banco de Dados SQL do Azure, execute os seguintes cmdlets:

		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

Caso já tenha um grupo de recursos, você poderá pular para a criação de um servidor, ou poderá editar e executar o seguinte comando para criar um novo grupo de recursos:

	PS C:>New-AzureResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Criar um servidor 

Para criar um novo servidor V12, use o comando [New-AzureSqlServer](https://msdn.microsoft.com/library/mt163526.aspx). Substitua server12 pelo nome do seu servidor. Esse nome deve ser exclusivo para Servidores do SQL do Azure. Você poderá receber um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Os detalhes do servidor e o prompt do PowerShell serão exibidos após a criação do servidor. Você pode editar o comando para usar qualquer local válido.

	PS C:>New-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Ao executar esse comando, uma janela é aberta solicitando um **Nome de usuário** e **Senha**. Essas não são suas credenciais do Azure, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.

## Criar uma regra de firewall de servidor

Para criar uma regra de firewall para acessar o servidor, use o comando [New-AzureSqlServerFirewallRule](https://msdn.microsoft.com/library/mt125953.aspx). Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu cliente.

Se o servidor precisar permitir o acesso a outros serviços do Azure, adicione a opção **- AllowAllAzureIPs** que adicionará uma regra de firewall especial e permitirá que todo o tráfego do azure acesse o servidor.

	PS C:>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Criar um banco de dados SQL

Para criar um banco de dados, use o comando [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125915.aspx). É necessário um servidor para criar um banco de dados. O exemplo a seguir cria um banco de dados SQL denominado TestDB12. O banco de dados é criado como um banco de dados Padrão S1.

	PS C:>New-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Alterar o nível de desempenho do banco de dados SQL

Você pode escalar o banco de dados verticalmente com o comando [Set-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125814.aspx). O exemplo a seguir escala verticalmente um banco de dados SQL denominado TestDB12 de seu nível de desempenho atual para um nível Padrão S3.

	PS C:>Set-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Excluir um banco de dados SQL

Você pode excluir um banco de dados SQL com o comando [Remove-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125977.aspx). O exemplo a seguir exclui um banco de dados SQL denominado TestDB12.

	PS C:>Remove-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Excluir um servidor

Também é possível excluir um servidor com o comando [Remove-AzureSqlServer](https://msdn.microsoft.com/library/mt125891.aspx). O exemplo a seguir exclui um servidor chamado server12.

	PS C:>Remove-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Se você estiver criando novamente esses recursos do SQL do Azure ou semelhantes, é possível:

- Salvar como um arquivo de script do PowerShell (*. ps1)
- Salvar como um runbook de automação do Azure, na seção Automação do Portal de Gerenciamento do Azure 

## Próximas etapas

Combinar comandos e automatizar. Por exemplo, substitua tudo que está entre aspas, incluindo os caracteres < and > pelos seus valores para criar um servidor, uma regra de firewall e um banco de dados:


    PS C:>New-AzureResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    PS C:>New-AzureSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    PS C:>New-AzureSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    PS C:>New-AzureSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Informações relacionadas

- [Cmdlets do Gerenciador de Recursos do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Cmdlets do Gerenciamento de Serviço do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=July15_HO4-->