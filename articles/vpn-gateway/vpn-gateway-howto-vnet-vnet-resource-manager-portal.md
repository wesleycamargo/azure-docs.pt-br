---
title: 'Conectar uma rede virtual do Azure a outra rede virtual: Portal | Microsoft Docs'
description: "Crie uma conexão de gateway de VPN entre redes virtuais usando o Resource Manager e o portal do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c80ddbaf8c2c84735564e514ddaf4308c4aff303
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurar uma conexão de rede virtual usando o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Clássico - Portal Clássico](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Este artigo explica as etapas para criar uma conexão entre redes virtuais no modelo de implantação do Resource Manager usando o Gateway de VPN e o portal do Azure.

Ao usar o portal do Azure para conectar redes virtuais, elas devem estar na mesma assinatura. Se suas redes virtuais estiverem em assinaturas diferentes, você ainda poderá conectá-las usando as etapas do [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

![Diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implantação e métodos para conexões de VNet a VNet
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os modelos de implantação e os métodos de configurações de VNet-para-VNet disponíveis no momento. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Emparelhamento VNet**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Sobre conexões de rede virtual a rede virtual
Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN do Azure para fornecer um túnel seguro usando IPsec/IKE. As VNetsque você conecta podem estar em regiões ou em assinaturas diferentes.

Você pode até combinar a comunicação de rede virtual com rede virtual a configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

![Sobre conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Sobre conexões")

### <a name="why-connect-virtual-networks"></a>Por que conectar redes virtuais?
Você talvez queira conectar redes virtuais pelos seguintes motivos:

* **Redundância geográfica entre regiões e presença geográfica**
  
  * Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
  * Com o Balanceador de Carga e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.
* **Aplicativos multicamadas regionais com limite administrativo ou de isolamento**
  
  * Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.

Para saber mais sobre conexões de Rede Virtual a Rede Virtual, confira as [Considerações sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo.

### <a name="values"></a>Configurações de exemplo
Ao usar estas etapas como um exercício, você poderá usar os valores de configuração de exemplo. Para fins de exemplo, podemos usar vários espaços de endereço para cada rede virtual. No entanto, as configurações de rede virtual para rede virtual não exigem vários espaços de endereço.

**Valores para TestVNet1:**

* Nome da rede virtual: TestVNet1
* Espaço de endereço: 10.11.0.0/16
  * Nome da sub-rede: FrontEnd
  * Intervalo de endereços da sub-rede: 10.11.0.0/24
* Grupo de recursos: TestRG1
* Local: Leste dos EUA
* Espaço de Endereço: 10.12.0.0/16
  * Nome da sub-rede: BackEnd
  * Intervalo de endereços da sub-rede: 10.12.0.0/24
* Nome da Sub-rede de Gateway: GatewaySubnet (isso preencherá automaticamente o portal)
  * Espaço de endereço da Sub-Rede do Gateway: 10.11.255.0/27
* Servidor DNS: use o endereço IP do seu Servidor DNS
* Nome do Gateway de Rede Virtua: TestVNet1GW
* Tipo de Gateway: VPN
* Tipo de VPN: baseada em rota
* SKU: selecione a SKU de Gateway que você deseja usar
* Nome do endereço IP público: TestVNet1GWIP
* Valores de conexão:
  * Nome: TestVNet1toTestVNet4
  * Chave compartilhada: você pode criar a chave compartilhada por conta própria. Para este exemplo, usaremos abc123. O importante é que, quando você cria a conexão entre as redes virtuais, o valor deve corresponder.

**Valores para TestVNet4:**

* Nome da rede virtual: TestVNet4
* Espaço de endereço: 10.41.0.0/16
  * Nome da sub-rede: FrontEnd
  * Intervalo de endereços da sub-rede: 10.41.0.0/24
* Grupo de recursos: TestRG1
* Local: Oeste dos EUA
* Espaço de Endereço: 10.42.0.0/16
  * Nome da sub-rede: BackEnd
  * Intervalo de endereços da sub-rede: 10.42.0.0/24
* Nome da GatewaySubnet: GatewaySubnet (isso preencherá automaticamente o portal)
  * Intervalo de endereços da GatewaySubnet: 10.41.255.0/27
* Servidor DNS: use o endereço IP do seu Servidor DNS
* Nome do Gateway de Rede Virtual: TestVNet4GW
* Tipo de Gateway: VPN
* Tipo de VPN: baseada em rota
* SKU: selecione a SKU de Gateway que você deseja usar
* Nome do endereço IP público: TestVNet4GWIP
* Valores de conexão:
  * Nome: TestVNet4toTestVNet1
  * Chave compartilhada: você pode criar a chave compartilhada por conta própria. Para este exemplo, usaremos abc123. O importante é que, quando você cria a conexão entre as redes virtuais, o valor deve corresponder.

## <a name="CreatVNet"></a>1. Criar e configurar a TestVNet1
Se você já tiver uma rede virtual, verifique se as configurações são compatíveis com seu design de gateway de VPN. Preste atenção especial em todas as sub-redes que possam se sobrepor a outras redes. Se você tiver uma sobreposição de sub-redes, a conexão não funcionará corretamente. Se a sua Rede Virtual estiver configurada com as definições corretas, poderá começar as etapas na seção [Especificar um servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Adicionar outro espaço de endereço e criar sub-redes
Você pode adicionar outro espaço de endereço e criar sub-redes após a criação da sua rede virtual.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Criar uma sub-rede de gateway
Antes de conectar sua Rede Virtual a um gateway, você precisará criar a sub-rede de gateway para a Rede Virtual à qual você deseja se conectar. Se possível, é melhor criar uma sub-rede de gateway usando um bloco CIDR de /28 ou /27, a fim de fornecer endereços IP suficientes para acomodar requisitos futuros de configuração.

Se a criação dessa configuração fizer parte de um exercício, confira essas [Configurações de exemplo](#values) ao criar sua sub-rede de gateway.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede de gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Especificar um servidor DNS (opcional)
O DNS não é necessário para as conexões VNet a VNet. No entanto, se você quiser ter a resolução de nomes dos recursos que são implantados em sua rede virtual, deverá especificar um servidor DNS. Essa configuração permite que você especifique o servidor DNS que deseja usar para a resolução de nomes dessa rede virtual. Ela não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Criar um gateway de rede virtual
Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Esta etapa pode demorar até 45 minutos para ser concluída. Se você estiver criando esta configuração como um exercício, consulte as [Configuração de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway da rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Criar e configurar TestVNet4
Depois de ter configurado TestVNet1, crie TestVNet4 repetindo as etapas anteriores, substituindo os valores pelos valores de TestVNet4. Você não precisa esperar até que o gateway de rede virtual para TestVNet1 tenha terminado a criação antes de configurar TestVNet4. Se você estiver usando seus próprios valores, certifique-se de que os espaços de endereço não se sobreponham a qualquer uma das redes virtuais às quais você deseja se conectar.

## <a name="TestVNet1Connection"></a>7. Configurar a conexão TestVNet1
Quando os gateways de rede virtual para TestVNet1 e TestVNet4 tiverem sido concluídos, você poderá criar suas conexões de gateway de rede virtual. Nesta seção, você criará uma conexão de VNet1 para VNet4.

1. Em **Todos os recursos**, navegue até o gateway de rede virtual para sua rede virtual. Por exemplo, **TestVNet1GW**. Clique em **TestVNet1GW** para abrir a folha do gateway de rede virtual.
   
    ![Folha Conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Folha Conexões")
2. Clique em **+ Adicionar** para abrir a folha **Adicionar conexão**.
3. Na folha **Adicionar conexão**, no campo nome, digite um nome para a conexão. Por exemplo, **TestVNet1toTestVNet4**.
   
    ![Nome da conexão](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nome da conexão")
4. Para **Tipo de conexão**. selecione **Redes Virtuais a Redes Virtuais** na lista suspensa.
5. O valor do campo **Primeiro gateway de rede virtual** é preenchido automaticamente porque você está criando essa conexão do gateway de rede virtual especificado.
6. O campo **Segundo gateway de rede virtual** é o gateway de rede virtual de rede virtual com o qual você deseja criar uma conexão. Clique em **Escolher outro gateway de rede virtual** para abrir a folha **Escolher gateway de rede virtual**.
   
    ![Adicionar conexão](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Adicionar uma conexão")
7. Exiba os gateways de rede virtual listados nessa folha. Observe que somente os gateways de rede virtual que estão em sua assinatura são listados. Se você quiser se conectar a um gateway de rede virtual que não esteja em sua assinatura, use o [Artigo do PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Clique no gateway de rede virtual ao qual você deseja se conectar.
9. No campo **Chave compartilhada**, digite uma chave compartilhada para a conexão. Você pode gerar ou criar essa chave. Em uma conexão site a site, a chave usada seria exatamente a mesma para o dispositivo local e a conexão de gateway de rede virtual. O conceito é semelhante aqui, exceto que, em vez de se conectar a um dispositivo VPN, você se conectará a outro gateway de rede virtual.
   
    ![Chave compartilhada](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Chave compartilhada")
10. Clique em **OK** na parte inferior da folha para salvar suas alterações.

## <a name="TestVNet4Connection"></a>8. Configurar a conexão TestVNet4
Em seguida, crie uma conexão de TestVNet4 para TestVNet1. Use o mesmo método que você usou para criar a conexão de TestVNet1 a TestVNet4. Use a mesma chave compartilhada.

## <a name="VerifyConnection"></a>9. Verificar a conexão
Verifique a conexão. Para cada gateway de rede virtual, faça o seguinte:

1. Localize a folha do gateway de rede virtual. Por exemplo, **TestVNet4GW**. 
2. Na folha do gateway de rede virtual, clique em **Conexões** para exibir a folha de conexões para o gateway de rede virtual.

Exiba as conexões e verifique o status. Depois que a conexão for criada, você verá **Êxito** e **Conectado** como os valores de Status.

![Êxito](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Êxito")

Clique duas vezes em cada conexão separadamente para exibir mais informações sobre a conexão.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>Considerações de Rede Virtual para Rede Virtual
Exiba os detalhes de perguntas frequentes para obter informações adicionais sobre conexões de Rede Virtual para Rede Virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Consulte a [Documentação sobre Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para obter mais informações.

