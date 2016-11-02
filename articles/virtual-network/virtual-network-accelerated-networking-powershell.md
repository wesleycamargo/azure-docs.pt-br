<properties 
   pageTitle="Rede acelerada para uma máquina virtual – PowerShell | Microsoft Azure"
   description="Saiba como configurar a Rede Acelerada para uma máquina virtual do Azure usando PowerShell."
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
   ms.date="09/23/2016"
   ms.author="jdial" />


# <a name="accelerated-networking-for-a-virtual-machine"></a>Rede acelerada para uma máquina virtual

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Rede Acelerada permite SR-IOV (Virtualização de E/S de Raiz Única) para uma VM (máquina virtual), melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos VM com suporte. Esse artigo explica como usar o Azure PowerShell para configurar a Rede Acelerada no modelo de implantação do Azure Resource Manager. Você também pode criar uma máquina virtual com Rede Acelerada usando o Portal do Azure. Para saber como fazer isso, clique na caixa do Portal do Azure na parte superior deste artigo.

A figura abaixo mostra a comunicação entre duas VMs (máquinas virtuais) com e sem Rede Acelerada:

![Comparação](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sem Rede Acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais, leia o artigo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualização de Rede Hyper-V e comutador virtual).

Com Rede Acelerada, o tráfego de rede chega à NIC (placa de rede) e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica sem Rede Acelerada são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios de Rede Acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma rede virtual.  Ao se comunicar entre redes virtuais ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Benefícios

- **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
- **Tremulação reduzida:** processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
- **Menor utilização da CPU:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="limitations"></a>Limitações

Existem as seguintes limitações ao usar essa funcionalidade:
 
- **Criação de adaptador de rede:** rede acelerada só pode ser habilitada para um novo adaptador de rede.  Não pode ser habilitada em um adaptador de rede existente.
- **Criação da VM:** um adaptador de rede com rede acelerada habilitada somente poderá ser conectado a uma VM quando a VM for criada. O adaptador de rede não pode ser conectado a uma VM existente.
- **Regiões:** oferecida apenas nas regiões do Azure do Centro-Oeste dos EUA e da Europa Ocidental. O conjunto de regiões será expandido no futuro.
- **Sistema de operacional com suporte:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Suporte a Linux e Windows Server 2012 será adicionado em breve.
- **Tamanho da VM:** Standard_D15_v2 e Standard_DS15_v2 são os únicos tamanhos de instância de VM com suporte. Para obter mais informações, consulte o artigo [Tamanhos das máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) . O conjunto de tamanhos de instância VM com suporte será expandido no futuro.

Alterações feitas nessas limitações serão anunciadas na página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Criar uma VM do Windows com rede acelerada

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .
2. Para registrar-se para a preview, envie um email para [Assinaturas de rede acelerada](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não conclua as etapas restantes antes de receber um email notificando que você foi aceito para a visualização.
3. Registre a funcionalidade com sua assinatura digitando os seguintes comandos:

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Substitua *westcentralus* pelo nome de outra localização com suporte nessa funcionalidade listada na seção [Limitações](#limitations) deste artigo (se desejado). Digite o comando a seguir para definir uma variável para a localização:

        $locName = "westcentralus"

5. Substitua *RG1* por um nome para o grupo de recursos que conterá o novo adaptador de rede e digite os seguintes comandos para criá-lo:

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Altere *VM1-NIC1* para o nome que deseja atribuir ao adaptador de rede e, em seguida, digite o seguinte comando:

        $NICName = "VM1-NIC1"

7. O adaptador de rede deve estar conectado a uma sub-rede em uma VNet (Rede Virtual) do Azure existente na mesma localização e [assinatura](../azure-glossary-cloud-terminology.md#subscription) que o adaptador de rede. Saiba mais sobre VNets lendo o artigo [Visão geral da rede virtual](virtual-networks-overview.md) se não estiver familiarizado com elas. Para criar uma VNet, conclua as etapas no artigo [Create a VNet](virtual-networks-create-vnet-arm-ps.md) (Criar uma VNet). Altere os "valores" das seguintes $Variables para o nome da VNet e da sub-rede às quais você deseja conectar o adaptador de rede.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Se você não souber o nome de uma rede virtual existente na localização escolhida na etapa 3, digite os seguintes comandos:
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Se a lista retornada estiver vazia, você precisará criar uma VNet na localização. Para criar uma VNet, conclua as etapas no artigo [Create a virtual network](virtual-networks-create-vnet-arm-ps.md) (Criar uma rede virtual).

    Para obter o nome das sub-redes na VNet, digite os seguintes comandos e substitua *Subnet1* acima pelo nome de uma sub-rede:
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Digite os seguintes comandos para recuperar a VNet e a sub-rede e atribuí-las a variáveis.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identifica um recurso de endereço IP público existente que pode ser associado ao adaptador de rede para que você possa conectá-lo pela Internet. Se não quiser acessar a VM com o adaptador de rede pela Internet, você poderá ignorar esta etapa. Sem um endereço IP público, você deve se conectar à VM usando outra VM conectada à mesma VNet. 

    Altere *PIP1* para o nome de um recurso de endereço IP público existente que esteja na localização em que você está criando o adaptador de rede e que não esteja atualmente associado a outro adaptador de rede. Se necessário, altere $rgName para o nome do grupo de recursos em que o recurso de endereço IP público existe e digite o seguinte comando:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Digite o seguinte comando se você não souber o nome de um recurso de endereço IP público existente:

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Se a coluna **IPConfiguration** não tiver nenhum valor na saída retornada, o recurso de endereço IP público não estará associado a um adaptador de rede existente e poderá ser usado. Se a lista estiver em branco ou não houver nenhum recurso de endereço IP público disponível, você poderá criar um usando o comando New-AzureRmPublicIPAddress.

    >[AZURE.NOTE] Endereços IP públicos têm um valor nominal. Saiba mais sobre preços lendo a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Se você tiver optado por não adicionar um recurso de endereço IP público à interface, remova *-PublicIPAddress $PIP1* no final do comando que se segue. Crie o adaptador de rede com rede acelerada digitando o seguinte comando:

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Atribua o adaptador de rede a uma máquina virtual ao criar a VM seguindo as instruções nas etapas 3 e 6 do artigo [Create a VM](../virtual-machines/virtual-machines-windows-ps-create.md) (Criar uma VM). Na etapa 6-2, substitua *Standard_A1* por um dos tamanhos de VM listados na seção [Limitações](#limitations) desse artigo.

    >[AZURE.NOTE] Se você tiver alterado o *nome* das variáveis $locName, $rgName ou $nic nesse artigo, a etapa 6 no artigo Create a VM (Criar uma VM) falhará. No entanto, você pode alterar os *valores* das variáveis.

12. Depois de criar a VM, baixe o [driver de Rede Acelerada](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conecte-se à VM e execute o instalador de driver dentro da VM.

13. Clique com o botão direito do mouse em Windows e clique em **Gerenciador de Dispositivos**. Verifique se **Adaptador Ethernet de Função Virtual Mellanox ConnectX-3** aparece na opção **Rede** quando expandida, conforme mostra a figura a seguir:

    ![Gerenciador de Dispositivos](./media/virtual-network-accelerated-networking-powershell/image2.png)


<!--HONumber=Oct16_HO2-->


