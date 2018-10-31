---
title: 'Criar um gateway VPN baseado em rotas: portal do Azure | Microsoft Docs'
description: Criar um Gateway de VPN baseado em rota usando o portal do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 7139b2de79b4e092ca761a4e51061c233e6031b5
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470295"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Criar um gateway de VPN baseado em rotas usando o portal do Azure

Este artigo ajuda você a criar rapidamente um gateway de VPN do Azure baseadas em rota usando o portal do Azure.  Um gateway de VPN é usado ao criar uma conexão VPN à rede local. Você também pode usar um gateway de VPN para se conectar a VNets. 

As etapas neste artigo criarão uma rede virtual, uma sub-rede, uma sub-rede do gateway e um gateway VPN com base em rota (gateway de rede virtual). Concluída a criação de gateway, você pode criar conexões. Estas etapas exigem uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="vnet"></a>Criar uma rede virtual

1. Em um navegador, acesse o [Portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.
2. Clique em **Criar um recurso**. No campo **Pesquisar no marketplace**, digite ‘rede virtual’. Localize a **Rede virtual** na lista retornada e clique para abrir a página **Rede Virtual**.
3. Perto da parte inferior da página Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** e clique em **Criar**. Isso abre a página **Criar rede virtual**.
4. Na página **Criar rede virtual**, defina as configurações da VNet. Durante o preenchimento dos campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres digitados no campo forem válidos. Use os seguintes valores:

  - **Nome**: TestVNet1
  - **Espaço de endereço**: 10.1.0.0/16
  - **Assinatura**: verifique se a assinatura listada é a que você quer usar. Você pode alterar as assinaturas usando o menu suspenso.
  - **Grupo de recursos**: TestRG1
  - **Local**: Leste dos EUA
  - **Sub-rede**: FrontEnd
  - **Intervalo de endereços**: 10.1.0.0/24

  ![Página Criar rede virtual](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Página Criar rede virtual")
5. Depois de inserir os valores, selecione **fixar no painel** para tornar mais fácil localizar sua rede virtual no painel e, em seguida, clique em **criar**. Depois de clicar em **Criar**, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.

## <a name="gwsubnet"></a>Adicione uma sub-rede de gateway

A sub-rede de gateway contém os endereços IP reservados que usam os serviços de gateway de rede virtual. Criar uma sub-rede de gateway.

1. No portal, navegue até a rede virtual para a qual você deseja criar um gateway de rede virtual.
2. Na sua página rede virtual, clique em **sub-redes** para expandir a página **VNet1 - sub-redes**.
3. Clique em **+Sub-rede de gateway** no topo para abrir a página **Adicionar sub-rede**.

  ![Adicionar a sub-rede de gateway](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Adicionar a sub-rede de gateway")
4. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Ajuste os valores preenchidos automaticamente de **Intervalo de endereços** para corresponder aos seguintes valores:

  **Intervalo de endereços (bloco CIDR)**: 10.1.255.0/27

  ![Adição da sub-rede de gateway](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Adição da sub-rede de gateway")
5. Para criar a sub-rede de gateway, clique em **OK** na parte inferior da página.

## <a name="gwvalues"></a>Configurar Gateway

1. No lado esquerdo da página do portal, clique em **+ Criar um recurso** e digite 'Gateway de Rede Virtual' na caixa de pesquisa e, em seguida, pressione **Enter**. Em **Resultados**, localize e clique em **Gateway de rede virtual**.
2. Na página Gateway da rede virtual, clique em **Criar** na parte inferior da página para abrir a página **Criar gateway da rede virtual**.
3. Na página **Criar gateway de rede virtual**, especifique os valores do seu gateway de rede virtual.

  - **Nome**: Vnet1GW
  - **Tipo de gateway:** VPN 
  - **Tipo de VPN:** baseada em rota
  - **SKU**: VpnGw1
  - **Local**: Leste dos EUA
  - **Rede virtual**: clique em **Virtual network/escolher uma rede virtual** para abrir a página **escolha uma rede virtual**. Selecione **VNet1**.
  - **Endereço IP público**: essa configuração especifica o objeto de endereço IP público associado ao gateway da VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

    - Deixe **criar novo** selecionado.
    - Na caixa de texto, digite uma **nome** para seu endereço IP público. Para este exercício, use **VNet1GWIP**.<br>

    ![Configurações de gateway](./media/create-routebased-vpn-gateway-portal/gw.png "Configurações de gateway")

## <a name="creategw"></a>Criar o gateway de VPN

1. Verifique as configurações na página **Criar gateway de rede virtual**. Se necessário, ajuste valores.
2. Clique **Criar** na parte inferior da página.

  Após você clicar **Criar**, em as configurações são validadas, e você verá o bloco **Implantar gateway de rede virtual** no painel. Um gateway de VPN pode demorar até 45 minutos para ser criado. Talvez seja necessário atualizar a página do portal para ver o status concluído.

## <a name="viewgw"></a>Veja o Gateway de VPN

1. Depois de criar o gateway, navegue para VNet1 no portal. O gateway de VPN é exibido na página Visão geral de como um dispositivo conectado.

  ![Dispositivos conectados](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "dispositivos conectados")

2. Na lista de dispositivos, clique em **VNet1GW** para exibir mais informações.

  ![Gateway VPN do modo de exibição](./media/create-routebased-vpn-gateway-portal/view-gateway.png "gateway VPN do modo de exibição")

## <a name="next-steps"></a>Próximas etapas

Uma vez que o gateway tenha sido criado, você pode criar uma conexão entre sua rede virtual e outra rede virtual. Ou criar uma conexão entre sua rede virtual e redes locais.

> [!div class="nextstepaction"]
> [Criar uma conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Criar uma conexão ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Criar uma conexão com outra rede virtual](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
