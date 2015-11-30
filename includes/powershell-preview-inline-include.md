Este tópico inclui exemplos que usam os cmdlets do Azure PowerShell. O Azure PowerShell está disponível em duas versões - 1.0 e 0.9.8. Se você tiver scripts existentes e não quiser alterá-los no momento, poderá continuar usando a versão 0.9.8. Ao usar a versão 1.0, você deverá testar cuidadosamente seus scripts em ambientes de pré-produção antes de usá-los em produção para evitar impactos inesperados.

Na maioria dos casos, a única diferença entre as duas versões é que o nome do cmdlet 1.0 segue o padrão {verbo}-AzureRm{substantivo}, enquanto o nome da versão 0.9.8 não inclui **Rm** (por exemplo, New-AzureRmResourceGroup em vez de New-AzureResourceGroup). Quando a diferença entre as versões é mais significante, este tópico mostra exemplos de ambas as versões.

Ao usar o Azure PowerShell 0.9.8, primeiro você deverá habilitar o modo do Gerenciador de Recursos executando o comando **Switch-AzureMode AzureResourceManager**. Este comando não é necessário no modo 1.0.

Para saber mais sobre a versão 1.0, incluindo como instalar e desinstalar a versão, consulte [Azure PowerShell 1.0 Visualização](https://azure.microsoft.com/blog/azps-1-0-pre/). Para saber mais sobre as alterações significativas nos comandos do Gerenciador de Recursos, consulte [Mudanças nos cmdlets do PowerShell de gerenciamento do Gerenciador de Recursos do Azure](../articles/powershell-preview-resource-manager-changes.md).

<!---HONumber=Nov15_HO4-->