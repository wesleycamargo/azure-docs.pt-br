---
title: "Criar uma máquina virtual com um endereço IP público estático - Portal do Azure | Microsoft Docs"
description: "Saiba como criar uma VM com um endereço IP público estático usando o Portal do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 53e9b55a82e7750393dfb6c4818681028c5c4d2f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Como criar uma VM com um endereço IP público estático usando o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI do Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modelo](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do modelo de implantação clássico.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Criar uma VM com um IP público estático

Para criar uma VM com um endereço IP público estático no Portal do Azure, siga as seguintes etapas:

1. Em um navegador, navegue até o [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. No canto superior esquerdo do portal, clique em **Criar um recurso**>>**Computação**>**Windows Server 2012 R2 Datacenter**.
3. Na lista **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** e clique em **Criar**.
4. No painel **Noções Básicas**, insira as informações da VM conforme a seguir e clique em **OK**.
   
    ![Portal do Azure - Noções básicas](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. No painel **Escolha um tamanho**, clique em **A1 Standard** conforme a seguir e clique em **Selecionar**.
   
    ![Portal do Azure - Escolha um tamanho](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. No painel **Configurações**, clique em **Endereço IP público**, em seguida, no painel **Criar endereço IP público** em **Atribuição**, e clique em **Estático** conforme mostrado a seguir. E, em seguida, clique em **OK**.
   
    ![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. No painel **Configurações**, clique em **OK**.
8. Examine o painel **Resumo**, conforme a seguir e clique **OK**.
   
    ![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Observe o novo bloco no seu painel.
   
    ![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Quando a VM é criada, o painel **Configurações** exibe o seguinte:
    
    ![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

