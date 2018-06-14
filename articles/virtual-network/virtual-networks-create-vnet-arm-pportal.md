---
title: Criar uma Rede Virtual do Azure com várias sub-redes - Portal | Microsoft Docs
description: Saiba como criar uma rede virtual com várias sub-redes usando o Portal do Azure.
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
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880476"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Criar uma rede virtual com várias sub-redes usando o Portal do Azure

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se com a Internet e em modo privado. Criar várias sub-redes em uma rede virtual permite segmentar a rede para que seja possível filtrar ou controlar o fluxo de tráfego entre as sub-redes. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar uma sub-rede
> * Testar a comunicação de rede entre máquinas virtuais

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Conforme mostrado na imagem a seguir, insira *myVirtualNetwork* para **Nome**, *10.0.0.0/16* para **Espaço de endereço**, **myResourceGroup** para **Grupo de recursos**, *Public* para **Nome** da Sub-rede, 10.0.0.0/24 para **Intervalo de endereços** da Sub-rede, selecione um **Local** e a **Assinatura**, aceite os padrões restantes e selecione **Criar**:

    ![Criar uma rede virtual](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    O **Espaço de endereço** e **Intervalo de Endereços** são especificados na notação CIDR. O **Espaço de endereço** especificado inclui os endereços IP 10.0.0.0-10.0.255.254. O **Intervalo de Endereços** especificado para uma sub-rede, deve estar dentro do **Espaço de endereço** definido para a rede virtual. O DHCP do Azure atribui endereços IP de um intervalo de endereços de sub-rede aos recursos implantados em uma sub-rede. O Azure atribui apenas os endereços 10.0.0.4-10.0.0.254 aos recursos implantados na sub-rede **Pública**, porque o Azure reserva os quatro primeiros endereços (10.0.0.0-10.0.0.3 para a sub-rede, neste exemplo) e o último endereço (10.0.0.255 para a sub-rede, neste exemplo) em cada sub-rede.

## <a name="create-a-subnet"></a>Criar uma sub-rede

1. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, comece digitando *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Selecione **Sub-redes** e, em seguida, selecione **+ Sub-rede**, conforme mostrado na imagem a seguir:

     ![Adicionar uma sub-rede](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. Na caixa **Adicionar sub-rede** que aparece, insira *Privado*  para **Nome**, insira *10.0.1.0/24* para **Intervalo de endereços** e, em seguida, selecione **OK**.  Um intervalo de endereços de sub-rede não pode sobrepor-se com os intervalos de endereços de outras sub-redes dentro de uma rede virtual. 

Antes de implantar sub-redes e redes virtuais do Azure para uso em produção, é recomendável que você esteja familiarizado com as [considerações](manage-virtual-network.md#create-a-virtual-network) de espaço de endereço e os [limites da rede virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Quando os recursos forem implantados em sub-redes, algumas mudanças na rede virtual e na sub-rede, como a alteração de intervalos de endereços, poderão exigir a reimplantação dos recursos do Azure existentes implantados nas sub-redes.

## <a name="test-network-communication"></a>Testar comunicação de rede

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se com a Internet e em modo privado. Um tipo de recurso que pode ser implantado em uma rede virtual é uma máquina virtual. Crie duas máquinas virtuais na rede virtual para que seja possível testar a comunicação de rede entre elas e a Internet em uma etapa posterior.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. É possível selecionar um sistema operacional diferente, mas as etapas restantes assumirão que está selecionado o **Windows Server 2016 Datacenter**. 
3. Selecione ou insira as informações a seguir para **Básico** e, em seguida, selecione **OK**:
    - **Nome**: *myVmWeb*
    - **Grupo de recursos**: selecione **Usar existente** e, em seguida, selecione *myResourceGroup*.
    - **Localização**: selecione *Leste dos EUA*.

    O **Nome de usuário** e **Senha** inseridos serão utilizados em uma etapa posterior. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). A **Localização** e **Assinatura** selecionadas deverão ser iguais à localização e assinatura da rede virtual onde estão. Não é necessário selecionar o mesmo grupo de recursos em que a rede virtual foi criada, porém, o mesmo grupo de recursos foi selecionado para este tutorial.
4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione ou insira as informações a seguir para **Configurações** e, em seguida, selecione **OK**:
    - **Rede virtual**: certifique-se de que **myVirtualNetwork** está selecionada. Se não, selecione a **Rede Virtual** e, em seguida, selecione **myVirtualNetwork** em **Escolher rede virtual**.
    - **Sub-rede**: certifique-se de que está selecionado **Público**. Caso contrário, selecione **Sub-rede** e, em seguida, selecione **Público** em **Escolhe sub-rede**, conforme mostrado na imagem a seguir:
    
        ![Configurações da máquina virtual](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da máquina virtual.
7. Complete as etapas 1 a 6 novamente, mas insira*myVmMgmt* para o **Nome** da máquina virtual e selecione **Privado** para a **Sub-rede**.

A criação das máquinas virtuais demora alguns minutos. Não continue com as etapas restantes até que ambas as máquinas virtuais sejam criadas.

As máquinas virtuais criadas neste artigo possuem uma [interface de rede](virtual-network-network-interface.md) com um endereço IP dinamicamente atribuído à interface de rede. Após implantar a VM, será possível [adicionar vários endereços IP públicos e privados ou alterar o método de atribuição de endereço IP para estático](virtual-network-network-interface-addresses.md#add-ip-addresses). É possível [adicionar interfaces de rede](virtual-network-network-interface-vm.md#vm-add-nic), até o limite suportado pelo [tamanho da VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que você seleciona ao criar uma máquina virtual. Além disso, é possível [habilitar a SR-IOV (virtualização de E/S de raiz única)](create-vm-accelerated-networking-powershell.md) para uma VM, mas somente quando você cria uma VM com um tamanho da VM que suporta a capacidade.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicação entre máquinas virtuais e Internet

1. Nas caixa *Pesquisar* na parte superior do portal, comece digitando *myVmMgmt*. Quando **myVmMgmt** aparecer na caixa de resultados, selecione-o.
2. Crie uma conexão de área de trabalho remota para a máquina virtual *myVmMgmt*, selecionando **Conectar**, conforme mostrado na imagem a seguir:

    ![Conectar-se à máquina virtual](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Para conectar-se à VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
4. Insira o nome de usuário e senha especificados ao criar a máquina virtual (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a máquina virtual) e selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
6. Em uma etapa posterior, o ping será usado para comunicar-se com a máquina virtual *myVmMgmt* da máquina virtual *myVmWeb*. O ping usa ICMP, que é negado através do Firewall do Windows, por padrão. Habilite o ICMP através do firewall do Windows, inserindo o seguinte comando a partir do prompt de comando:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Embora o ping seja usado neste artigo, não é recomendável ICMP através do Firewall do Windows para implantações de produção.
7. Por razões de segurança, é comum limitar o número de máquinas virtuais que podem ser conectadas remotamente em uma rede virtual. Neste tutorial, a máquina virtual *myVmMgmt* é usada para gerenciar a máquina virtual *myVmWeb* na rede virtual. Para área de trabalho remota para a máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*, insira o seguinte comando a partir de um prompt de comando:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Para comunicar-se com a máquina virtual *myVmMgmt* a partir da máquina virtual *myVmWeb*, insira o comando a seguir em um prompt de comando:

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
      
    É possível ver que o endereço da máquina virtual *myVmMgmt* é 10.0.1.4. 10.0.1.4 foi o primeiro endereço IP disponível no intervalo de endereços da sub-rede *Privada* implantada na máquina virtual *myVmMgmt* em uma etapa anterior.  Observe que o nome de domínio totalmente qualificado da máquina virtual é *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Embora a parte *dar5p44cif3ulfq00wxznl3i3f* do nome de domínio seja diferente para a máquina virtual, as partes restantes do nome de domínio serão as mesmas. 

    Por padrão, todas as máquinas virtuais do Azure usam o serviço DNS do Azure padrão. Todas as máquinas virtuais dentro de uma rede virtual podem resolver os nomes de todas as outras máquinas virtuais na mesma rede virtual usando o serviço DNS padrão do Azure. Em vez de usar o serviço DNS do Azure padrão, é possível usar seu próprio servidor DNS ou a capacidade de domínio privado do serviço DNS do Azure. Para obter detalhes, consulte [Resolução de nome usando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ou [Usar DNS do Azure para domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Para instalar o IIS (Serviços de Informações da Internet) para Windows Server na *myVmWeb*, insira o comando a seguir de uma sessão do PowerShell:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. Após a conclusão da instalação do IIS, desconecte a sessão de área de trabalho remota da *myVmWeb* que direciona para a sessão de área de trabalho remota da *myVmMgmt*. Abra um navegador da Web e navegue para http://myvmweb. Você visualiza a página de boas-vindas do IIS.
11. Desconecte a sessão de área de trabalho remota da *myVmMgmt*.
12. Localize o endereço IP público da máquina virtual *myVmWeb*. Quando o Azure criou a máquina virtual *myVmWeb*, um recurso de endereço IP público nomeado *myVmWeb* também foi criado e atribuído à máquina virtual. É possível observar que 52.170.5.92 foi atribuído para **Endereço IP público** para a máquina virtual *myVmMgmt* na imagem na etapa 2. Para localizar o endereço IP público atribuído à máquina virtual *myVmWeb*, procure *myVmWeb* na caixa de pesquisa do portal e, em seguida, selecione-o quando aparecer nos resultados da pesquisa.

    Embora uma máquina virtual não seja necessária para ter um endereço IP público atribuído a ela, o Azure atribui um endereço IP público a cada máquina virtual criada, por padrão. Para comunicar-se da Internet para uma máquina virtual, um endereço IP público deverá ser atribuído à máquina virtual. Todas as máquinas virtuais podem se comunicar com a Internet, tendo ou não um endereço IP público atribuído à máquina virtual. Para saber mais sobre as conexões com a Internet de saída no Azure, consulte [Conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. No seu próprio computador, navegue até o endereço IP público da máquina virtual *myVmWeb*. A tentativa de visualizar a página de boas-vindas do IIS de seu próprio computador falha. A tentativa falha porque, quando as máquinas virtuais foram implantadas, o Azure criou um grupo de segurança de rede para cada máquina virtual, por padrão. 

     Um grupo de segurança de rede contém regras de segurança que permitem ou negam tráfego de entrada e saída pela porta e pelo endereço IP. O grupo de segurança de rede padrão do Azure criado permite a comunicação entre todas as portas entre recursos na mesma rede virtual. Para máquinas virtuais do Windows, o grupo de segurança de rede padrão nega todo o tráfego de entrada da Internet em todas as portas, exceto a porta TCP 3389 (RDP). Como resultado, por padrão, também é possível RDP diretamente para a máquina virtual *myVmWeb* da Internet, mesmo que você não queira a porta 3389 aberta para um servidor Web. Como a navegação na Web comunica-se através da porta 80, a comunicação falha a partir da Internet porque não há nenhuma regra no grupo de segurança de rede padrão que permite o tráfego na porta 80.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como implantar uma rede virtual com várias sub-redes. Adicionalmente, aprendeu que ao criar uma máquina virtual do Windows, o Azure cria uma interface de rede que anexa à máquina virtual e cria um grupo de segurança de rede que permite somente tráfego na porta 3389, da Internet. Avance para o próximo tutorial para aprender como filtrar tráfego para sub-redes, e não para máquinas virtuais individuais.

> [!div class="nextstepaction"]
> [Filtrar tráfego para sub-redes](./virtual-networks-create-nsg-arm-pportal.md)
