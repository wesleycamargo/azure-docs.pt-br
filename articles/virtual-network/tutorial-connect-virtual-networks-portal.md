---
title: Conectar redes virtuais ao emparelhamento de rede virtual – Portal do Azure | Microsoft Docs
description: Saiba como conectar redes virtuais ao o emparelhamento de rede virtual.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Conectar redes virtuais ao emparelhamento de rede virtual usando o portal do Azure

Você pode conectar redes virtuais entre si ao emparelhamento de rede virtual. Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Este artigo aborda a criação e o emparelhamento de duas redes virtuais. Você aprenderá como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Criar um emparelhamento entre redes virtuais
> * Testar o emparelhamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-virtual-networks"></a>Criar redes virtuais

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Conforme mostrado na imagem a seguir, insira *myVirtualNetwork1* para **Nome**, *10.0.0.0/16* para **Espaço de endereço**, **myResourceGroup** para **Grupo de recursos**, *Sub-rede 1* para **Nome** da Sub-rede, 10.0.0.0/24 para **Intervalo de endereços** da Sub-rede, selecione um **Local** e a **Assinatura**, aceite os padrões restantes e selecione **Criar**:

    ![Criar uma rede virtual](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Conclua as etapas 1 a 3 novamente, com as seguintes alterações:
    - **Nome**: *myVirtualNetwork2*
    - **Grupo de recursos**: selecione **Usar existente** e, em seguida, selecione **myResourceGroup**.
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*

    O prefixo de endereço para a rede virtual *myVirtualNetwork2* não sobrepõe se sobrepõe ao espaço de endereço da rede virtual *myVirtualNetwork1*. Não é possível emparelhar redes virtuais aos espaços de endereço sobrepostos.

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

1. Nas caixa Pesquisar na parte superior do portal, comece digitando *MyVirtualNetwork1*. Quando **myVirtualNetwork1** aparecer nos resultados da pesquisa, selecione-o.
2. Em **Emparelhamentos**, selecione **CONFIGURAÇÕES** e, em seguida, selecione **+ Add**, como mostrado na imagem a seguir:

    ![Criar emparelhamento](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Insira ou selecione as informações mostradas na imagem a seguir, em seguida selecione **OK**. Para selecionar a rede virtual *myVirtualNetwork2*, selecione **Rede virtual**, em seguida, selecione *myVirtualNetwork2*.

    ![Configurações de emparelhamento](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    O **STATUS DO EMPARELHAMENTO** é *iniciadi*, conforme mostrado na figura a seguir:

    ![Status de emparelhamento](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Se você não vir o status, atualize seu navegador.

4. Pesquise a rede virtual *myVirtualNetwork2*. Quando você a encontrar nos resultados da pesquisa, selecione-a.
5. Conclua as etapas 1 a 3 novamente, com as seguintes alterações, e em seguida, selecione **OK**:
    - **Nome**: *myVirtualNetwork2-myVirtualNetwork1*
    - **Rede virtual**: *myVirtualNetwork1*

    O **ESTADO DE EMPARELHAMENTO** é *Conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork2-myVirtualNetwork1* de *Iniciado* para *Conectado.* O emparelhamento de rede virtual não é estabelecido com êxito até que o status de emparelhamento para ambas as redes virtuais seja *Conectado.* 

Os emparelhamentos são feitos entre duas redes virtuais, mas não são transitivos. Assim, por exemplo, se você desejar emparelhar *myVirtualNetwork2* com *myVirtualNetwork3*, precisará criar um emparelhamento adicional entre as redes virtuais *myVirtualNetwork2* e *myVirtualNetwork3*. Embora *myVirtualNetwork1* esteja emparelhada com *myVirtualNetwork2*, os recursos de *myVirtualNetwork1* só podem acessar os recursos de *myVirtualNetwork3* se *myVirtualNetwork1* também foi emparelhada com *myVirtualNetwork3*. 

Antes de emparelhar redes virtuais de produção, é recomendável que você se familiarize por completo com a [visão geral do emparelhamento](virtual-network-peering-overview.md), o [gerenciamento do emparelhamento](virtual-network-manage-peering.md) e os [limites da rede virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Embora este artigo ilustre um emparelhamento entre duas redes virtuais na mesma assinatura e no mesmo local, você também pode emparelhar redes virtuais em [regiões diferentes](#register) e [assinaturas diferentes do Azure](create-peering-different-subscriptions.md#portal). Você também pode criar [designs de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento.

## <a name="test-peering"></a>Testar o emparelhamento

Para testar a comunicação de rede entre máquinas virtuais em diferentes redes virtuais por meio de um emparelhamento, implante uma máquina virtual em cada sub-rede e, em seguida, estabeleça a comunicação entre as máquinas virtuais. 

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma máquina virtual em cada rede virtual para que você possa validar a comunicação entre elas em uma etapa posterior.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. É possível selecionar um sistema operacional diferente, mas as etapas restantes assumirão que está selecionado o **Windows Server 2016 Datacenter**. 
3. Selecione ou insira as informações a seguir para **Básico** e, em seguida, selecione **OK**:
    - **Nome**: *myVm1*
    - **Grupo de recursos**: selecione **Usar existente** e, em seguida, selecione *myResourceGroup*.
    - **Localização**: selecione *Leste dos EUA*.

    O **Nome de usuário** e **Senha** inseridos serão utilizados em uma etapa posterior. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). A **Localização** e **Assinatura** selecionadas deverão ser iguais à localização e assinatura da rede virtual onde estão. Não é necessário selecionar o mesmo grupo de recursos em que a rede virtual foi criada, porém, o mesmo grupo de recursos foi selecionado para este artigo.
4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione ou insira as informações a seguir para **Configurações** e, em seguida, selecione **OK**:
    - **Rede virtual**: certifique-se de que **myVirtualNetwork1** está selecionada. Se não, selecione a **Rede Virtual** e, em seguida, selecione **myVirtualNetwork1** em **Escolher rede virtual**.
    - **Sub-rede**: certifique-se de que está selecionado **Sub-rede1**. Caso contrário, selecione **Sub-rede** e, em seguida, selecione **Sub-rede1** em **Escolher sub-rede**, conforme mostrado na imagem a seguir:
    
        ![Configurações da máquina virtual](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da máquina virtual.
7. Complete as etapas 1 a 6 novamente, mas insira*myVm2* para o **Nome** da máquina virtual e selecione *myVirtualNetwork2* para a **Rede Virtual**.

O Azure atribuiu *10.0.0.4* como o endereço de IP privado à máquina virtual *myVm1* e 10.1.0.4 à máquina virtual *myVm2*, porque foram os primeiros endereços de IP disponíveis na *Sub-rede 1* das redes virtuais *myVirtualNetwork1* e *myVirtualNetwork2*, respectivamente.

A criação das máquinas virtuais demora alguns minutos. Não continue com as etapas restantes até que ambas as máquinas virtuais sejam criadas.

### <a name="test-virtual-machine-communication"></a>Testar a comunicação entre máquinas virtuais

1. Na caixa *Pesquisar* na parte superior do portal, comece digitando *myVm1*. Selecione **myVm1** quando aparecer nos resultados da pesquisa.
2. Crie uma conexão de área de trabalho remota para a máquina virtual *myVm1*, selecionando **Conectar**, conforme mostrado na imagem a seguir:

    ![Conectar-se à máquina virtual](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Para conectar-se à VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
4. Insira o nome de usuário e senha especificados ao criar a máquina virtual (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a máquina virtual) e selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
6. Em uma etapa posterior, o ping será usado para comunicar-se com a máquina virtual *myVm2* da máquina virtual *myVmWeb*. O ping usa ICMP, que é negado através do Firewall do Windows, por padrão. Habilite o ICMP através do firewall do Windows, inserindo o seguinte comando a partir do prompt de comando:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Embora o ping seja usado neste artigo para testes, não é recomendável ICMP através do Firewall do Windows para implantações de produção.

7. Para conectar a máquina virtual *myVm2*, insira o seguinte comando a partir de um prompt de comando na máquina virtual *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Desde que você tenha habilitado o ping em *myVm1*, você pode agora executar o ping-lo pelo endereço IP:

    ```
    ping 10.0.0.4
    ```
    
    Você receberá quatro respostas. Se você executar ping pelo nome da máquina virtual (*myVm1*) em vez de pelo endereço IP, o ping falhará, porque *myVm1* é um nome de host desconhecido. A resolução de nomes padrão do Azure funciona entre máquinas virtuais na mesma rede virtual, mas não entre máquinas virtuais em redes virtuais diferentes. Para resolver nomes entre redes virtuais, você precisa [implantar seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) ou usar [domínios privados do DNS do Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Desconecte as sessões RDP para ambas *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

**<a name="register"></a>Registrar-se para a versão prévia de emparelhamento de rede virtual global**

O emparelhamento de redes virtuais na mesma região está disponível ao público em geral. Emparelhar redes virtuais em diferentes regiões está em versão prévia no momento. Consulte [Atualizações de rede virtual](https://azure.microsoft.com/updates/?product=virtual-network) para as regiões disponíveis. Para emparelhar redes virtuais entre regiões, você deve primeiro registrar para a versão prévia. Você não pode registrar usando o portal, mas pode registrar usando o [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) ou [CLI do Azure](tutorial-connect-virtual-networks-cli.md#register). Se você tentar emparelhar redes virtuais em regiões diferentes de mesmo nível antes de registrar o recurso, o emparelhamento falhará.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a conectar duas redes com o emparelhamento de rede virtual.

Continue a conectar seu próprio computador a uma rede virtual por meio de um VPN e intereja com os recursos de uma rede virtual ou de redes virtuais emparelhadas.

> [!div class="nextstepaction"]
> [Conectar seu computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
