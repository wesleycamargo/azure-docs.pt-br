
### <a name="start-your-powershell-session"></a>Iniciar sua sessão do PowerShell
Primeiro, você deve ter o [Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx) mais recente instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Muitos recursos novos do Banco de Dados SQL só terão suporte quando você estiver usando o [modelo de implantação do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), portanto, os exemplos usam os [cmdlets do PowerShell do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) para o Resource Manager. Os [cmdlets de gerenciamento do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) do modelo de implantação de gerenciamento (clássico) de serviço têm suporte para compatibilidade com versões anteriores, mas recomendamos que você use os cmdlets do Resource Manager.
> 
> 

Execute o cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) e você verá uma tela de entrada para inserir suas credenciais. Use as mesmas credenciais que você usa para entrar no Portal do Azure.

    Add-AzureRmAccount

Se você tiver várias assinaturas, use o cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) para selecionar qual assinatura sua sessão do PowerShell deve usar. Para ver qual assinatura a sessão atual do PowerShell está usando, execute [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). Para ver todas as suas assinaturas, execute [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
