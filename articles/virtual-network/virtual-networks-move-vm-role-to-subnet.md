<properties 
   pageTitle="Como mover uma VM ou instância de função para uma sub-rede diferente."
   description="Saiba como mover VMs e instâncias de função para uma sub-rede diferente"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# Como mover uma VM ou instância de função para uma sub-rede diferente.

Você pode usar o PowerShell para mover suas VMs de uma sub-rede para outra na mesma rede virtual (VNet). As instâncias de função podem ser movidas editando o CSCFG em vez de usar o PowerShell.

>[AZURE.NOTE] Este artigo contém informações referentes apenas a implantações clássicas do Azure.

Por que transferir VMs para outra sub-rede? A migração de sub-rede é útil quando a sub-rede antiga é muito pequena e não pode ser expandida devido às VMs existentes em execução nessa sub-rede. Nesse caso, você pode criar uma nova sub-rede maior e migrar as máquinas virtuais para a nova sub-rede. Após a conclusão da migração, você pode excluir a sub-rede antiga vazia.

## Como mover uma VM para outra sub-rede

Para mover uma VM, execute o cmdlet Set-AzureSubnet PowerShell usando o exemplo abaixo como um modelo. No exemplo a seguir, transferimos TestVM da sub-rede atual para Subnet-2. Não deixe de editar o exemplo para refletir o seu ambiente. Observe que sempre que você executar o cmdlet Update-AzureVM como parte de um procedimento, ele reiniciará a sua VM como parte do processo de atualização.

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

Se você especificou um IP privado interno estático para a sua VM, terá que desmarcar essa configuração antes de poder mover a máquina virtual para uma nova sub-rede. Nesse caso, use o seguinte:

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## Para mover uma instância de função para outra sub-rede

Para mover uma instância de função, edite o arquivo CSCFG. No exemplo a seguir, transferimos "Role0" na rede virtual *VNETName* da sub-rede atual para *Subnet-2*. Como a instância de função já foi implantada, você só alterará o nome da sub-rede = Subnet-2. Não deixe de editar o exemplo para refletir o seu ambiente.

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=AcomDC_0810_2016-->