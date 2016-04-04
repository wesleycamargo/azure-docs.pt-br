<properties 
   pageTitle="Gerenciar NSGs usando o portal de visualização no Resource Manager | Microsoft Azure"
   description="Saiba como gerenciar NSGs existentes usando o portal de visualização no Resource Manager"
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
   ms.date="03/14/2016"
   ms.author="telmos" />

# Gerenciar NSGs usando o portal de visualização

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## Recuperar informações

Você pode exibir seus NSGs existentes, recuperar regras para um NSG existente e descobrir a quais recursos um NSG está associado.

### Exibir NSGs existentes
Para exibir todos os NSGs existentes em uma assinatura, siga as etapas abaixo.

1. Em um navegador, vá até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Procurar >** > **Grupos de segurança de rede**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Verifique a lista de NSGs na folha **Grupos de segurança de rede**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Para exibir a lista de NSGs no grupo de recursos **RG-NSG**, siga as etapas abaixo.

1. Clique em **Grupos de recursos >** > **RG-NSG** > **...**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Na lista de recursos, procure itens que exibam o ícone do NSG, conforme mostrado na folha **Recursos** abaixo.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
		 
### Listar todas as regras de um NSG

Para exibir as regras de um NSG chamado **NSG-FrontEnd**, siga as etapas abaixo.

1. Na folha **Grupos de segurança de rede**, ou na folha **Recursos** mostrada acima, clique em **NSG-FrontEnd**.
2. Na guia **Configurações**, clique em **Regras de segurança de entrada**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. A folha **Regras de segurança de entrada** é exibida como mostrado abaixo.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Na guia **Configurações**, clique em **Regras de segurança de saída** para ver as regras de saída.

>[AZURE.NOTE] Para exibir as regras padrão, clique no ícone **Regras padrão**, na parte superior da folha que exibe as regras.

### Exibir associações de NSGs

Para ver a quais recursos o NSG **NSG-FrontEnd** está associado, siga as etapas abaixo.

1. Na folha **Grupos de segurança de rede**, ou na folha **Recursos** mostrada acima, clique em **NSG-FrontEnd**.
2. Na guia **Configurações**, clique em **Sub-redes** para ver quais sub-redes estão associadas ao NSG.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Na guia **Configurações**, clique em **Interfaces de rede** para ver quais NICs estão associadas ao NSG.

## Gerenciar regras

Você pode adicionar regras a um NSG existente, editar regras existentes e remover regras.

### Adicionar uma regra

Para adicionar uma regra permitindo o tráfego de **entrada** na porta **443** de qualquer computador para o NSG **NSG-FrontEnd**, siga as etapas abaixo.

1. Na folha **Grupos de segurança de rede**, ou na folha **Recursos** mostrada acima, clique em **NSG-FrontEnd**.
2. Na guia **Configurações**, clique em **Regras de segurança de entrada**.
3. Na folha **Regras de segurança de entrada**, clique em **Adicionar**. Na folha **Adicionar regra de segurança de entrada**, preencha os valores como mostrado abaixo e clique em **OK**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Depois de alguns segundos, observe a nova regra na folha **Regras de segurança de entrada**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### Alterar uma regra

Para alterar a regra criada acima para permitir o tráfego de entrada apenas da **Internet**, siga as etapas abaixo.

1. Na folha **Grupos de segurança de rede**, ou na folha **Recursos** mostrada acima, clique em **NSG-FrontEnd**.
2. Na guia **Configurações**, clique na regra criada acima.
3. Na folha **allow-https**, altere a propriedade **Source** como mostrado abaixo e clique em **Salvar**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### Excluir uma regra

Para excluir a regra criada acima, siga as etapas abaixo.

1. Na folha **Grupos de segurança de rede**, ou na folha **Recursos** mostrada acima, clique em **NSG-FrontEnd**.
2. Na guia **Configurações**, clique na regra criada acima.
3. Na folha **allow-https**, clique em **Excluir** e em **Sim**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## Gerenciar associações

É possível associar um NSG a sub-redes e NICs. Você também pode desassociar um NSG de qualquer recurso ao qual ele esteja associado.

### Associar um NSG a uma NIC

Para associar o NSG **NSG-FrontEnd** à NIC **TestNICWeb1**, siga as etapas abaixo.

1. Na folha **Grupos de segurança de rede**, ou na folha **Recursos** mostrada acima, clique em **NSG-FrontEnd**.
2. Na guia **Configurações**, clique em **Interfaces de rede** > **Associar** > **TestNICWeb1**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### Desassociar um NSG de uma NIC

Para desassociar o NSG **NSG-FrontEnd** da NIC **TestNICWeb1**, siga as etapas abaixo.

1. No portal do Azure, clique em **Grupos de recursos >** > **RG-NSG** > **...** > **TestNICWeb1**.
2. Na folha **TestNICWeb1**, clique em **Alterar segurança...** > **Nenhum**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Você também pode usar essa folha para associar a NIC a qualquer NSG existente.

### Desassociar um NSG de uma sub-rede

Para desassociar o NSG **NSG-FrontEnd** da sub-rede **FrontEnd**, siga as etapas abaixo.

1. No portal do Azure, clique em **Grupos de recursos >** > **RG-NSG** > **...** > **TestVNet**.
2. Na folha **Configurações**, clique em **Sub-redes** > **FrontEnd** > **Grupo de segurança de rede** > **Nenhum**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Na folha **FrontEnd**, clique em **Salvar**.

![Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### Associar um NSG a uma sub-rede

Para associar o NSG **NSG-FrontEnd** à sub-rede **FronEnd** novamente, siga as etapas abaixo.

1. No portal do Azure, clique em **Grupos de recursos >** > **RG-NSG** > **...** > **TestVNet**.
2. Na folha **Configurações**, clique em **Sub-redes** > **FrontEnd** > **Grupo de segurança de rede** > **NSG-FrontEnd**.
3. Na folha **FrontEnd**, clique em **Salvar**.

>[AZURE.NOTE] Você também pode associar um NSG a uma sub-rede na folha **Configurações** do NSG.

## Excluir um NSG

Você pode excluir um NSG apenas se ele não estiver associado a nenhum recurso. Para excluir um NSG, siga as etapas abaixo.

1. No portal do Azure, clique em **Grupos de recursos >** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. Na folha **Configurações**, clique em **Interfaces de rede**.
3. Se houver alguma NIC listada, clique na NIC e siga a etapa 2 em [Desassociar um NSG de uma NIC](#Dissociate-an-NSG-from-a-NIC).
4. Repita a etapa 3 para cada NIC.
5. Na folha **Configurações**, clique em **Sub-redes**.
6. Se houver alguma sub-rede listada, clique na sub-rede e siga as etapas 2 e 3 em [Desassociar um NSG de uma sub-rede](#Dissociate-an-NSG-from-a-subnet).
7. Role da esquerda para a folha **NSG-FrontEnd** e clique em **Excluir** > **Sim**.

[Portal do Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## Próximas etapas

- [Habilitar registro em log](virtual-network-nsg-manage-log.md) para NSGs.

<!---HONumber=AcomDC_0323_2016-->