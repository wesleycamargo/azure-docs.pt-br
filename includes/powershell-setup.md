
## Configurando o PowerShell

Antes de poder usar o PowerShell do Azure, siga estas etapas.

### Verificar as versões do PowerShell

Para poder usar o Windows PowerShell, você deverá ter o Windows PowerShell Versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite este comando no prompt de comando do Windows PowerShell.

	$PSVersionTable

Você deverá ver algo assim.

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2

Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Para instalar uma versão compatível, consulte [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Você também deverá ter o PowerShell do Azure versão 0.8.0 ou posterior. Você pode verificar a versão do Azure PowerShell instalada com o comando este comando no prompt de comando do Azure PowerShell.

	Get-Module azure | format-table version

Você deverá ver algo assim.

	Version
	-------
	0.8.16.1

Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).


### Definir sua conta e assinatura do Azure

Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

Abra um prompt de comando do Azure PowerShell e faça logon no Azure com este comando.

	Add-AzureAccount

Se você tiver várias assinaturas do Azure, você pode listar suas assinaturas do Azure com este comando.

	Get-AzureSubscription

Você receberá o seguinte tipo de informações:

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Você pode definir a assinatura do Azure atual ao executar estes comandos no prompt de comando do Azure PowerShell. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelo nome correto.

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

Para obter mais informações sobre contas e assinaturas do Azure, consulte [How to: Connect to your subscription (Como: conectar-se à sua assinatura)](powershell-install-configure.md#Connect).

<!---HONumber=July15_HO2-->