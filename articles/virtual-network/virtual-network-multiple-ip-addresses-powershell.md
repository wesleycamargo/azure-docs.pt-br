---
title: "Vários endereços IP para máquinas virtuais – PowerShell | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando o PowerShell | Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: a0d8a6dbe793bc4ea5211e772439d931c8e84a04
ms.openlocfilehash: cf2a57f96576b18692dd42a9680d7f3b8d8f7c69


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP a máquinas virtuais usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Uma VM (máquina virtual) do Azure tem um ou mais NICs (adaptadores de rede) conectados a ele. Qualquer NIC pode ter um ou mais endereços IP públicos e privados estáticos ou dinâmicos atribuídos a ele. A atribuição de vários endereços IP a uma VM permite as seguintes capacidades:

* Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
* Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
* A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md).

Todos os NICs anexados a uma VM têm uma ou mais configurações IP associadas a eles. Cada configuração recebe um endereço IP privado estático ou dinâmico. Cada configuração também pode ter um recurso de endereço IP público associado a ele. Um recurso de endereço IP público tem um endereço IP dinâmico ou estático atribuído a ele. Se você não está familiarizado com endereços IP no Azure, leia o artigo [endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md) para saber mais sobre eles.

Este artigo explica como usar o PowerShell para atribuir vários endereços IP a uma VM criada por meio do modelo de implantação do Azure Resource Manager. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Cenário
Uma VM com um único NIC é criada e conectada a uma rede virtual. A VM requer três endereços IP *privados* diferentes e dois endereços IP *públicos*. Os endereços IP são atribuídos às seguintes configurações de IP:

* **IPConfig-1:** atribui um endereço IP privado *dinâmico* (padrão) e um endereço IP público *estático*.
* **IPConfig-2:** atribui um endereço IP privado *estático* e um endereço IP público *estático*.
* **IPConfig-3:** atribui um endereço IP privado *dinâmico* e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

As configurações de IP são associadas ao NIC quando o NIC é criado e o NIC é conectado à máquina Virtual quando a VM é criada. Os tipos de endereços IP usados para o cenário são para fins de ilustração. Você pode atribuir quaisquer tipos de atribuição e de endereço IP necessários.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, como descrito no cenário. Altere os nomes da variável e os tipos de endereço IP como exigido por sua implementação.

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .
2. Registre-se para a visualização enviando um email para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não tente concluir as etapas restantes:
    - Até que você receba um email notificando de que foi aceito para a visualização
    - Sem seguir as instruções no email recebido
3. Conclua as etapas 1 a 4 do artigo [Criar uma VM Windows](../virtual-machines/virtual-machines-windows-ps-create.md). Não conclua a etapa 5 (criação de interface de rede e recurso IP público). Se você alterar os nomes das variáveis usadas neste artigo, altere os nomes das variáveis também nas etapas restantes. Para criar uma VM Linux, selecione o sistema operacional Linux em vez do Windows.
4. Crie uma variável para armazenar o objeto de sub-rede criado na Etapa 4 (criar uma rede virtual) do artigo Criar um artigo de VM Windows digitando o seguinte comando:

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
5. Defina as configurações do IP que você deseja atribuir ao NIC. Você pode adicionar, remover ou alterar as configurações conforme necessário. As configurações a seguir são descritas no cenário:

    **IPConfig-1**

    Insira os comandos a seguir para criar:
    - Um recurso de endereço IP público com um endereço IP público estático
    - Uma configuração de IP com o recurso de endereço IP público e um endereço IP privado dinâmico

    ```powershell
    $myPublicIp1     = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    Observe a opção `-Primary` no comando anterior. Quando você atribuir várias configurações de IP a uma NIC, uma configuração deverá ser atribuída como a *Primária*.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **IPConfig-2**

    Altere o valor da variável **$IPAddress** a seguir para um endereço válido disponível na sub-rede que você criou. Para verificar se o endereço 10.0.0.5 está disponível na sub-rede, digite o comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`. Se o endereço está disponível, a saída retorna *True*. Se não está disponível, a saída retorna *False* e uma lista de endereços disponíveis. Insira os seguintes comandos para criar um novo recurso de endereço IP público e uma nova configuração de IP com um endereço IP público estático e um endereço IP privado estático:
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = 10.0.0.5
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    Insira os comandos a seguir para criar uma configuração de IP com um endereço IP privado dinâmico e nenhum endereço IP público:

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
6. Crie o NIC usando as configurações de IP definidas na etapa anterior e digitando o seguinte comando:

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > Embora este artigo atribua todas as configurações de IP a um único NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com vários NICs, leia o artigo [Criar uma VM com vários NICs](virtual-network-deploy-multinic-arm-ps.md).

7. Conclua a etapa 6 do artigo [Criar uma VM](../virtual-machines/virtual-machines-windows-ps-create.md). 

    > [!WARNING]
    > A etapa 6 de Criar um artigo de VM falhará se:
    > - Você alterou a variável chamada $myNIC para algo diferente na etapa 6 deste artigo.
    > - Você não tiver concluído as etapas anteriores deste artigo e criar um artigo de VM.
    >
8. Exiba os endereços IP privados e o recurso de endereço IP público atribuído ao NIC, digitando o seguinte comando:

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>Adicionar endereços IP em um sistema operacional da VM

Conecte-se e faça logon em uma VM criada com vários endereços IP privados. Você deve adicionar manualmente todos os endereços IP privados (incluindo o principal) que você adicionou à VM. Complete as etapas a seguir para seu sistema operacional VM:

### <a name="windows"></a>Windows

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Digite *ncpa.cpl* no prompt de comando para abrir a janela **Conexões de rede**.
3. Abra as propriedades de **Rede local**.
4. Clique duas vezes em versão do Protocolo de Internet 4 (IPv4).
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:

    * **Endereço IP**: insira o endereço IP privado *primário*
    * **Máscara de sub-rede**: defina com base na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
    * **Gateway padrão**: o primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Clique em **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
        * **Servidor DNS preferencial**: digite 168.63.129.16 se você não estiver usando seu próprio servidor DNS.  Se você estiver usando seu próprio servidor DNS, digite o endereço IP do seu servidor.
    * Clique no botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários listados na etapa 8 à NIC com a mesma sub-rede especificada para o endereço IP primário.
    * Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. A conexão RDP é restabelecida.
6. Em um prompt de comando, digite *ipconfig /all*. Todos os endereços IP que você adicionou são mostrados e o DHCP está desativado.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Atualize o arquivo de configuração do adaptador de rede (supondo que 'eth0').

    * Mantenha o item de linha existente para o dhcp. O endereço IP principal permanece configurado como era anteriormente.
    * Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Você deve ver um arquivo. cfg.
4. Abra o arquivo: vi *filename*.

    Você verá as seguintes linhas ao final do arquivo:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Adicione as seguintes linhas após as linhas existentes neste arquivo:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Salve o arquivo usando o seguinte comando:

    ```bash
    :wq
    ```

7. Reinicie o adaptador de rede com o seguinte comando:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Execute ifdown e ifup na mesma linha se você estiver usando uma conexão remota.
    >

8. Verifique se que o endereço IP foi adicionado ao adaptador de rede com o seguinte comando:

    ```bash
    Ip addr list eth0
    ```

    Você verá o endereço IP adicionado como parte da lista.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS e outros)

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Digite sua senha e siga as instruções conforme solicitado. Quando você for o usuário raiz, navegue até a pasta de scripts de rede com o seguinte comando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Liste os arquivos ifcfg relacionados usando o seguinte comando:

    ```bash
    ls ifcfg-*
    ```

    Você deve ver *ifcfg-eth0* como um dos arquivos.

5. Copie o arquivo *ifcfg-eth0* e nomeie-o *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Edite o arquivo *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    vi ifcfg-eth1
    ```

7. Altere o dispositivo para o nome apropriado no arquivo; *eth0:0* , nesse caso, com o seguinte comando:

    ```bash
    DEVICE=eth0:0
    ```

8. Altere a linha *IPADDR = YourPrivateIPAddress* para refletir o endereço IP.
9. Salve o arquivo com o seguinte comando:

    ```bash
    :wq
    ```

10. Reinicie os serviços de rede e certifique-se de que as alterações foram bem-sucedidas executando os seguintes comandos:

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    Você verá o endereço IP adicionado, *eth0:0*, na lista retornada.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados adicionais para um NIC existente ao concluir as etapas a seguir. Os exemplos baseiam-se no [cenário](#Scenario) descrito neste artigo.

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .
2. Registre-se para a visualização enviando um email para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não tente concluir as etapas restantes:
    - Até que você receba um email notificando de que foi aceito para a visualização
    - Sem seguir as instruções no email recebido
3. Altere os "valores" de $Variables a seguir para o nome do NIC ao qual você deseja adicionar os endereços IP e o grupo de recursos e a localização onde o NIC está:

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    Se você não souber o nome da NIC que você deseja alterar, digite os seguintes comandos e altere os valores das variáveis anteriores:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
4. Crie uma variável e defina-a para a NIC existente digitando o seguinte comando:

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
5. Nos comandos a seguir, mude *myVNet* e *mySubnet* para os nomes de rede virtual e sub-rede aos quais o NIC está conectado. Digite os comandos para recuperar os objetos de rede virtual e sub-rede aos quais o NIC está conectado:

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    Se você não souber o nome da rede virtual ou sub-rede à qual o NIC está conectado, digite o seguinte comando:
    ```powershell
    $mynic.IpConfigurations
    ```
    Procure por texto semelhante ao seguinte no resultado retornado:

        Subnet   : {
                     "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"

    Nessa saída, *myVnet* é a rede virtual e *mySubnet* é a sub-rede à qual o NIC está conectado.

6. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a um NIC, você deverá criar uma configuração de IP. O comando a seguir cria uma configuração com um endereço IP estático 10.0.0.7. Se você deseja adicionar um endereço IP privado dinâmico, remova `-PrivateIpAddress 10.0.0.7` antes de inserir o comando. Ao especificar um endereço IP estático, ele deve ser um endereço não usado para a sub-rede. É recomendável que você primeiro teste o endereço para garantir que ele está disponível digitando o comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet`. Se o endereço IP está disponível, a saída retorna *True*. Se não está disponível, a saída retorna *False* e uma lista de endereços disponíveis.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado por meio de sua associação a uma nova configuração de IP ou de uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Associar o recurso a uma nova configuração de IP**
    
    Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    Para criar uma nova configuração de IP com um endereço IP privado dinâmico e o recurso de endereço IP público *myPublicIP3* associado, insira o seguinte comando:

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

    **Associar o recurso a uma configuração de IP existente**
    Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

    Procure uma linha semelhante à seguinte na saída retornada:

        Name       PrivateIpAddress PublicIpAddress                                           Primary
        ----       ---------------- ---------------                                           -------
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False

    Como a coluna **PublicIpAddress** para *IpConfig 3* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IpConfig-3*:
    
    ```powershell
    Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

7. Defina o NIC com a nova configuração de IP digitando o seguinte comando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

8. Exiba os endereços IP privados e o recurso de endereço IP público atribuído ao NIC digitando o seguinte comando:

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

9. Adicione os endereços IP que você adicionou à NIC para o sistema operacional da VM seguindo as instruções da seção [Adicionar endereços IP a um sistema operacional de VM](#OsConfig) deste artigo.



<!--HONumber=Nov16_HO3-->


