<properties 
   pageTitle="Implantar uma VM com um IP público estático usando o portal de visualização no Gerenciador de Recursos | Microsoft Azure"
   description="Saiba como implantar VMs com um IP público estático usando o portal de visualização no Gerenciador de Recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
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
   ms.date="01/08/2015"
   ms.author="telmos" />

# Implantar uma VM com um IP público estático usando o Portal do Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Criar uma VM com um IP público estático 

Para criar uma VM com um endereço IP público estático no Portal do Azure, siga as etapas abaixo.

1. Em um navegador, vá até o [Portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. No canto superior esquerdo do portal, clique em **Novo**>>**Computação**>**Windows Server 2012 R2 Datacenter**.
3. Na lista **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** e clique em **Criar**.
4. Na folha **Noções Básicas**, insira as informações da VM conforme mostrado abaixo e, em seguida, clique em **OK**.

	![Portal do Azure - Noções básicas](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Na folha **Escolha um tamanho**, clique em **A1 Standard** conforme mostrado abaixo e, em seguida, clique em **Selecionar**.

	![Portal do Azure - Escolha um tamanho](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Na folha **Configurações**, clique em **Endereço IP público**; então, na folha **Criar endereço IP público**, em **Atribuição**, clique em **Estático** conforme mostrado abaixo. E em seguida, clique em **OK**.

	![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Na folha **Configurações**, clique em **OK**.
8. Examine a folha **Resumo** conforme mostrado abaixo e então clique em **OK**.

	![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Observe o novo bloco no seu painel.

	![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Depois que a VM é criada, a folha **Configurações** será exibida conforme mostrado abaixo

	![Portal do Azure - Criar endereço IP público](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0114_2016-->