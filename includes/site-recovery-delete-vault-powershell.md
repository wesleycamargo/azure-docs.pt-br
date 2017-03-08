## <a name="delete-a-recovery-services-vault-powershell"></a>Excluir um cofre de Serviços de Recuperação (PowerShell)

1. Obter o cofre de Serviços de Recuperação

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Excluir o cofre

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Use o comando acima com muito cuidado, pois, se você excluir qualquer cofre por engano, perderá todos os dados. Esta é uma ação permanente e não há nenhuma maneira de revertê-la.  


