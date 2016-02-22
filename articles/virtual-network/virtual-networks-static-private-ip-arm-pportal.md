<properties 
   pageTitle="Como definir um IP privado estático no modo ARM usando o portal do Azure | Microsoft Azure"
   description="Noções básicas sobre IPs privados (DIPs) e como gerenciá-los no modo ARM usando o portal do Azure"
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

# Como definir um endereço IP privado estático no portal do Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [gerenciar o endereço IP privado estático no modelo de implantação clássico](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

As etapas de exemplo abaixo esperam um ambiente simples já criado. Se você quiser executar as etapas da forma como elas aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-arm-pportal.md).

## Como criar uma VM para testar endereços IP privados estáticos

Você não pode definir um endereço IP privado estático durante a criação de uma VM no modo de implantação do Gerenciador de Recursos usando o portal do Azure. Você deve criar a VM primeiro e, em seguida, definir seu IP privado como estático.

Para criar uma VM denominada *DNS01* na sub-rede *Front-end* de uma VNet chamada *TestVNet*, execute as etapas abaixo:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **NOVO** > **Computação** > **Windows Server 2012 R2 Datacenter**, observe que a lista **Selecionar um modelo de implantação** já mostra **Gerenciador de recursos** e, em seguida, clique em **Criar**, conforme visto na figura abaixo.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Na folha **Basics**, insira o nome da VM a ser criada (*DNS01* em nosso cenário), a conta de administrador local e a senha, como mostra a figura a seguir.

	![Folha de Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Certifique-se que o **Local** selecionado é *Centro dos EUA*, em seguida, clique em **Selecionar existente** em **Grupo de recursos**, em seguida, clique em **Grupo de recursos** novamente, em seguida, clique em *TestRG*, e, em seguida, clique em **OK**.

	![Folha de Noções básicas](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Na folha **Escolha um tamanho**, selecione **A1 Padrão** e, em seguida, clique em **Selecionar**.

	![Escolha uma folha de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure04.png)

6. Na folha **Configurações**, verifique se as seguintes propriedades são definidas são definidas com os valores abaixo e, em seguida, clique em **OK**.

	-**Conta de armazenamento**: *vnetstorage* - **Rede**: *TestVNet* - **Sub-rede**: *FrontEnd*

	![Escolha uma folha de tamanho](./media/virtual-networks-static-ip-arm-pportal/figure05.png)

7. Na folha **Resumo**, clique em **OK**. Observe o bloco abaixo exibido no painel.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## Como recuperar informações do endereço IP privado estático de uma VM

Para exibir as informações de endereço IP privado estático para a VM criada com as etapas acima, execute as etapas abaixo.

1. No portal do Azure, clique em **PROCURAR TUDO** > **Máquinas virtuais** > **DNS01** > **Todas as configurações** > **Interfaces de rede** e, em seguida, clique na interface de rede listada.

	![Implantando o bloco VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Na folha **Interface de rede**, clique em **Todas as configurações** > **Endereços IP** e observe os valores de **Atribuição** e **Endereço IP**.

	![Implantando o bloco VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada usando as etapas acima, execute as etapas abaixo:

1. Na folha **Endereços IP** mostrada acima, clique em **Estático** em **Atribuição**.
2. Digite *192.168.1.101* para **Endereço IP**, e, em seguida, clique em **Salvar**.

	![Criar VM no portal do Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Se, depois de clicar em **Salvar** você notar que a atribuição ainda está definida como **Dinâmico**, isso significa que o endereço IP digitado já está em uso. Tente um endereço IP diferente.

## Como remover o endereço IP privado estático de uma VM
Para remover o endereço IP privado estático da VM criada acima, siga a etapa abaixo.
	
1. Na folha **Endereços IP** mostrada acima, clique em **Dinâmico** em **Atribuição** e, em seguida, clique em **Salvar**.

## Próximas etapas

- Saiba mais sobre endereços [IP públicos reservados](../virtual-networks-reserved-public-ip).
- Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](../virtual-networks-instance-level-public-ip).
- Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0211_2016-->