<properties
	pageTitle="Gerenciar o registro e a proteção" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em servidores locais no Azure ou em um armazenamento de dados secundário. Use este artigo para cancelar o registro de servidores de um cofre de Recuperação de Site e desativar a proteção para máquinas virtuais e servidores físicos." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/08/2015" 
	ms.author="raynew"/>

# Gerenciar o registro e a proteção

Este artigo descreve como cancelar o registro de servidores no cofre de Recuperação de Site e como desabilitar a proteção das máquinas virtuais protegidas pela Recuperação de Site. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Cancelar o registro de um servidor VMM

Cancele o registro de um servidor VMM a partir de um cofre, excluindo o servidor na guia **Servidores** no portal do Azure Site Recovery. Observe que:

-  **Servidor VMM conectado**: recomendamos o cancelamento do registro do servidor VMM quando ele estiver conectado ao Azure. Isso garante que as configurações no servidor VMM local, e os servidores VMM associados a ele (servidores VMM que contêm as nuvens mapeadas para nuvens no servidor que você deseja excluir), sejam apagadas apropriadamente. Recomendamos que você remova um servidor desconectado somente se houver um problema de conectividade permanente.
- **Servidor VMM desconectado**: se o servidor VMM não estiver conectado quando você o exclui, será necessário executar manualmente um script para realizar a limpeza. O script está disponível na [Galeria da Microsoft](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439). Anote a ID do VMM do servidor para concluir o processo de limpeza manual.
- **Servidor VMM em cluster**: se você precisar cancelar o registro de um servidor VMM implantado em um cluster, faça o seguinte:

	- Se o servidor estiver conectado, exclua o servidor VMM conectado na guia **Servidores**. Para desinstalar o Provedor no servidor, faça logon em cada nó do cluster e desinstale-o no Painel de Controle. Execute o script de limpeza mencionado na seção anterior em todos os nós passivos no cluster a fim de excluir as entradas do registro.
	- Se o servidor não estiver conectado, será necessário executar o script de limpeza em todos os nós do cluster.

### Cancelar o registro de um servidor VMM desconectado

No servidor VMM que você deseja remover:

1. Cancelar o registro do servidor VMM no portal do Azure.
2. No servidor VMM, baixe o script de limpeza.
3. Abra o PowerShell com a opção Executar como Administrador para alterar a política de execução para o escopo padrão (LocalMachine).
4. Siga as instruções no script. 

Nos servidores VMM com nuvens combinadas com as nuvens no servidor que você está removendo:

1. Execute o script de limpeza e execute as etapas 2 a 4.
2. Especifique a ID do VMM para o servidor VMM que teve o registro cancelado. 
3. Esse script removerá as informações de registro do servidor VMM e as informações de combinação em nuvem.


## Cancelar o registro de um servidor Hyper-V em um site do Hyper-V

Quando o Azure Site Recovery é implantado para proteger as máquinas virtuais localizadas em um servidor Hyper-V em um site do Hyper-V (em um servidor VMM), você pode cancelar o registro de um servidor Hyper-V de um cofre da seguinte maneira:

1. Desabilite a proteção para máquinas virtuais localizadas no servidor Hyper-V.
2. Na guia **Servidores**, no portal do Azure Site Recovery, selecione o servidor > Excluir. O servidor não precisa estar conectado ao Azure para fazer isso.
3. Execute o script a seguir para limpar as configurações no servidor e cancelar o registro do cofre. 

	    pushd .
	    try
	    {
		     $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
		     $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
		     $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
		     $isAdmin=$principal.IsInRole($administrators)
		     if (!$isAdmin)
		     {
		        "Please run the script as an administrator in elevated mode."
		        $choice = Read-Host
		        return;       
		     }
	
		    $error.Clear()    
			"This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
			$choice =  Read-Host
		
			if (!($choice -eq 'Y' -or $choice -eq 'y'))
			{
			"Stopping cleanup."
			return;
			}
		
			$serviceName = "dra"
			$service = Get-Service -Name $serviceName
			if ($service.Status -eq "Running")
		    {
				"Stopping the Azure Site Recovery service..."
				net stop $serviceName
			}
		
		    $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
			$registrationPath = $asrHivePath + '\Registration'
			$proxySettingsPath = $asrHivePath + '\ProxySettings'
		    $draIdvalue = 'DraID'
	    
		    if (Test-Path $asrHivePath)
		    {
		        if (Test-Path $registrationPath)
		        {
		            "Removing registration related registry keys."	
			        Remove-Item -Recurse -Path $registrationPath
		        }
	
		        if (Test-Path $proxySettingsPath)
	        {
			        "Removing proxy settings"
			        Remove-Item -Recurse -Path $proxySettingsPath
		        }
	
		        $regNode = Get-ItemProperty -Path $asrHivePath
		        if($regNode.DraID -ne $null)
		        {            
		            "Removing DraId"
		            Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
		        }
			    "Registry keys removed."
		    }
	
		    # First retrive all the certificates to be deleted
			$ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
			# Open a cert store object
			$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
			$store.Open('ReadWrite')
			# Delete the certs
			"Removing all related certificates"
		    foreach ($cert in $ASRcerts)
		    {
			    $store.Remove($cert)
		    }
	    }catch
	    {	
		    [system.exception]
		    Write-Host "Error occured" -ForegroundColor "Red"
		    $error[0] 
		    Write-Host "FAILED" -ForegroundColor "Red"
	    }
	    popd


## Interromper a proteção de uma máquina virtual Hyper-V

Se você quiser interromper a proteção de uma máquina virtual Hyper-V, será necessário remover a proteção dela. Dependendo de como você remove a proteção, talvez seja necessário limpar manualmente as configurações de proteção na máquina.

### Remover proteção

1. Na guia **Máquinas Virtuais** das propriedades da nuvem, selecione a máquina virtual > **Remover**.
2. Na página **Confirmar a Remoção da Máquina Virtual**, há algumas opções:

	- **Desativar a proteção**— Se você habilitar e salvar essa opção, a máquina virtual não estará mais protegida pela Recuperação de Site. As configurações de proteção para a máquina virtual serão limpas automaticamente.
	- **Remover do cofre**—Se você selecionar essa opção, a máquina virtual será removida somente do cofre da Recuperação de Site. As configurações de proteção local da máquina virtual não serão afetadas. Será necessário limpar manualmente as configurações para remover as configurações de proteção e remover a máquina virtual da assinatura do Azure, e para isso use as instruções abaixo.

Se você optar por excluir a máquina virtual e seus discos rígidos, eles serão removidos do local de destino.

### Limpar as configurações de proteção manualmente (entre sites do VMM)

Se você selecionou **Parar de gerenciar a máquina virtual**, limpe manualmente as configurações:

1. No servidor primário, execute este script no console do VMM para limpar as configurações da máquina virtual primária. No console do VMM, clique no botão do PowerShell para abrir o console do PowerShell do VMM. Substitua SQLVM1 pelo nome da máquina virtual.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. No servidor VMM secundário, execute este script para limpar as configurações da máquina virtual secundária:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. No servidor VMM secundário, depois de executar o script, atualize as máquinas virtuais no servidor do host Hyper-V para que a máquina virtual secundária seja novamente detectada no console do VMM.
4. As etapas acima limparão do servidor do VMM somente as configurações de replicação. Se você quiser remover a replicação de máquina virtual para a máquina virtual. Você precisará fazer as seguir etapas nas máquinas virtuais primária e secundária. Execute o script abaixo para remover a replicação e substitua SQLVM1 pelo nome da máquina virtual.

	    Remove-VMReplication –VMName “SQLVM1”


### Limpar as configurações de proteção manualmente (entre sites VMM locais e no Azure)

1. No servidor VMM de origem, execute este script para limpar as configurações da máquina virtual primária:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. As etapas acima limparão do servidor do VMM somente as configurações de replicação. Depois de remover a replicação do servidor VMM, certifique-se de remover a replicação para a máquina virtual em execução no servidor host Hyper-V com este script. Substitua SQLVM1 pelo nome de sua máquina virtual e host01.contoso.com pelo nome do servidor do host Hyper-V.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### Limpar as configurações de proteção manualmente (entre sites do Hyper-V e o Azure)

1. No servidor de host do Hyper-V de origem, use esse script para remover a replicação para a máquina virtual. Substitua SQLVM1 pelo nome da máquina virtual.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## Interromper a proteção de uma máquina virtual VMware ou servidor físico

Se você quiser interromper a proteção de uma máquina virtual VMware ou de um servidor físico, será necessário remover a proteção dela. Dependendo de como você remove a proteção, talvez seja necessário limpar manualmente as configurações de proteção na máquina.

### Remover proteção

1. Na guia **Máquinas Virtuais** das propriedades da nuvem, selecione a máquina virtual > **Remover**.
2. Em **Remover Máquina Virtual**, selecione uma destas opções:

	- **Desabilitar a proteção (usada para a análise de recuperação e redimensionamento de volume)**—Você verá essa opção e poderá habilitá-la apenas se tiver:
		- **Redimensionado o volume da máquina virtual**—Quando você redimensiona um volume, a máquina virtual entra em um estado crítico. Se isso ocorrer, selecione essa opção. Ele desabilita a proteção enquanto retém os pontos de recuperação no Azure. Quando você reabilita a proteção para a máquina, os dados do volume redimensionado são transferidos para o Azure.
		- **Executar um failover**—Depois de testar seu ambiente executando um failover de máquinas virtuais VMware locais ou servidores físicos para o Azure, selecione esta opção para iniciar novamente a proteção das máquinas virtuais locais. Esta opção desabilita cada máquina virtual e, em seguida, será necessário reabilitar a proteção para elas. Observe que:
			- Desabilitar a máquina virtual com essa configuração não afeta a máquina virtual de réplica no Azure.
			- Você não deve desinstalar o Serviço de Mobilidade da máquina virtual.
	
	- **Desabilitar a proteção**—Se você habilitar e salvar essa opção, a máquina não será mais protegida pela Recuperação de Site. As configurações de proteção para a máquina serão limpas automaticamente.
	- **Remover do cofre**—Se você selecionar essa opção, a máquina será removida somente do cofre da Recuperação de Site. As configurações de proteção local da máquina não serão afetadas. Para remover as configurações na máquina e remover a máquina virtual da assinatura do Azure, será necessário limpar as configurações desinstalando o Serviço de Mobilidade. ![Opções de remoção](./media/site-recovery-manage-registration-and-protection/RegistrationProtection_RemoveVM.png)

<!---HONumber=July15_HO2-->