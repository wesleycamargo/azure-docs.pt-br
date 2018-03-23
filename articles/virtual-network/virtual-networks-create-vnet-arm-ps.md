---
title: Criar uma Rede Virtual do Azure com várias sub-redes - PowerShell | Microsoft Docs
description: Saiba como criar uma rede virtual com várias sub-redes usando o PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Criar uma rede virtual com várias sub-redes usando o PowerShell

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se com a Internet e em modo privado. Criar várias sub-redes em uma rede virtual permite segmentar a rede para que seja possível filtrar ou controlar o fluxo de tráfego entre as sub-redes. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar uma sub-rede
> * Testar a comunicação de rede entre máquinas virtuais

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroup* na localização *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

O **AddressPrefix** é especificado na notação CIDR. O prefixo de endereço especificado inclui os endereços IP 10.0.0.0-10.0.255.254.

## <a name="create-a-subnet"></a>Criar uma sub-rede

Uma sub-rede é criada, primeiro criando uma configuração de sub-rede e, em seguida, atualizando a rede virtual com a configuração da sub-rede. Crie uma configuração de sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). O exemplo a seguir cria duas configurações de sub-rede para sub-redes *Pública* e *Privada*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

O **AddressPrefix** especificado para uma sub-rede deve estar dentro do prefixo de endereço definido para a rede virtual. O DHCP do Azure atribui endereços IP de um prefixo de endereço de sub-rede aos recursos implantados em uma sub-rede. O Azure atribui apenas os endereços 10.0.0.4-10.0.0.254 aos recursos implantados na sub-rede **Pública**, porque o Azure reserva os quatro primeiros endereços (10.0.0.0-10.0.0.3 para a sub-rede, neste exemplo) e o último endereço (10.0.0.255 para a sub-rede, neste exemplo) em cada sub-rede.

Grave as configurações de sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que cria as sub-redes na rede virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Antes de implantar sub-redes e redes virtuais do Azure para uso em produção, é recomendável que você esteja familiarizado com as [considerações](manage-virtual-network.md#create-a-virtual-network) de espaço de endereço e os [limites da rede virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Quando os recursos forem implantados em sub-redes, algumas mudanças na rede virtual e na sub-rede, como a alteração de intervalos de endereços, poderão exigir a reimplantação dos recursos do Azure existentes implantados nas sub-redes.

## <a name="test-network-communication"></a>Testar comunicação de rede

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se com a Internet e em modo privado. Um tipo de recurso que pode ser implantado em uma rede virtual é uma máquina virtual. Crie duas máquinas virtuais na rede virtual para que seja possível testar a comunicação de rede entre elas e a Internet em uma etapa posterior. 

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma máquina virtual nomeada *myVmWeb* na sub-rede *Pública* da rede virtual *myVirtualNetwork*. A opção `-AsJob` cria a máquina virtual em segundo plano para que você continue na próxima etapa. Quando solicitado, insira o nome de usuário e a senha com os quais deseja iniciar sessão na máquina virtual.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmWeb" `
    -AsJob
```

O Azure atribuiu automaticamente 10.0.0.4 como o endereço IP privado da máquina virtual, porque 10.0.0.4 é o primeiro endereço IP disponível na sub-rede *Pública*. 

Crie uma máquina virtual na sub-rede *Privada*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmMgmt"
```

A criação da máquina virtual demora alguns minutos. Embora não esteja na saída retornada, o Azure atribuiu 10.0.1.4 como o endereço IP privado da máquina virtual, porque 10.0.1.4 é o primeiro endereço IP disponível na sub-rede *Privada* da *myVirtualNetwork*. 

Não continue com as etapas restantes até que a máquina virtual seja criada e o PowerShell retorne a saída.

As máquinas virtuais criadas neste artigo possuem uma [interface de rede](virtual-network-network-interface.md) com um endereço IP dinamicamente atribuído à interface de rede. Após implantar a VM, será possível [adicionar vários endereços IP públicos e privados ou alterar o método de atribuição de endereço IP para estático](virtual-network-network-interface-addresses.md#add-ip-addresses). É possível [adicionar interfaces de rede](virtual-network-network-interface-vm.md#vm-add-nic), até o limite suportado pelo [tamanho da VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que você seleciona ao criar uma máquina virtual. Além disso, é possível [habilitar a SR-IOV (virtualização de E/S de raiz única)](create-vm-accelerated-networking-powershell.md) para uma VM, mas somente quando você cria uma VM com um tamanho da VM que suporta a capacidade.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicação entre máquinas virtuais e Internet

É possível conectar ao endereço IP público de uma máquina virtual da Internet. Quando o Azure criou a máquina virtual *myVmMgmt*, um endereço IP público nomeado *myVmMgmt* também foi criado e atribuído à máquina virtual. Embora uma máquina virtual não seja necessária para ter um endereço IP público atribuído a ela, o Azure atribui um endereço IP público a cada máquina virtual criada, por padrão. Para comunicar-se da Internet para uma máquina virtual, um endereço IP público deverá ser atribuído à máquina virtual. Todas as máquinas virtuais podem se comunicar com a Internet, tendo ou não um endereço IP público atribuído à máquina virtual. Para saber mais sobre as conexões com a Internet de saída no Azure, consulte [Conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público de uma máquina virtual. O exemplo a seguir retorna o endereço IP público da máquina virtual *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a máquina virtual *myVmMgmt* do seu computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (protocolo RDP) é criado, baixado para seu computador e aberto. Insira o nome de usuário e senha especificados ao criar a máquina virtual (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a máquina virtual) e clique em **OK**. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão. 

Em uma etapa posterior, o ping será usado para comunicar-se com a máquina virtual *myVmMgmt* da máquina virtual *myVmWeb*. O ping usa ICMP, que é negado através do Firewall do Windows, por padrão. Habilite o ICMP através do firewall do Windows, inserindo o seguinte comando a partir do prompt de comando:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Embora o ping seja usado neste artigo, não é recomendável ICMP através do Firewall do Windows para implantações de produção.

Por razões de segurança, é comum limitar o número de máquinas virtuais que podem ser conectadas remotamente em uma rede virtual. Neste tutorial, a máquina virtual *myVmMgmt* é usada para gerenciar a máquina virtual *myVmWeb* na rede virtual. Use o seguinte comando para a área de trabalho remota na máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*:

``` 
mstsc /v:myVmWeb
```

Para comunicar-se com a máquina virtual *myVmMgmt* a partir da máquina virtual *myVmWeb*, insira o comando a seguir em um prompt de comando:

```
ping myvmmgmt
```

Você receberá uma saída semelhante à saída de exemplo a seguir:
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
É possível ver que o endereço da máquina virtual *myVmMgmt* é 10.0.1.4. 10.0.1.4 foi o primeiro endereço IP disponível no intervalo de endereços da sub-rede *Privada* implantada na máquina virtual *myVmMgmt* em uma etapa anterior.  Observe que o nome de domínio totalmente qualificado da máquina virtual é *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Embora a parte *dar5p44cif3ulfq00wxznl3i3f* do nome de domínio seja diferente para a máquina virtual, as partes restantes do nome de domínio serão as mesmas. Por padrão, todas as máquinas virtuais do Azure usam o serviço DNS do Azure padrão. Todas as máquinas virtuais dentro de uma rede virtual podem resolver os nomes de todas as outras máquinas virtuais na mesma rede virtual usando o serviço DNS padrão do Azure. Em vez de usar o serviço DNS do Azure padrão, é possível usar seu próprio servidor DNS ou a capacidade de domínio privado do serviço DNS do Azure. Para obter detalhes, consulte [Resolução de nome usando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ou [Usar DNS do Azure para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para instalar o IIS (Serviços de Informações da Internet) para Windows Server na *myVmWeb*, insira o comando a seguir de uma sessão do PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Após a conclusão da instalação do IIS, desconecte a sessão de área de trabalho remota da *myVmWeb* que direciona para a sessão de área de trabalho remota da *myVmMgmt*. Abra um navegador da Web e navegue para http://myvmweb. Você visualiza a página de boas-vindas do IIS.

Desconecte a sessão de área de trabalho remota da *myVmMgmt*.

Obter o endereço público do Azure atribuído à *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

No seu próprio computador, navegue até o endereço IP público da máquina virtual *myVmWeb*. A tentativa de visualizar a página de boas-vindas do IIS de seu próprio computador falha. A tentativa falha porque, quando as máquinas virtuais foram implantadas, o Azure criou um grupo de segurança de rede para cada máquina virtual, por padrão. 

Um grupo de segurança de rede contém regras de segurança que permitem ou negam tráfego de entrada e saída pela porta e pelo endereço IP. O grupo de segurança de rede padrão do Azure criado permite a comunicação entre todas as portas entre recursos na mesma rede virtual. Para máquinas virtuais do Windows, o grupo de segurança de rede padrão nega todo o tráfego de entrada da Internet em todas as portas, aceita a porta TCP 3389 (RDP). Como resultado, por padrão, também é possível RDP diretamente para a máquina virtual *myVmWeb* da Internet, mesmo que você não queira a porta 3389 aberta para um servidor Web. Como a navegação na Web comunica-se através da porta 80, a comunicação falha a partir da Internet porque não há nenhuma regra no grupo de segurança de rede padrão que permite o tráfego na porta 80.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, utilize [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como implantar uma rede virtual com várias sub-redes. Adicionalmente, aprendeu que ao criar uma máquina virtual do Windows, o Azure cria uma interface de rede que anexa à máquina virtual e cria um grupo de segurança de rede que permite somente tráfego na porta 3389, da Internet. Avance para o próximo tutorial para aprender como filtrar tráfego para sub-redes, e não para máquinas virtuais individuais.

> [!div class="nextstepaction"]
> [Filtrar tráfego para sub-redes](./virtual-networks-create-nsg-arm-ps.md)
