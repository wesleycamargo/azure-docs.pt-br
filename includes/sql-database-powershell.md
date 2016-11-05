
## Iniciar sua sessão do PowerShell
Primeiro, você precisa ter o [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) mais recente instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../articles/powershell-install-configure.md).

> [!NOTE]
> Muitos recursos novos do Banco de Dados SQL só terão suporte quando você estiver usando o [modelo de implantação do Azure Resource Manager](../articles/resource-group-overview.md), portanto, os exemplos usam os [cmdlets do PowerShell do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) para oResource Manager. Os cmdlets do Banco de Dados SQL do Azure (clássico) do [modelo de implantação clássico existente](https://msdn.microsoft.com/library/azure/dn546723.aspx) têm suporte para a compatibilidade com versões anteriores, mas é recomendável usar os cmdlets do Resource Manager.
> 
> 

Execute o cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) e você verá uma tela de entrada para inserir suas credenciais. Use as mesmas credenciais que você usa para entrar no Portal do Azure.

    Add-AzureRmAccount

Se você tiver várias assinaturas, use o cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) para selecionar qual assinatura sua sessão do PowerShell deve usar. Para ver qual assinatura a sessão atual do PowerShell está usando, execute [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). Para ver todas as suas assinaturas, execute [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0803_2016-->