<properties 
   pageTitle="Como definir um IP privado estático no modo clássico usando o Portal do Azure | Microsoft Azure"
   description="Noções básicas sobre IPs privados estáticos e como gerenciá-los no modo clássico usando o portal do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Como definir um endereço IP privado estático (clássico) no portal do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo abaixo esperam um ambiente simples já criado. Se você quiser executar as etapas da forma como elas aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-pportal.md).

## Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM denominada *DNS01* na sub-rede *Front-end* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, execute as etapas abaixo:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **NOVO** > **Computação** > **Windows Server 2012 R2 Datacenter**, observe que a lista **Selecionar um modelo de implantação** já mostra **Clássico** e, em seguida, clique em **Criar**.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Na folha **Criar VM**, insira o nome da VM a ser criada (*DNS01* em nosso cenário), a conta de administrador local e a senha.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Clique em **Configuração opcional** > **Rede** > **Rede Virtual**, e, em seguida, clique em **TestVNet**. Se **TestVNet** não estiver disponível, verifique se você está usando o *Centro dos EUA* como local e que você criou o ambiente de teste descrito no início deste artigo.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. Na folha **Rede**, certifique-se de que a sub-rede selecionada atualmente é *FrontEnd*, em seguida, clique em **Endereços IP**, em **Atribuição de endereço IP** clique em **Estático**, e, em seguida, digite *192.168.1.101* para **Endereço IP** conforme mostrado abaixo.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)

6. Clique em **OK** na folha **Endereços IP**, em seguida, clique em **OK** na folha **Rede** e clique em **OK** na folha **Configuração opcional**.
7. Na folha **Criar VM**, clique em **Criar**. Observe o bloco abaixo exibido no painel.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## Como recuperar informações do endereço IP privado estático de uma VM

Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas abaixo.

1. No portal do Azure, clique em **PROCURAR TUDO** > **Máquinas virtuais (clássico)** > **DNS01** > **Todas as configurações** > **Endereços IP** e observe o endereço IP e a atribuição de endereço IP, conforme mostrado abaixo.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## Como remover o endereço IP privado estático de uma VM
Para remover o endereço IP privado estático da VM criada acima, siga as etapas abaixo.
	
1. Na folha **Endereços IP** mostrada acima, clique em **Dinâmico** à direita da **Atribuição de endereço IP**, em seguida, clique em **Salvar**, e, em seguida, clique em **Sim**.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada usando as etapas acima, execute as etapas abaixo:

1. Na folha **Endereços IP** mostrada acima, clique em **Estático** à direita de **Atribuição de endereço IP**.
2. Digite *192.168.1.101* para **Endereço IP**, clique em **Salvar** e, em seguida, clique em **Sim**.

## Próximas etapas

- Saiba mais sobre endereços [IP públicos reservados](../virtual-networks-reserved-public-ip).
- Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](../virtual-networks-instance-level-public-ip).
- Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0211_2016-->