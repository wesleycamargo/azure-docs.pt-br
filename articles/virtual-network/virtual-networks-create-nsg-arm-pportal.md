<properties 
   pageTitle="Como criar NSGs no modo ARM usando o portal do Azure | Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no ARM usando o portal do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Como gerenciar NSGs usando o portal de visualização

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [criar NSGs no modelo de implantação clássica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Implantar o modelo ARM usando clique para implantar

Neste momento, você não pode criar um NSG por meio da visualização. No entanto, você pode gerenciar seus NSGs existentes. Para poder gerenciar NSGs, use o modelo de exemplo disponível no repositório público para criar os recursos descritos no cenário acima. Implante [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

## Criar regras em um NSG existente

Para criar regras em um NSG existente no portal de visualização, siga as etapas abaixo.

1. Em um navegador, vá até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Navegar>** > **Grupos de segurança de rede**.

![Portal de visualização - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. Na lista de NSGs, clique em **NSG-FrontEnd** > **Regras de segurança de entrada**

![Portal de visualização - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Na lista de **Regras de segurança de entrada**, clique em **Adicionar**.

![Portal de visualização - Adicionar regra](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Na folha **Adicionar regra de segurança de entrada**, crie uma regra chamada *web-rule* com prioridade de *200* permitindo o acesso via *TCP* à porta *80* a qualquer VM por meio de qualquer origem e, em seguida, clique em **OK**. Observe como a maioria destas configurações já é valor padrão.

![Portal de visualização - Configurações de regra](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Depois de alguns segundos, você encontrará a nova regra no NSG.

![Portal de visualização - Nova regra](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=AcomDC_0211_2016-->