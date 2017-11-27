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
ms.date: 11/15/2017
ms.author: cherylmc
ms.openlocfilehash: b2da1c7148e27ca8dd8eb774d4201415a969fada
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configurar uma conexão de gateway de VPN de VNet para VNet usando o portal do Azure

Este artigo mostra como criar uma conexão de gateway de VPN entre redes virtuais. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes. Ao conectar-se a redes virtuais a partir de assinaturas diferentes, as assinaturas não precisam ser associadas ao mesmo locatário do Active Directory. 

As etapas neste artigo se aplicam ao modelo de implantação do Resource Manager e o uso do Portal do Azure. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conectar modelos de implantação diferentes – portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conectar modelos de implantação diferentes - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. É possível criar conexões Site a site (IPsec) entre redes virtuais, em vez de Rede virtual a Rede virtual. Ambos os tipos de conexão funcionam da mesma maneira ao se comunicarem. No entanto, quando você cria uma conexão de rede virtual a rede virtual, se atualizar o espaço de endereço de uma rede virtual, a outra saberá automaticamente rotear para o espaço de endereço atualizado. Se você criar conexões de Site a site (IPsec), é preciso configurar manualmente o espaço de endereço para o gateway de rede local. Quando estiver trabalhando com configurações complexas, talvez você prefira criar conexões IPsec em vez de rede virtual a rede virtual. Isso permite que você especifique manualmente o espaço de endereço adicional para o gateway de rede local.

Se suas VNets estiverem na mesma região, convém considerar conectá-las usando o emparelhamento VNet. O emparelhamento Vnet não usa um gateway de VPN. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

![Sobre conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Sobre conexões")

### <a name="why-connect-virtual-networks"></a>Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

* **Redundância geográfica entre regiões e presença geográfica**

  * Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
  * Com o Balanceador de Carga e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.
* **Aplicativos multicamadas regionais com limite administrativo ou de isolamento**

  * Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.

Ao usar essas etapas como um exercício, você pode usar os seguintes valores de exemplo. No exemplo, as redes virtuais estão na mesma assinatura, mas em diferentes grupos de recursos. Se suas redes virtuais estiverem em assinaturas diferentes, não será possível criar a conexão no portal. Você pode usar o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou a [CLI](vpn-gateway-howto-vnet-vnet-cli.md). Para saber mais sobre conexões de Rede Virtual a Rede Virtual, consulte as [Perguntas frequentes sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo.

### <a name="values"></a>Configurações de exemplo

**Valores para TestVNet1:**

* Nome da rede virtual: TestVNet1
* Espaço de endereço: 10.11.0.0/16
* Assinatura: selecione a assinatura que você quer usar
* Grupo de recursos: TestRG1
* Local: Leste dos EUA
* Nome da sub-rede: FrontEnd
* Intervalo de endereços da sub-rede: 10.11.0.0/24
* Nome da Sub-rede de Gateway: GatewaySubnet (isso preencherá automaticamente o portal)
* Espaço de endereço da Sub-Rede do Gateway: 10.11.255.0/27
* Servidor DNS: use o endereço IP do seu Servidor DNS
* Nome do Gateway de Rede Virtua: TestVNet1GW
* Tipo de Gateway: VPN
* Tipo de VPN: baseada em rota
* SKU: selecione a SKU de Gateway que você deseja usar
* Nome do endereço IP público: TestVNet1GWIP
* Nome da conexão: TestVNet1toTestVNet4
* Chave compartilhada: você pode criar a chave compartilhada por conta própria. Para este exemplo, usaremos abc123. O importante é que, quando você cria a conexão entre as redes virtuais, o valor deve corresponder.

**Valores para TestVNet4:**

* Nome da rede virtual: TestVNet4
* Espaço de endereço: 10.41.0.0/16
* Assinatura: selecione a assinatura que você quer usar
* Grupo de recursos: TestRG4
* Local: Oeste dos EUA
* Nome da sub-rede: FrontEnd
* Intervalo de endereços da sub-rede: 10.41.0.0/24
* Nome da GatewaySubnet: GatewaySubnet (isso preencherá automaticamente o portal)
* Intervalo de endereços da GatewaySubnet: 10.41.255.0/27
* Servidor DNS: use o endereço IP do seu Servidor DNS
* Nome do Gateway de Rede Virtual: TestVNet4GW
* Tipo de Gateway: VPN
* Tipo de VPN: baseada em rota
* SKU: selecione a SKU de Gateway que você deseja usar
* Nome do endereço IP público: TestVNet4GWIP
* Nome da conexão: TestVNet4toTestVNet1
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

## <a name="dns"></a>4. Especificar um servidor DNS (opcional)
O DNS não é necessário para as conexões VNet a VNet. No entanto, se você quiser ter a resolução de nomes dos recursos que são implantados em sua rede virtual, deverá especificar um servidor DNS. Essa configuração permite que você especifique o servidor DNS que deseja usar para a resolução de nomes dessa rede virtual. Ela não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Criar um gateway de rede virtual
Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se você estiver criando esta configuração como um exercício, consulte as [Configuração de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway da rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Criar e configurar TestVNet4
Depois de ter configurado TestVNet1, crie TestVNet4 repetindo as etapas anteriores, substituindo os valores pelos valores de TestVNet4. Você não precisa esperar até que o gateway de rede virtual para TestVNet1 tenha terminado a criação antes de configurar TestVNet4. Se você estiver usando seus próprios valores, certifique-se de que os espaços de endereço não se sobreponham a qualquer uma das redes virtuais às quais você deseja se conectar.

## <a name="TestVNet1Connection"></a>7. Configurar a conexão de gateway TestVNet1
Quando os gateways de rede virtual para TestVNet1 e TestVNet4 tiverem sido concluídos, você poderá criar suas conexões de gateway de rede virtual. Nesta seção, você cria uma conexão de VNet1 para VNet4. Estas etapas funcionam apenas para as VNets na mesma assinatura. Se suas VNets estiverem em assinaturas diferentes, você deverá usar o PowerShell para fazer a conexão. Consulte o artigo [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). No entanto, se suas redes virtuais estão em grupos de recursos diferente na mesma assinatura, você poderá conectá-las usando o portal.

1. Em **Todos os recursos**, navegue até o gateway de rede virtual para sua rede virtual. Por exemplo, **TestVNet1GW**. Clique em **TestVNet1GW** para abrir a página do gateway de rede virtual.

  ![Página Conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Página Conexões")
2. Clique em **+ Adicionar** para abrir a página **Adicionar conexão**.

  ![Adicionar conexão](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Adicionar uma conexão")
3. Na página **Adicionar conexão**, no campo nome, digite um nome para a conexão. Por exemplo, **TestVNet1toTestVNet4**.
4. Para **Tipo de conexão**, selecione **Redes Virtuais a Redes Virtuais** na lista suspensa.
5. O valor do campo **Primeiro gateway de rede virtual** é preenchido automaticamente porque você está criando essa conexão do gateway de rede virtual especificado.
6. O campo **Segundo gateway de rede virtual** é o gateway de rede virtual de rede virtual com o qual você deseja criar uma conexão. Clique em **Escolher outro gateway de rede virtual** para abrir a página **Escolher gateway de rede virtual**.
7. Exiba os gateways de rede virtual listados nessa página. Observe que somente os gateways de rede virtual que estão em sua assinatura são listados. Se você quiser se conectar a um gateway de rede virtual que não esteja em sua assinatura, use o [Artigo do PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).
8. Clique no gateway de rede virtual ao qual você deseja se conectar.
9. No campo **Chave compartilhada**, digite uma chave compartilhada para a conexão. Você pode gerar ou criar essa chave. Em uma conexão site a site, a chave usada seria exatamente a mesma para o dispositivo local e a conexão de gateway de rede virtual. O conceito é semelhante aqui, exceto que, em vez de se conectar a um dispositivo VPN, você se conectará a outro gateway de rede virtual.
10. Clique em **OK** na parte inferior da página para salvar as alterações.

## <a name="TestVNet4Connection"></a>8. Configurar a conexão de gateway TestVNet4
Em seguida, crie uma conexão de TestVNet4 para TestVNet1. No portal, localize o gateway de rede virtual associado com o TestVNet4. Siga as etapas da seção anterior, substituindo os valores para criar uma conexão de TestVNet4 para TestVNet1. Use a mesma chave compartilhada.

## <a name="VerifyConnection"></a>9. Verificar as conexões

Localize o gateway de rede virtual no portal. Na página do gateway de rede virtual, clique em **Conexões** para exibir a página de conexões para o gateway de rede virtual. Depois que a conexão for estabelecida, você verá os valores do Status mudarem para **Êxito** e **Conectado**. Você pode clicar duas vezes em uma conexão para abrir a página **Essentials** e exibir mais informações.

![Êxito](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Êxito")

Quando dados começarem a fluir, você verá valores de Entrada de dados e de Saída de dados.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="to-add-additional-connections"></a>Para adicionar conexões extras

Se deseja adicionar conexões extras, navegue até o gateway de rede virtual a partir do qual deseja criar a conexão e clique em **Conexões**. Você pode criar outra conexão de rede virtual a rede virtual ou criar uma conexão Site a site (IPsec) para um lugar local. Verifique se você ajustou o **Tipo de conexão** para que corresponda ao tipo de conexão que deseja criar. Antes de criar conexões adicionais, verifique se o espaço de endereço para sua rede virtual não se sobrepõe nenhum dos espaços de endereço aos quais você deseja se conectar. Para obter as etapas para criar uma conexão Site a site, consulte [Criar uma conexão Site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Perguntas frequentes sobre Rede Virtual para Rede Virtual
Exiba os detalhes de perguntas frequentes para obter informações adicionais sobre conexões de Rede Virtual para Rede Virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Próximas etapas

Consulte [Segurança de Rede](../virtual-network/security-overview.md) para obter mais informações sobre como você pode limitar o tráfego de rede para recursos em uma rede virtual.

Consulte [Roteamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md) para obter mais informações sobre como o Azure roteia o tráfego entre o Azure, locais e recursos da Internet.
