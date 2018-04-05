---
title: Criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona usando o Portal do Azure | Microsoft Docs
description: Saiba como criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona com o Portal Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Criar um Load Balancer Standard público com frontend de endereço IP Público com redundância de zona usando o Portal do Azure

Este artigo segue as etapas para a criação de um [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) público com um front-end com redundância de zona usando um endereço IP Público Standard.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="register-for-availability-zones-preview"></a>Registre-se na versão prévia das Zonas de Disponibilidade
 
As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Criar um balanceador de carga com redundância de zona

1. Em um navegador, acesse o portal do Azure:[http://portal.azure.com](http://portal.azure.com) e entre com sua conta do Azure.
2. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**
3. Em **Criar balanceador de carga, em **Nome**, digite **myPublicLB**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **Standard (versão prévia)**.
6. Clique em **Endereço IP público**, clique em **Criar novo**, na página **Criar um endereço IP público**, em nome, digite **myPublicIPStandard** e, para **Zona de disponibilidade (versão prévia)**, selecione **Com redundância de zona**.
7. Em **Localização**, selecione **Leste dos EUA 2** e, em seguida, clique em **OK**. A implantação do balanceador de carga começará e levará alguns minutos para ser concluída com êxito.

    ![criar um Load Balancer Standard com redundância de zona com o Portal do Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Próximas etapas
- Saiba como [criar um IP público em uma zona de disponibilidade](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



