---
title: "Vários endereços IP para máquinas virtuais – Portal | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando o Portal do Azure | Resource Manager."
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
ms.date: 11/30/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 7ce83952f0f16e01a4837ce2155571d223d7b551
ms.openlocfilehash: b1a2549e0f04dbc00a47a57ecc888ca36339c646


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP a máquinas virtuais usando o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Uma VM (máquina virtual) do Azure tem um ou mais NICs (adaptadores de rede) conectados a ele. Qualquer NIC pode ter um ou mais endereços IP públicos e privados estáticos ou dinâmicos atribuídos a ele. A atribuição de vários endereços IP a uma VM permite as seguintes capacidades:

* Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
* Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
* A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md).

Todas as NICs anexadas a uma VM têm uma ou mais configurações IP associadas a elas. Cada configuração recebe um endereço IP privado estático ou dinâmico. Cada configuração também pode ter um recurso de endereço IP público associado a ele. Um recurso de endereço IP público tem um endereço IP dinâmico ou estático atribuído a ele. Para saber mais sobre endereços IP no Azure, leia o artigo [Endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md).

Este artigo explica como usar o Portal do Azure para atribuir vários endereços IP a uma VM criada por meio do modelo de implantação do Azure Resource Manager. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Cenário
Uma VM com uma única NIC é criada e conectada a uma rede virtual. A VM requer três endereços IP *privados* diferentes e dois endereços IP *públicos*. Os endereços IP são atribuídos às seguintes configurações de IP:

* **IPConfig-1:** atribui um endereço IP privado *dinâmico* (padrão) e um endereço IP público *estático*.
* **IPConfig-2:** atribui um endereço IP privado *estático* e um endereço IP público *estático*.
* **IPConfig-3:** atribui um endereço IP privado *dinâmico* e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

As configurações de IP são associadas à NIC quando a NIC é criada e a NIC é conectada à máquina Virtual quando a VM é criada. Os tipos de endereços IP usados para o cenário são para fins de ilustração. Você pode atribuir quaisquer tipos de atribuição e de endereço IP necessários.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

Se você quiser criar uma VM com vários endereços IP, crie-a usando o PowerShell ou a CLI do Azure. Para saber como fazer isso, clique nas opções PowerShell ou CLI na parte superior deste artigo. Você pode criar uma VM com um único endereço IP privado estático e (opcionalmente) um único endereço IP público usando o Portal e executando as etapas nos artigos [Criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM do Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Depois de criar a VM, você poderá alterar os tipos de endereço IP e adicionar outros endereços IP usando o Portal e executando as etapas na seção [Adicionar endereços IP a uma VM](#add) deste artigo.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Adicionar endereços IP a uma VM

Adicione endereços IP públicos e privados a um NIC executando as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#Scenario) neste artigo, mas isso não é obrigatório.

> [!NOTE]
> Embora este artigo atribua todas as configurações de IP a um único NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com várias NICs, leia o artigo [Criar uma VM com várias NICs](virtual-network-deploy-multinic-arm-ps.md).

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>Principais etapas

1. Registre-se para a visualização enviando um email para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não tente concluir as etapas restantes:
    - Até que você receba um email notificando de que foi aceito para a visualização
    - Sem seguir as instruções no email recebido
2. Navegue até o Portal do Azure em https://portal.azure.com e entre nele, se for necessário.
3. No portal, clique em **Mais serviços** > digite *máquinas virtuais* na caixa de filtro e clique em **Máquinas virtuais**.
4. Na folha **Máquinas virtuais**, clique na VM a qual você deseja adicionar os endereços IP. Clique em **Adaptador de rede** na folha da máquina virtual que aparece e selecione o adaptador de rede ao qual você deseja adicionar os endereços IP. No exemplo mostrado na figura a seguir, o NIC chamado *myNIC* da VM denominada *myVM* está selecionada:

    ![Interface de rede](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

5. Na folha que aparece para a NIC selecionada, clique em **Configurações de IP**, conforme mostra a imagem a seguir:

    ![Configurações de IP](./media/virtual-network-multiple-ip-addresses-portal/figure2.png)

Conclua as etapas em uma das seções a seguir com base no tipo de endereço IP que você deseja adicionar.

### <a name="add-a-private-ip-address"></a>**Adicionar um endereço IP privado**

Conclua as etapas a seguir para adicionar um novo endereço IP privado:

1. Conclua as etapas na seção [Principais etapas](#coreadd) deste artigo.
2. Clique em **Adicionar**. Na folha **Adicionar configuração de IP** que aparece, crie uma configuração de IP chamada *IPConfig-4* com *10.0.0.7* como um endereço IP privado *Estático* e clique em **OK**, conforme mostra a imagem a seguir:

    ![Adicionar IP privado](./media/virtual-network-multiple-ip-addresses-portal/figure3.png)

    > [!NOTE]
    > Ao adicionar um endereço IP estático, você deve especificar um endereço válido, não utilizado, na sub-rede a qual a NIC está conectada. Se o endereço selecionado não estiver disponível, o portal mostrará um X para o endereço IP, e você precisará selecionar outro.

    Se você preferir que o **Método de alocação** do endereço IP privado seja *Dinâmico*, selecione essa opção e não será necessário especificar um endereço IP.
3. Ao clicar em OK, a folha será fechada e você verá a nova configuração de IP listada, conforme mostra a imagem a seguir:

    ![Configurações de IP](./media/virtual-network-multiple-ip-addresses-portal/figure4.png)

    Clique em **OK** para fechar a folha **Adicionar Configuração de IP**.
4. Clique em **Adicionar** para adicionar outras configurações de IP ou feche todas as folhas abertas para concluir a adição de endereços IP.
5. Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

### <a name="add-a-public-ip-address"></a>Adicionar um endereço IP público

Um endereço IP público é adicionado por meio da associação de um recurso de endereço IP público a uma nova configuração de IP ou a uma configuração de IP existente.

> [!NOTE]
> Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="a-namecreate-public-ipacreate-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Criar um recurso de endereço IP público

Um endereço IP público é uma configuração para um recurso de endereço IP público. Se você tiver um recurso de endereço IP público que não está associado no momento a uma configuração de IP, e você deseja associá-lo, ignore as etapas a seguir e conclua as etapas em uma das seções seguintes, de acordo com sua necessidade. Se você não tiver um recurso de endereço IP público disponível, conclua as seguintes etapas para criar um:

1. Navegue até o Portal do Azure em https://portal.azure.com e entre nele, se for necessário.
3. No portal, clique em **Novo** > **Rede** > **Endereço IP público**.
4. Na folha **Criar endereço IP público** que aparece, insira um **Nome**, selecione um tipo de **Atribuição de endereço IP**, uma **Assinatura**, um **Grupo de recursos** e um **Local**, em seguida, clique em **Criar**, conforme mostra a imagem a seguir:

    ![Criar um recurso de endereço IP público](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Conclua as etapas em uma das seções a seguir para associar o recurso de endereço IP público a uma configuração de IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associar o recurso de endereço IP público a uma nova configuração de IP

1. Conclua as etapas na seção [Principais etapas](#coreadd) deste artigo.
2. Clique em **Adicionar**. Na folha **Adicionar configuração de IP** que aparece, crie uma configuração de IP chamada *IPConfig-4*. Habilite o **Endereço IP público** e selecione um recurso de endereço IP público existente e disponível na folha **Escolher endereço IP público** que aparece, como mostra a imagem a seguir:

    ![Nova configuração de IP](./media/virtual-network-multiple-ip-addresses-portal/figure6.png)

    Depois de selecionar o recurso de endereço IP público, clique em **OK** e a folha será fechada. Se você não tiver um endereço IP público existente, crie um usando as etapas na seção [Criar um recurso de endereço IP público](#create-public-ip) deste artigo. 

3. Analise a nova configuração de IP, conforme mostra a imagem a seguir:

    ![Configurações de IP](./media/virtual-network-multiple-ip-addresses-portal/figure7.png)

    > [!NOTE]
    > Apesar de um endereço IP privado não ter sido explicitamente atribuído, um foi atribuído automaticamente para a configuração de IP, pois todas as configurações de IP devem ter um endereço IP privado.
    >

4. Clique em **Adicionar** para adicionar outras configurações de IP ou feche todas as folhas abertas para concluir a adição de endereços IP.
5. Adicione o endereço IP privado ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo. Não adicione o endereço IP público ao sistema operacional.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associar o recurso de endereço IP público a uma configuração de IP existente

1. Conclua as etapas na seção [Principais etapas](#coreadd) deste artigo.
2. Selecione a configuração de IP a qual você deseja adicionar o recurso de endereço IP público, habilite o endereço IP público e selecione um recurso de endereço IP público existente e disponível. No exemplo da imagem a seguir, o recurso de endereço IP público *myPublicIp3* está associado a *IPConfig-3*.

    ![Configuração de IP existente](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    Depois de selecionar o recurso de endereço IP público, clique em **Salvar** e a folha será fechada. Se você não tiver um endereço IP público existente, crie um usando as etapas na seção [Criar um recurso de endereço IP público](#create-public-ip) deste artigo.

3. Analise a nova configuração de IP, conforme mostra a imagem a seguir:

    ![Configurações de IP](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. Clique em **Adicionar** para adicionar outras configurações de IP ou feche todas as folhas abertas para concluir a adição de endereços IP. Não adicione o endereço IP público ao sistema operacional.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->


