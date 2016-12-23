---
title: "Vários endereços IP para máquinas virtuais – Portal | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 716046d5e23de015da42f25f8a1f674de228efac
ms.openlocfilehash: a04192ffde57ae38d901a78a74724a89f43caedb


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Atribuir vários endereços IP a máquinas virtuais
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)


Uma VM (máquina virtual) do Azure pode ter uma ou mais NICs (adaptadores de rede) conectadas a ele. Qualquer NIC pode ter um ou mais endereços IP públicos ou privados atribuídos a ela. Se você não está familiarizado com endereços IP no Azure, leia o artigo [endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md) para saber mais sobre eles. Este artigo explica como usar o Portal do Azure para atribuir vários endereços IP a uma VM no modelo de implantação do Azure Resource Manager.

A atribuição de vários endereços IP a uma VM permite as seguintes capacidades:

* Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
* Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
* A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrar-se para a preview, envie um email para [vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido.

## <a name="scenario"></a>Cenário
Neste artigo, você associará três configurações de IP a uma interface de rede.
Os seguintes exemplos de configuração serão criados e atribuídos a uma NIC que terá três endereços IP privados e um endereço IP público atribuído a ela:

* IPConfig-1: um endereço IP privado dinâmico (padrão) e um endereço IP público do recurso do endereço IP público denominado PIP1.
* IPConfig-2: um endereço IP privado estático e nenhum endereço IP público.
* IPConfig-3: um endereço IP privado dinâmico e nenhum endereço IP público.

    ![Texto Alt da imagem](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Este cenário pressupõe que você tem um grupo de recursos chamado *RG1* np qual existe uma VNet chamada *VNet1* e uma sub-rede chamada *Subnet1*. Além disso, ele pressupõe que você tem uma VM chamada *VM1*, uma interface de rede chamada *VM1-NIC1* associada à essa VM e um endereço IP público chamado *PIP1*.

[Este artigo](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) explica como criar os recursos mencionados acima, caso você não os tenha criado anteriormente.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP
Para criar várias configurações de IP com base no cenário anterior, usando a Versão Prévia do Portal do Azure, execute as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Selecione a interface de rede a qual você deseja adicionar as configurações de IP: **Máquinas Virtuais** > **VM1** > **Interfaces de rede** > **VM1 - NIC1**
3. Na folha **Interface de rede**, selecione **Configurações de IP**. Você verá uma lista das configurações de IP existentes.
4. Para associar **PIP1** a **ipconfig1**, na folha **Configurações de IP**, selecione **ipconfig1**. Na folha **ipconfig1**, em **Endereço IP público**, selecione **Habilitado**.
5. Na guia **Endereço IP**, selecione **Definir as configurações necessárias** e, em seguida, selecione **PIP1** ou o endereço IP público que você gostaria de associar a essa configuração de IP primário e, depois, clique em Salvar. Também é possível criar um novo endereço IP público para associar aqui. Em seguida, você verá uma notificação de Economia de interface de rede e, quando isso for concluído, você verá o **PIP1** associado a **ipconfig1**.

    ![Texto Alt da imagem](media\\virtual-network-multiple-ip-addresses-portal\\01-portal.PNG)

    >[!NOTE] 
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

1. Depois, para adicionar uma configuração de IP, na seção **Configurações de IP** de sua interface de rede, clique em **+Adicionar**.

   > [!NOTE]
   > Você pode atribuir até 250 endereços IP privado a uma NIC. Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager) .
   >
   >
2. Na folha **Adicionar configuração de IP**, nomeie sua configuração de IP. Aqui está, **IPConfig-2**. Selecione **Estático** para **Alocação** e digite o endereço IP que você deseja. Neste cenário, é 10.0.0.5. Em seguida, clique em **OK**. Após a gravação da configuração, você verá a configuração de IP na lista.

    ![Texto Alt da imagem](media\\virtual-network-multiple-ip-addresses-portal\\02-portal.PNG)
3. Em seguida, adicione a terceira configuração de IP selecionando **+Adicionar** na folha **Configurações de IP** e preencha os detalhes necessários, conforme mostrado abaixo. Depois, selecione **OK**.

    ![Texto Alt da imagem](media\\virtual-network-multiple-ip-addresses-portal\\03-portal.PNG)

    Observe que IPConfig-2 e IPConfig-3 também podem ser associados a um IP público selecionando **Habilitado** na seção **Endereço IP público** na folha **Adicionar configuração IP**. Um novo IP público pode ser criado, ou um endereço já criado pode ser associado à configuração de IP.
4. Adicione manualmente todos os endereços IP privados (incluindo o primário) para a configuração de TCP/IP no sistema operacional, conforme exibido abaixo. Para adicionar manualmente os endereços IP, você deve conectar sua VM e seguir as etapas descritas abaixo.

**Windows**

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Em seguida, digite *ncpa. cpl* na janela do prompt de comando. Isso abrirá uma nova janela.
3. Abra as propriedades de **Rede local**.
4. Clique duas vezes em Protocolo IP versão 4 (IPv4)
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:

   * **Endereço IP**: insira o endereço IP privado *primário*
   * **Máscara de sub-rede**: defina com base na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
   * **Gateway padrão**: o primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
   * Clique em **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
     * **Servidor DNS preferencial:** digite 168.63.129.16 se você não estiver usando seu próprio servidor DNS.  Se você estiver, digite o endereço IP do seu servidor DNS.
   * Clique no botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários listados na etapa 8 à NIC com a mesma sub-rede especificada para o endereço IP primário.
   * Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. Isso restabelecerá a conexão de RDP.
6. Em um prompt de comando, digite *ipconfig /all*. Todos os endereços IP que você adicionou são mostrados e o DHCP está desativado.

**Linux (Ubuntu)**

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se você não for, você poderá fazer isso usando o seguinte comando:

            sudo -i
3. Atualize o arquivo de configuração do adaptador de rede (supondo que 'eth0').

   * Mantenha o item de linha existente para o dhcp. Isso configurará o endereço IP primário para como ele estava anteriormente.
   * Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

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
7. Reinicie o adaptador de rede com o seguinte comando:

           sudo ifdown eth0 && sudo ifup eth0

   > [!IMPORTANT]
   > Execute ifdown e ifup na mesma linha se você estiver usando uma conexão remota.
   >
   >
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

## <a name="a-nameaddaadd-ip-addresses-to-an-existing-vm"></a><a name="add"></a>Adicione os endereços IP a uma VM existente
Siga as seguintes etapas para adicionar mais endereços IP a uma NIC existente:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Selecione a interface de rede a qual você deseja adicionar as configurações de IP na seção **Interfaces de rede**.
3. Na folha **Interface de rede**, selecione **Configurações de IP**. Você verá uma lista das configurações de IP existentes.
4. Depois, para adicionar uma configuração de IP, na seção Configurações de IP de sua interface de rede, clique em **+Adicionar**.
5. Na folha Adicionar configuração de IP, nomeie sua configuração de IP. Selecione o tipo de endereço IP que você gostaria, Estático ou Dinâmico, em **Alocação**. Em **Endereço IP público**, selecione **Habilitado** se você quiser associar a uma configuração de IP a um IP público. Caso contrário, clique em **Desabilitado**. Selecionar **Habilitado** permite que você associe um IP público existente à sua configuração, depois clique em OK. Após a gravação da configuração, você verá a configuração de IP na lista.



<!--HONumber=Nov16_HO3-->


