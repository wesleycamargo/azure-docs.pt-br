---
title: "Criar uma rota definida pelo usuário para rotear tráfego de rede por meio de um dispositivo de rede virtual – PowerShell | Microsoft Docs"
description: "Saiba como criar uma rota definida pelo usuário para substituir o roteamento padrão do Azure pelo roteamento de tráfego de rede por meio de um dispositivo de rede virtual."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 70ddec1c7ba76ef7f42048896079e5c5fa2bf60c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Criar uma rota definida pelo usuário – PowerShell

Neste tutorial, saiba como criar rotas definidas pelo usuário para rotear o tráfego entre duas sub-redes de [rede virtual](virtual-networks-overview.md) por meio de um dispositivo de rede virtual. Um dispositivo de rede virtual é uma máquina virtual que executa um aplicativo de rede, tal como um firewall. Para saber mais sobre soluções de virtualização de rede pré-configuradas que você pode implantar em uma rede virtual do Azure, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando você cria sub-redes em uma rede virtual, o Azure cria [rotas de sistema](virtual-networks-udr-overview.md#system-routes) padrão que habilitam recursos em todas as sub-redes para se comunicarem entre si, conforme mostrado na figura a seguir:

![Rotas padrão](./media/create-user-defined-route/default-routes.png)

Neste tutorial, você criará uma rede virtual com sub-redes públicas, privadas e DMZ, conforme mostrado na figura a seguir. Normalmente, os servidores Web podem ser implantados em uma sub-rede pública, enquanto um aplicativo ou servidor de banco de dados pode ser implantado em uma sub-rede privada. Você cria uma máquina virtual para atuar como uma solução de virtualização de rede na sub-rede DMZ e, opcionalmente, cria uma máquina virtual em cada sub-rede que se comunica por meio do dispositivo de rede virtual. Todo o tráfego entre as sub-redes públicas e privadas é roteado por dispositivo, conforme mostrado na figura a seguir:

![Rotas definidas pelo usuário](./media/create-user-defined-route/user-defined-routes.png)

Este artigo fornece etapas para criar uma rota definida pelo usuário usando o modelo de implantação do Resource Manager, que é o modelo de implantação recomendado ao criar rotas definidas pelo usuário. Se você precisar criar uma rota definida pelo usuário (clássica), consulte [Criar uma rota definida pelo usuário (clássica)](virtual-network-create-udr-classic-ps.md). Se você não estiver familiarizado com os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para saber mais sobre as rotas definidas pelo usuário, confira [Visão geral de rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Criar rotas e solução de virtualização de rede

Você pode instalar e configurar a versão mais recente do módulo [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell em seu PC ou clique no botão **Experimentar** em qualquer um dos scripts para executar os scripts no Azure Cloud Shell. O Cloud Shell tem o módulo AzureRM PowerShell instalado.
 
1. **Pré-requisitos**: criar uma rede virtual com duas sub-redes completando as etapas em [Criar uma rede virtual](#create-a-virtual-network).
2. Se executar o PowerShell do seu computador, faça logon no Azure com a [Conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) usando o comando `login-azurermaccount`. Se estiver usando o Cloud Shell, você estará automaticamente conectado. Pode ser necessário reiniciar o Cloud Shell para deixar de usar o shell de busca usado ao criar a rede virtual de pré-requisito.
3. Defina algumas variáveis usadas pelas etapas:

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Crie uma sub-rede de DMZ da rede virtual existente:

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Crie um endereço IP público estático para a máquina virtual de solução de virtualização de rede.

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. Criar a máquina virtual de NVA. A NVA pode ser uma máquina virtual executando o sistema operacional Linux ou Windows. Para criar a máquina virtual, copie o script para o sistema operacional e cole-o na sessão do PowerShell. Se criar uma VM do Windows, cole o script em um editor de texto, altere o "valor" para a variável $cred e, em seguida, cole o texto modificado na sessão do PowerShell:

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. Por padrão, o Azure roteia tráfego entre todas as sub-redes contidas em uma rede virtual. Crie uma rota para alterar o roteamento padrão do Azure para que o tráfego da sub-rede *pública* seja roteado para a NVA, em vez de diretamente para a sub-rede *privada*.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Crie uma tabela de rotas para a sub-rede *pública*.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. Crie uma tabela de rotas para sub-rede pública. Associar uma tabela de rotas a uma sub-rede faz com que o Azure roteie todo o tráfego de saída da sub-rede de acordo com as rotas na tabela de rotas. Uma tabela de rotas pode ser associada a nenhuma ou várias sub-redes, enquanto uma sub-rede pode ter zero ou uma tabela de rotas associada a ela.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Crie uma rota para o tráfego da sub-rede *privada* para a *pública* por meio da máquina virtual da NVA.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Crie a tabela de rotas para a sub-rede *privada*.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Associe a tabela de rotas à sub-rede *privada*.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Opcional:** Crie uma máquina virtual nas sub-redes Pública e Privada e valide que a comunicação entre as máquinas virtuais é roteada através da solução de virtualização de rede completando as etapas em [Validar roteamento](#validate-routing).
15. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-resources).

## <a name="validate-routing"></a>Validar roteamento

1. Se você ainda não fez isso, conclua as etapas em [Criar rotas e solução de virtualização de rede](#create-routes-and-network-virtual-appliance).
2. Clique no botão **Experimente** na caixa a seguir, que abre o Azure Cloud Shell. Se solicitado, faça logon no Azure usando a [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). O Azure Cloud Shell é um shell de busca gratuito com a interface de linha de comando do Azure pré-instalada. 

    Os scripts a seguir criam duas máquinas virtuais, uma na sub-rede *Pública* e outra na *Privada*. Os scripts também habilitam o encaminhamento IP para o adaptador de rede dentro do sistema operacional da NVA para habilitar o sistema operacional a rotear o tráfego por meio do adaptador de rede. Uma NVA de produção normalmente inspeciona o tráfego antes de roteá-lo, mas neste tutorial, a NVA simples apenas roteia o tráfego, sem inspecioná-lo. 

    Clique no botão **Copiar** dos scripts **Linux** ou **Windows** a seguir e cole o conteúdo do script em um editor de texto. Altere a senha para a variável *adminPassword* e, em seguida, cole o script no Azure Cloud Shell. Execute o script para o sistema operacional que você selecionou ao criar o dispositivo de rede virtual na etapa 7 de [Criar rotas e solução de virtualização de rede](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Valide a comunicação entre as máquinas virtuais nas sub-redes pública e privada. 

    - Abra uma conexão [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) ou de [Área de Trabalho Remota](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) para o endereço IP público da máquina virtual *myVm-Public*.
    - Em um prompt de comando na máquina virtual *myVm-Public*, digite `ping myVm-Private`. Você recebe respostas porque a NVA roteia o tráfego da sub-rede pública para a privada.
    - Da máquina virtual *myVm-Public*, execute uma rota de rastreamento entre as máquinas virtuais nas sub-redes pública e privada. Digite o comando apropriado a seguir, dependendo de qual sistema operacional você instalou nas máquinas virtuais nas sub-redes Pública e Privada:
        - **Windows**: Em um prompt de comando, execute o comando `tracert myvm-private`.
        - **Ubuntu**: Execute o comando `tracepath myvm-private`.
      O tráfego passa por 10.0.2.4 (a NVA) antes de alcançar 10.0.1.4 (a máquina virtual na sub-rede Privada). 
    - Conclua as etapas anteriores conectando-se à máquina virtual *myVm-Private* e executando ping na máquina virtual *myVm-Public*. A rota de rastreamento mostra a comunicação viajando através de 10.0.2.4 antes de alcançar 10.0.0.4 (a máquina virtual na sub-rede pública).
    
      > [!NOTE]
      > As etapas anteriores permitem confirmar o roteamento entre os endereços IP privados do Azure. Caso você deseje encaminhar o tráfego, ou usar um proxy para ele, a endereços IP públicos por meio de uma solução de virtualização de rede:
      > - O dispositivo deve fornecer a funcionalidade de conversão de endereços de rede ou de proxy. Se a conversão de endereços de rede for usada, o dispositivo deverá converter o endereço IP de origem para seu próprio e, em seguida, encaminhar essa solicitação para o endereço IP público. Independentemente de o dispositivo ter o endereço de origem convertido em um endereço de rede, ou estiver usando um proxy, o Azure converte o endereço IP privado da solução de virtualização de rede para um endereço IP público. Para obter mais informações sobre os diferentes métodos usados pelo Azure para converter endereços IP privados em endereços IP públicos, consulte [Noções básicas sobre conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Uma rota adicional na tabela de rota como o prefixo: 0.0.0.0/0, o tipo do próximo salto VirtualAppliance e o endereço IP do próximo salto 10.0.2.4 (no script de exemplo anterior).
      >
    - **Opcionalmente**: Use a funcionalidade de próximo salto do Observador de Rede do Azure para validar o próximo salto entre duas máquinas virtuais no Azure. Antes de usar o Observador de Rede, você deve primeiro [Criar uma instância do Observador de Rede do Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para a região na qual você deseja usá-lo. Neste tutorial, a região Leste dos EUA é usada. Depois de habilitar uma instância do Observador de Rede para a região, digite o comando a seguir para ver as informações do próximo salto entre as máquinas virtuais nas sub-redes Pública e Privada:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       A saída retorna *10.0.2.4* como o **nextHopIpAddress** e *VirtualAppliance* como o **nextHopType**.

> [!NOTE]
> Para ilustrar os conceitos neste tutorial, os endereços IP públicos são atribuídos a máquinas virtuais nas sub-redes Pública e Privada, enquanto todo o acesso de porta de rede é habilitado dentro do Azure para ambas as máquinas virtuais. Ao criar máquinas virtuais para uso em produção, você não pode atribuir endereços IP públicos a elas e pode filtrar o tráfego de rede para a sub-rede Privada implantando uma solução de virtualização de rede diante dela ou atribuindo um grupo de segurança de rede às sub-redes, ao adaptador de rede ou a ambos. Para saber mais sobre grupos de segurança de rede, confira [Grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Este tutorial requer uma rede virtual existente com duas sub-redes. Clique no botão **Experimente** da caixa a seguir para criar uma rede virtual rapidamente. Clicar no botão **Experimente** abre o [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Embora o Cloud Shell execute o PowerShell ou um shell Bash, nesta seção o shell Bash é usado para criar a rede virtual. O shell Bash tem a interface de linha de comando do Azure instalada. Se solicitado pelo Cloud Shell, faça logon no Azure usando a [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Para criar a rede virtual usada neste tutorial, clique no botão **Copiar** na caixa a seguir e, em seguida, cole o script no Azure Cloud Shell:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Para saber mais sobre como usar o portal, o PowerShell ou um modelo do Azure Resource Manager para criar uma rede virtual, consulte [Criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Excluir recursos

Após a conclusão este tutorial, convém excluir os recursos criados, para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos. No PowerShell, insira o comando a seguir:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

- Crie uma [solução de virtualização de rede altamente disponível](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Soluções de virtualização de rede geralmente possuem vários adaptadores de rede e endereços IP atribuídos a eles. Saiba como [adicionar adaptadores de rede a uma máquina virtual existente](virtual-network-network-interface-vm.md#vm-add-nic) e [adicionar endereços IP a um adaptador de rede](virtual-network-network-interface-addresses.md#add-ip-addresses). Embora todos os tamanhos de máquina virtual possam ter pelo menos dois adaptadores de rede anexados a eles, o tamanho de cada máquina virtual dá suporte a um número máximo de adaptadores de rede. Para saber a quantos adaptadores de rede cada tamanho de máquina virtual dá suporte, consulte os tamanhos de máquina virtual do [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Crie uma rota definida pelo usuário para realizar túnel forçado de tráfego local por meio de uma [conexão de VPN site a site](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
