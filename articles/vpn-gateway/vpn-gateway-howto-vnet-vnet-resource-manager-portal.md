---
title: Configurar uma conexão de gateway de VPN a VNet usando o portal do Azure | Microsoft Docs
description: Crie uma conexão de gateway de VPN entre redes virtuais usando o Resource Manager e o portal do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 94b32595cf2c884ccfd1362f6c8d03f542aabfc5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128374"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configurar uma conexão de gateway de VPN de VNet para VNet usando o portal do Azure

Este artigo ajuda você a conectar VNets (redes virtuais) usando o tipo de conexão de rede virtual a rede virtual. Redes virtuais podem estar em regiões diferentes e ser de assinaturas diferentes. Ao conectar VNets de assinaturas diferentes, as assinaturas não precisam ser associadas ao mesmo locatário do Active Directory. 

![Diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

As etapas neste artigo se aplicam ao modelo de implantação do Azure Resource Manager e o uso do portal do Azure. Você pode criar essa configuração com um modelo ou ferramenta de implantação diferente usando as opções descritas nos artigos a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conectar modelos de implantação diferentes – portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conectar modelos de implantação diferentes - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>


## <a name="about-connecting-vnets"></a>Sobre a conexão de VNets

As seções a seguir descrevem as diferentes maneiras de conectar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma conexão de VNet a VNet é uma maneira simples de conectar VNets. Quando você conecta uma rede virtual a outra rede virtual com um tipo de conexão VNet2VNet (VNet a VNet), é semelhante a criar uma conexão IPsec Site a Site com uma localização local. Os dois tipos de conexão usam um gateway de VPN para fornecer um túnel seguro com IPsec/IKE e funcionam da mesma forma ao se comunicar. No entanto, diferem na maneira como gateway de rede local é configurado. 

Quando você cria uma conexão de VNet a VNet, o espaço de endereço de gateway de rede local é automaticamente criado e preenchido. Se você atualizar o espaço de endereço de uma VNet, a outra roteará automaticamente para o espaço de endereço atualizado. Normalmente, é mais rápido e fácil criar uma conexão de VNet a VNet do que uma conexão Site a Site.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se você estiver trabalhando com uma configuração de rede complicada, talvez prefira conectar suas VNets usando uma [Conexão Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Quando você segue as etapas de IPsec Site a Site, cria e configura os gateways de rede local manualmente. O gateway de rede local para cada VNet trata a outra VNet como um site local. Estas etapas permitem que você especifique os espaços de endereço adicionais para o gateway de rede local para rotear o tráfego. Se o espaço de endereço para uma VNet mudar, você deverá atualizar manualmente o gateway de rede local correspondente.

### <a name="vnet-peering"></a>Emparelhamento VNet

Você também pode conectar suas VNets usando o Emparelhamento VNET. O Emparelhamento VNET não usa um gateway de VPN e tem restrições diferentes. Além disso, os [preços do emparelhamento VNet](https://azure.microsoft.com/pricing/details/virtual-network) são calculados de maneira diferente dos [preços de Gateway de VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Por que criar uma conexão de VNet a VNet?

Talvez você queira conectar redes virtuais usando uma conexão VNet a VNet pelos seguintes motivos:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Redundância geográfica entre regiões e presença geográfica

  * Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
  * Com o Azure Load Balancer e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Por exemplo, você pode configurar grupos de disponibilidade Always On do SQL Server em várias regiões do Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Aplicativos multicamadas regionais com limites administrativos ou de isolamento

  * Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.

Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Essas configurações permitem estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

![Sobre conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Sobre conexões")

Este artigo mostra como conectar VNets usando o tipo de conexão VNet a VNet. Ao realizar estas etapas como um exercício, você pode usar os valores de configurações de exemplo a seguir. No exemplo, as redes virtuais estão na mesma assinatura, mas em diferentes grupos de recursos. Se suas redes virtuais estiverem em assinaturas diferentes, não será possível criar a conexão no portal. Use [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [CLI](vpn-gateway-howto-vnet-vnet-cli.md) em vez disso. Para obter mais informações sobre conexões de rede virtual para rede virtual, confira [Perguntas frequentes sobre VNet a VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Configurações de exemplo

**Valores para TestVNet1:**

- **Configurações de rede virtual**
    - **Nome**: Insira *TestVNet1*.
    - **Espaço de endereço**: Insira *10.11.0.0/16*.
    - **Assinatura**: Selecione a assinatura que deseja usar.
    - **Grupo de recursos**: Insira *TestRG1*.
    - **Localização**: Selecione **Leste dos EUA**.
    - **Sub-rede**
        - **Nome**: Insira *FrontEnd*.
        - **Intervalo de endereços**: Insira *10.11.0.0/24*.
    - **Gateway de sub-rede**:
        - **Nome**: *GatewaySubnet* é preenchido automaticamente.
        - **Intervalo de endereços**: Insira *10.11.255.0/27*.
    - **Servidor DNS**: Selecione **Personalizado** e insira o endereço IP do seu servidor DNS.

- **Configurações de gateway de rede virtual** 
    - **Nome**: Insira *TestVNet1GW*.
    - **Tipo de gateway**: Selecione **VPN**.
    - **Tipo de VPN**: Selecione **Baseado em rota**.
    - **SKU**: selecione a SKU de Gateway que você deseja usar.
    - **Nome do endereço IP público**: Insira *TestVNet1GWIP*
    - **Conexão** 
       - **Nome**: Insira *TestVNet1toTestVNet4*.
       - **Chave compartilhada**: Insira *abc123*. Você pode criar a chave compartilhada por conta própria. Quando você cria a conexão entre as redes virtuais, os valores devem corresponder.

**Valores para TestVNet4:**

- **Configurações de rede virtual**
   - **Nome**: Insira *TestVNet4*.
   - **Espaço de endereço**: Insira *10.41.0.0/16*.
   - **Assinatura**: Selecione a assinatura que deseja usar.
   - **Grupo de recursos**: Insira *TestRG4*.
   - **Localização**: Selecione **Oeste dos EUA**.
   - **Sub-rede** 
      - **Nome**: Insira *FrontEnd*.
      - **Intervalo de endereços**: Insira *10.41.0.0/24*.
   - **GatewaySubnet** 
      - **Nome**: *GatewaySubnet* é preenchido automaticamente.
      - **Intervalo de endereços**: Insira *10.41.255.0/27*.
   - **Servidor DNS**: Selecione **Personalizado** e insira o endereço IP do seu servidor DNS.

- **Configurações de gateway de rede virtual** 
    - **Nome**: Insira *TestVNet4GW*.
    - **Tipo de gateway**: Selecione **VPN**.
    - **Tipo de VPN**: Selecione **Baseado em rota**.
    - **SKU**: selecione a SKU de Gateway que você deseja usar.
    - **Nome do endereço IP público**: Insira *TestVNet4GWIP*.
    - **Conexão** 
       - **Nome**: Insira *TestVNet4toTestVNet1*.
       - **Chave compartilhada**: Insira *abc123*. Você pode criar a chave compartilhada por conta própria. Quando você cria a conexão entre as redes virtuais, os valores devem corresponder.

## <a name="create-and-configure-testvnet1"></a>Criar e configurar a TestVNet1
Se você já tiver uma rede virtual, verifique se as configurações são compatíveis com seu design de gateway de VPN. Preste atenção especial em todas as sub-redes que possam se sobrepor a outras redes. Sua conexão não funcionará corretamente se você tiver uma sobreposição de sub-redes. Depois que a VNet for definida com as configurações corretas, você poderá começar as etapas na seção Especificar um servidor DNS.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>Adicionar outro espaço de endereço e criar sub-redes
Você pode adicionar outro espaço de endereço e criar sub-redes após a criação da sua rede virtual.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>Criar uma sub-rede de gateway
Antes de criar um gateway de rede virtual para sua rede virtual, você precisa criar a sub-rede de gateway. A sub-rede de gateway contém os endereços IP usados pelo gateway de rede virtual. Se possível, é melhor criar uma sub-rede de gateway usando um bloco CIDR de /28 ou /27 para fornecer endereços IP suficientes para acomodar requisitos futuros de configuração.

Se a criação dessa configuração fizer parte de um exercício, veja estas [Configurações de exemplo](#example-settings) ao criar sua sub-rede de gateway.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede de gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>Especificar um servidor DNS (opcional)
O DNS não é necessário para as conexões VNet a VNet. No entanto, se você quiser ter a resolução de nomes dos recursos que são implantados em sua rede virtual, especifique um servidor DNS. Essa configuração permite que você especifique o servidor DNS que deseja usar para a resolução de nomes dessa rede virtual. Ele não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual
Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se você estiver criando esta configuração como um exercício, confira [Configurações de exemplo](#example-settings).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway da rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>Criar e configurar TestVNet4
Depois de ter configurado TestVNet1, crie TestVNet4 repetindo as etapas anteriores e substituindo os valores por aqueles de TestVNet4. Você não precisa esperar até que o gateway de rede virtual para TestVNet1 tenha terminado a criação antes de configurar TestVNet4. Se você estiver usando seus próprios valores, verifique se os espaços de endereço não se sobrepõem a nenhuma das redes virtuais às quais você deseja se conectar.

## <a name="configure-the-testvnet1-gateway-connection"></a>Configurar a conexão de gateway TestVNet1
Quando os gateways de rede virtual para TestVNet1 e TestVNet4 tiverem sido concluídos, você poderá criar suas conexões de gateway de rede virtual. Nesta seção, você cria uma conexão de VNet1 para VNet4. Estas etapas funcionam apenas para as VNets na mesma assinatura. Se as VNets estiverem em assinaturas diferentes, você deverá usar o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) para fazer a conexão. No entanto, se as redes virtuais estiverem em grupos de recursos diferentes na mesma assinatura, você poderá conectá-las usando o portal.

1. No portal do Azure, selecione **Todos os recursos**, insira *gateway de rede virtual* na pesquisa de caixa e, em seguida, navegue até o gateway de rede virtual para sua rede virtual. Por exemplo, **TestVNet1GW**. Selecione-o para abrir a página **Gateway de rede virtual**.

   ![Página Conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Página Conexões")
2. Em **Configurações**, selecione **Conexões** e, em seguida, **Adicionar** para abrir a página **Adicionar conexão**.

   ![Adicionar conexão](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Adicionar uma conexão")
3. Na página **Adicionar conexão**, preencha os valores para sua conexão:

   - **Nome**: Digite um nome para a conexão. Por exemplo, *TestVNet1toTestVNet4*.

   - **Tipo de conexão**: Selecione **VNet a VNet** na lista suspensa.

   - **Primeiro gateway de rede virtual**: O valor deste campo é preenchido automaticamente porque você está criando essa conexão do gateway de rede virtual especificado.

   - **Segundo gateway de rede virtual**: Esse campo é o gateway de rede virtual da VNet para a qual você deseja criar uma conexão. Selecione **Escolher outro gateway de rede virtual** para abrir a página **Escolher gateway de rede virtual**.

     - Exiba os gateways de rede virtual listados nessa página. Observe que somente os gateways de rede virtual que estão em sua assinatura são listados. Se você quiser se conectar a um gateway de rede virtual que não esteja em sua assinatura, use o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Selecione o gateway de rede virtual ao qual você deseja se conectar.

     - **Chave compartilhada (PSK)**: neste campo, insira uma chave compartilhada para sua conexão. Você pode gerar ou criar essa chave. Em uma conexão site a site, a chave usada é a mesma que para o dispositivo local e a conexão de gateway de rede virtual. O conceito é semelhante aqui, exceto que, em vez de se conectar a um dispositivo VPN, você se conectará a outro gateway de rede virtual.
    
4. Selecione **OK** para salvar suas alterações.

## <a name="configure-the-testvnet4-gateway-connection"></a>Configurar a conexão de gateway TestVNet4
Em seguida, crie uma conexão de TestVNet4 para TestVNet1. No portal, localize o gateway de rede virtual associado com o TestVNet4. Siga as etapas da seção anterior, substituindo os valores para criar uma conexão de TestVNet4 para TestVNet1. Use a mesma chave compartilhada.

## <a name="verify-your-connections"></a>Verificar as conexões

Localize o gateway de rede virtual no portal do Azure. Na página **Gateway de rede virtual**, selecione **Conexões** para exibir a página **Conexões** para o gateway de rede virtual. Depois que a conexão for estabelecida, você verá os valores do **Status** mudarem para **Êxito** e **Conectado**. Selecione uma conexão para abrir a página **Essentials** e exibir mais informações.

![Êxito](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Êxito")

Quando dados começarem a fluir, você verá valores de **Entrada de dados** e de **Saída de dados**.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="add-additional-connections"></a>Adicionar conexões extras

Se desejar adicionar conexões extras, navegue até o gateway de rede virtual do qual deseja criar a conexão e selecione **Conexões**. Você pode criar outra conexão de rede virtual a rede virtual ou criar uma conexão Site a site (IPsec) para um lugar local. Verifique se você ajustou o **Tipo de conexão** para que corresponda ao tipo de conexão que deseja criar. Antes de criar conexões adicionais, verifique se o espaço de endereço para sua rede virtual não se sobrepõe nenhum dos espaços de endereço aos quais você deseja se conectar. Para obter as etapas para criar uma conexão Site a site, consulte [Criar uma conexão Site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Perguntas Frequentes sobre VNet a VNet
Exiba os detalhes de perguntas frequentes para obter informações adicionais sobre conexões de Rede Virtual para Rede Virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como você pode limitar o tráfego de rede para recursos em uma rede virtual, confira [Segurança de Rede](../virtual-network/security-overview.md).

Para obter mais informações sobre como o Azure roteia o tráfego entre o Azure, locais e recursos da Internet, consulte [Roteamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).
