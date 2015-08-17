## Configurando o PowerShell para modelos do Gerenciador de recursos
 
Antes de poder usar o Azure PowerShell com o Gerenciador de Recursos, você precisará ter as versões corretas do Windows PowerShell e do Azure PowerShell.

### Verificar as versões do PowerShell

Verifique se você tem o Windows PowerShell versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite este comando no prompt de comando do Windows PowerShell.

	$PSVersionTable

Você receberá o seguinte tipo de informações:

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Caso não seja, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Você também deve ter o Azure PowerShell versão 0.9.0 ou posterior. Se você não tiver instalado e configurado o Azure PowerShell, clique [aqui](powershell-install-configure.md) para obter instruções.

Você pode verificar a versão do Azure PowerShell instalada com o comando este comando no prompt de comando do Azure PowerShell.

	Get-Module azure | format-table version

Você receberá o seguinte tipo de informações:

	Version
	-------
	0.9.0

Se você não tiver a 0.9.0 ou posterior, deve remover o Azure PowerShell usando o Painel de Controle de Programas e Recursos e, em seguida, instalar a versão mais recente. Consulte [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](powershell-install-configure.md) para obter mais informações.

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

### Alternar para o módulo Gerenciador de Recursos do Azure

Para usar o módulo Gerenciador de Recursos do Azure, você precisa alternar do conjunto de comandos padrão do Azure para o conjunto de comandos do Gerenciador de Recursos do Azure. Execute este comando.

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]Você pode alternar de volta para o conjunto de comandos padrão com o comando **Switch-AzureMode AzureServiceManagement**.

<!---HONumber=August15_HO6-->