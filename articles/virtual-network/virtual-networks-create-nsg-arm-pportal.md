---
title: Criar um grupo de segurança de rede - Portal do Azure | Microsoft Docs
description: Aprenda a criar e implantar grupos de segurança de rede usando o Portal do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793636"
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Criar um grupo de segurança de rede usando o Portal do Azure

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [criar NSGs no modelo de implantação clássica](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Criar o NSG NSG-FrontEnd
Para criar o NSG **NSG-FrontEnd** conforme mostrado no cenário, conclua as etapas a seguir:

1. Em um navegador, navegue até https://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Selecione **+ Criar um recurso >** > **Grupos de Segurança de Rede**.
   
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Em **Grupos de segurança de rede**, selecione **Adicionar**.
   
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Em **Criar grupo de segurança de rede**, crie um NSG nomeado *NSG-FrontEnd* no grupo de recursos *RG-NSG* e, em seguida, clique em **Criar**.
   
    ![Portal do Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Criar regras em um NSG existente
Para criar regras em um NSG existente no Portal do Azure, conclua as etapas a seguir:

1. Selecione **Todos os serviços** e, em seguida, pesquise **Grupos de segurança de rede**. Quando **Grupos de segurança de rede** for exibido, selecione-o.
2. Na lista dos NSGs, selecione **NSG-FrontEnd** > **Regras de segurança de entrada**
   
    ![Portal do Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Na lista das **Regras de segurança de entrada**, selecione **Adicionar**.
   
    ![Portal do Azure - Adicionar regra](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. Em **Adicionar regra de segurança de entrada**, crie uma regra nomeada *web-rule* com prioridade de *200* permitindo o acesso via *TCP* à porta *80* a qualquer VM por meio de qualquer origem e, em seguida, selecione **OK**. Observe como a maioria destas configurações já é o valor padrão.
   
    ![Portal do Azure - Configurações de regra](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Após alguns segundos, você verá a nova regra no NSG.
   
    ![Portal do Azure - Nova regra](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Repita as etapas até 6 para criar uma regra de entrada chamada *rdp-rule* com uma prioridade de *250* permitindo o acesso via *TCP* à porta *3389* a qualquer VM de qualquer origem.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associar o NSG à sub-rede FrontEnd

1. Selecione **Todos os serviços >**, insira **Grupos de recursos**, selecione **Grupos de recursos** quando for exibido, selecione **RG-NSG**.
2. Em **RG-NSG**, selecione **...** > **TestVNet**.
   
    ![Portal do Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Em **Configurações**, selecione **Sub-redes** > **FrontEnd** > **Grupo de segurança de rede** > **NSG-FrontEnd**.
   
    ![Portal do Azure - Configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Na folha **FrontEnd**, selecione **Salvar**.
   
    ![Portal do Azure - Configurações de sub-rede](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Criar o NSG NSG-BackEnd
Para criar o NSG **NSG-BackEnd** e associá-lo à sub-rede **BackEnd** conclua as etapas a seguir:

1. Para criar um NSG nomeado *NSG-BackEnd*, repita as etapas em [Criar o NSG NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG).
2. Para criar as regra de **entrada** na tabela a seguir, repita as etapas em [Criar regras em um NSG existente](#Create-rules-in-an-existing-NSG).
   
   | Regra de entrada | Regra de saída |
   | --- | --- |
   | ![Portal do Azure - regra de entrada](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portal do Azure - regra de saída](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Para associar o NSG **NSG-Backend** à sub-rede **BackEnd** repita as etapas em [Associe o NSG à sub-rede FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Próximas etapas
* Saiba como [gerenciar NSGs existentes](manage-network-security-group.md)
* [Habilite o registro em log](virtual-network-nsg-manage-log.md) para NSGs.