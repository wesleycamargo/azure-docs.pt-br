<properties
   pageTitle="Diversos endereços IP para máquinas virtuais – PowerShell | Microsoft Azure"
   description="Saiba como atribuir diversos endereços IP a uma máquina virtual usando o Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />



# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Atribuir vários endereços IP a máquinas virtuais

Uma VM (máquina virtual) do Azure pode ter uma ou mais NICs (adaptadores de rede) conectadas a ele. Cada NIC pode ter um ou mais endereços IP públicos ou privados atribuídos a ele. Se você não está familiarizado com endereços IP no Azure, leia o artigo [endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md) para saber mais sobre eles. Este artigo explica como usar o Azure PowerShell para atribuir vários endereços IP a uma NIC no modelo de implantação do Azure Resource Manager.

A atribuição de vários endereços IP a uma NIC permite que a VM:

- Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
- Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrar-se para a preview, envie um email para [vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido.
## <a name="scenario"></a>Cenário

Neste artigo, você associará três configurações de IP a uma interface de rede.
Os seguintes exemplos de configuração serão criados e atribuídos a uma NIC que terá três endereços IP privados e um endereço IP público atribuído a ela:

- IPConfig-1: um endereço IP privado dinâmico (padrão) e um endereço IP público do recurso do endereço IP público denominado PIP1.
- IPConfig-2: um endereço IP privado estático e nenhum endereço IP público.
- IPConfig-3: um endereço IP privado dinâmico e nenhum endereço IP público.

    ![Texto Alt da imagem](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Este cenário pressupõe que você tem um grupo de recursos chamado *RG1* np qual existe uma VNet chamada *VNet1* e uma sub-rede chamada *Subnet1*. Além disso, ele pressupõe que você tem uma VM chamada *VM1*, uma interface de rede chamada *VM1-NIC1* associada à essa VM e um endereço IP público chamado *PIP1*.

[Este artigo](./virtual-machines/virtual-machines-windows-ps-create.md ) explica como criar os recursos mencionados acima, caso você não os tenha criado anteriormente.

## <a name="<a-name-=-"create"></a>create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .

2. Altera os "valores" das seguintes $Variables para o [local](https://azure.microsoft.com/regions) no Azure no qual está a sua rede virtual, o nome do seu [grupo de recursos](../resource-group-overview.md#resource-groups), a VNet do grupo de recursos, a sub-rede a qual você deseja conectar o NIC e o nome do NIC.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Se você não souber o nome de uma localização do Azure ou um grupo de recursos existente, digite os seguintes comandos:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>A NIC deve ser conectada a uma sub-rede em uma VNet (Rede Virtual) do Azure. Os três componentes: NIC, sub-rede e rede virtual, devem existir na mesma região e [assinatura](../azure-glossary-cloud-terminology.md#subscription).  Se você não estiver familiarizado com VNets, leia o artigo [Visão geral da rede virtual](virtual-networks-overview.md) para saber mais sobre elas ou leia o artigo [Criar uma VNet](virtual-networks-create-vnet-arm-ps.md) para saber como criar uma.

    Se você não souber o nome de uma VNet existente, digite o seguinte comando e substitua *VNet1* na variável anterior pelo nome de uma VNet:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Se a lista retornada estiver vazia, você precisará criar uma VNet. Para saber como fazer isso, leia o artigo [Criar uma rede virtual](virtual-networks-create-vnet-arm-ps.md) .

    Digite os seguintes comandos para obter o nome das sub-redes existentes na VNet e substitua *Subnet1* acima pelo nome de uma sub-rede:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Digite o seguinte comando para recuperar a sub-rede e atribuí-la a uma variável.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Defina as configurações do IP que você deseja atribuir à NIC. Cada configuração pode ter um endereço IP privado estático ou dinâmico e um recurso de endereço IP público associado com um endereço estático ou dinâmico.

    Adicione ou remova qualquer número das configurações a seguir dependendo de quantos endereços IP você deseja associar à NIC e as configurações que você deseja configurar.

    **IPConfig-1**

    Altere o valor *PIP1* para o nome de um recurso de endereço IP de público existente que há na localização onde você está criando a NIC e que não esteja atualmente associado a outra NIC. Altere *RG1* para o nome do grupo de recursos em que o recurso de endereço IP público está. Altere *IPConfig-1* para o nome que você deseja dar para a primeira configuração do IP. Digite os seguintes comandos:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Observe a opção *-Primary* . Quando você atribuir várias configurações de IP a uma NIC, uma configuração deverá ser atribuída como a *Primária*. Se você não souber o nome de um recurso de endereço IP público existente, digite o seguinte comando:

        Get-AzureRMPublicIPAddress |Format-Table Name, Location, IPAddress, IpConfiguration

    Se a coluna **IPConfiguration** não tiver nenhum valor na saída retornada, o recurso de endereço IP público não será associado a uma NIC existente e poderá ser usado. Se a lista estiver em branco ou não houver nenhum recurso de endereço IP público disponível, você poderá criar um usando o comando **New-AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Altere *IPConfig-2* para o nome que você deseja dar à configuração do segundo IP e altere *10.0.0.5* para um endereço IP válido não usado para a sub-rede à qual você está atribuindo a NIC. Digite os seguintes comandos:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Digite o comando a seguir, se você não souber o intervalo de endereços IP atribuído à sub-rede:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Altere *IPConfig-3* para o nome que você deseja dar à configuração do terceiro IP e digite os seguintes comandos:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Você pode atribuir até 250 endereços IP privado a uma NIC. Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Crie a NIC usando as configurações de IP definidas na etapa anterior.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Anexe a NIC ao criar uma VM, seguindo as etapas no artigo [Criar uma VM](../virtual-machines/virtual-machines-windows-ps-create.md) . Embora o artigo crie uma VM que executa o Windows Server, as etapas são as mesmas para uma VM do Linux, diferindo na seleção do sistema operacional. Siga as etapas 1 a 3 do artigo. Ignore as etapas 4 e 5 e, em seguida, siga a etapa 6 no artigo Criar uma VM.

    >[AZURE.WARNING] A etapa 6 no artigo Criar uma VM falhará se você tiver alterado a variável denominada $nic para algo diferente na etapa 6 deste artigo ou se você ainda não tiver concluído as etapas anteriores deste artigo.

8. Veja os endereços IP privados que o DHCP do Azure atribuiu à NIC e o recurso de endereço IP público atribuído à NIC, digitando o seguinte comando:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Adicione manualmente todos os endereços IP privados secundários (endereços IP com *False* na coluna **Primário** da saída na etapa anterior) à configuração de TCP/IP no sistema operacional. O endereço IP privado atribuído à *IPConfig-1* na etapa 5 é atribuído automaticamente ao sistema operacional por meio do DHCP do Azure, porque ela é a configuração *Primária*.


**Windows**

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Em seguida, digite *ncpa. cpl* na janela do prompt de comando. Isso abrirá uma nova janela.
3. Abra as propriedades de **Rede local**.
4. Clique duas vezes em Protocolo IP versão 4 (IPv4)
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:
    - **Endereço IP**: insira o endereço IP privado *primário*
    - **Máscara de sub-rede**: defina com base na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
    - **Gateway padrão**: o primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    - Clique em **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
        - **Servidor DNS preferencial:** digite 168.63.129.16 se você não estiver usando seu próprio servidor DNS.  Se você estiver, digite o endereço IP do seu servidor DNS.
    - Clique no botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários listados na etapa 8 à NIC com a mesma sub-rede especificada para o endereço IP primário.
    - Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. Isso restabelecerá a conexão de RDP.

6. Em um prompt de comando, digite *ipconfig /all*. Todos os endereços IP que você adicionou são mostrados e o DHCP está desativado.


**Linux (Ubuntu)**

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se você não for, você poderá fazer isso usando o seguinte comando:

            sudo -i
3. Atualize o arquivo de configuração do adaptador de rede (supondo que 'eth0').
    - Mantenha o item de linha existente para o dhcp. Isso configurará o endereço IP primário para como ele estava anteriormente.
    - Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

                cd /etc/network/interfaces.d/
                ls

        Você deve ver um arquivo. cfg.
4. Abra o arquivo: vi *filename*.

    Você verá as seguintes linhas ao final do arquivo:

            auto eth0
            iface eth0 inet dhcp
5. Adicione as seguintes linhas após as linhas existentes neste arquivo:

            iface eth0 inet static
            address <your private IP address here>
6. Salve o arquivo usando o seguinte comando:

            :wq
7.  Reinicie o adaptador de rede com o seguinte comando:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Execute ifdown e ifup na mesma linha se você estiver usando uma conexão remota.
8. Verifique se que o endereço IP foi adicionado ao adaptador de rede com o seguinte comando:

            ip addr list eth0

    Você verá o endereço IP adicionado como parte da lista.

**Linux (Redhat, CentOS e outros)**

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se você não for, você poderá fazer isso usando o seguinte comando:

            sudo -i
3. Digite sua senha e siga as instruções conforme solicitado. Quando você for o usuário raiz, navegue até a pasta de scripts de rede com o seguinte comando:

            cd /etc/sysconfig/network-scripts
4. Liste os arquivos ifcfg relacionados usando o seguinte comando:

            ls ifcfg-*

    Você deve ver *ifcfg-eth0* como um dos arquivos.
5. Copie o arquivo *ifcfg-eth0* e nomeie-o *ifcfg-eth0:0* com o seguinte comando:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Edite o arquivo *ifcfg-eth0:0* com o seguinte comando:

            vi ifcfg-eth1
7. Altere o dispositivo para o nome apropriado no arquivo; *eth0:0* , nesse caso, com o seguinte comando:

            DEVICE=eth0:0
8. Altere a linha *IPADDR = YourPrivateIPAddress* para refletir o endereço IP.
9. Salve o arquivo com o seguinte comando:

            :wq
10. Reinicie os serviços de rede e certifique-se de que as alterações foram bem-sucedidas executando os seguintes comandos:

            /etc/init.d/network restart
            Ipconfig

    Você verá o endereço IP adicionado, *eth0:0*, na lista retornada.

## <a name="<a-name="add"></a>add-ip-addresses-to-an-existing-vm"></a><a name="add"></a>Adicione os endereços IP a uma VM existente

Siga as seguintes etapas para adicionar mais endereços IP a uma NIC existente:

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .

2. Altere os "valores" de $Variables a seguir para o nome da NIC ao qual você deseja adicionar os endereços IP e o grupo de recursos e a localização onde a NIC está:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Se você não souber o nome da NIC que você deseja alterar, digite os seguintes comandos e altere os valores das variáveis anteriores:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Crie uma variável e defina-a para a NIC existente digitando o seguinte comando:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Recupere a ID de sub-rede à qual a NIC está conectada concluindo a [etapa 3](#subnet) da seção Criar uma VM com vários endereços IP deste artigo.

5. Crie as configurações de IP que você deseja adicionar à rede seguindo as instruções na [etapa 5](#ipconfigs) da seção Criar uma VM com diversos endereços IP deste artigo.

6. Altere *$IPConfigName4* para o nome da configuração de IP que você criou na etapa anterior. Para adicionar a configuração, digite o seguinte comando:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Para definir a NIC com a configuração de IP, digite o seguinte comando:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Adicione os endereços IP que você adicionou à NIC ao sistema operacional da VM seguindo as instruções na [etapa 9](#os) da seção Criar uma VM com diversos endereços IP deste artigo.



<!--HONumber=Oct16_HO2-->


