O Azure PowerShell está disponível em duas versões - 1.0 e 0.9.8. Se você tiver scripts existentes e não quiser alterá-los no momento, poderá continuar usando a versão 0.9.8. Ao usar a versão 1.0, você deverá testar cuidadosamente seus scripts em ambientes de pré-produção antes de usá-los em produção para evitar impactos inesperados.

Os cmdlets da versão 1.0 seguem o padrão de nomenclatura {verbo}-AzureRm{substantivo}; por outro lado, os nomes da versão 0.9.8 não incluem **Rm** (por exemplo, New-AzureRmResourceGroup em vez de New-AzureResourceGroup). Ao usar o Azure PowerShell 0.9.8, você deve primeiro habilitar o modo do Gerenciador de Recursos executando o comando **Switch-AzureMode AzureResourceManager** . Este comando não é necessário no modo 1.0.

Os novos recursos só serão adicionados à versão 1.0. Para saber mais sobre a versão 1.0, incluindo como instalar e desinstalar a versão, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).



<!--HONumber=Nov16_HO3-->


