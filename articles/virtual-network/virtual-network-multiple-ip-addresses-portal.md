---
title: "Vários endereços IP para máquinas virtuais do Azure – Portal | Microsoft Docs"
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
ms.sourcegitcommit: 394315f81cf694cc2bb3a28b45694361b11e0670
ms.openlocfilehash: 6e7eac6ae505c627ffa1d63aace76b9006d92c74


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Atribuir vários endereços IP a máquinas virtuais usando o Portal do Azure

>[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
>
Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando o Portal do Azure. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

Se você quiser criar uma VM com vários endereços IP, crie-a usando o PowerShell ou a CLI do Azure. Para saber como fazer isso, clique nas opções PowerShell ou CLI na parte superior deste artigo. Você pode criar uma VM com um único endereço IP privado estático e (opcionalmente) um único endereço IP público usando o Portal e executando as etapas nos artigos [Criar uma VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM do Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Depois de criar a VM, você poderá alterar os tipos de endereço IP e adicionar outros endereços IP usando o Portal e executando as etapas na seção [Adicionar endereços IP a uma VM](#add) deste artigo.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados a um NIC executando as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#Scenario) neste artigo, mas isso não é obrigatório.

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>Principais etapas

1. Registre-se para obter a prévia executando os seguintes comandos do PowerShell após o logon e selecione a assinatura apropriada:
    ```
    Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    Não tente concluir as etapas restantes até ver a saída a seguir ao executar o comando ```Get-AzureRmProviderFeature```:
        
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------      
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >Isso pode levar alguns minutos.
    
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



<!--HONumber=Feb17_HO2-->


