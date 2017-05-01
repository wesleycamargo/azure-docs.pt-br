---
title: "Configurar um Gateway de VPN: Portal Clássico : Azure | Microsoft Docs"
description: "Este artigo conduz você pela configuração do seu gateway de VPN da rede virtual e pela alteração de um tipo de roteamento de VPN de gateway. Essas etapas se aplicam ao modelo de implantação clássico e ao Portal Clássico ."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fbe59ba8-b11f-4d21-9bb1-225ec6c6d351
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 2ea4e6bb86b1ba6f7b501b193d0713d3901457af
ms.lasthandoff: 04/06/2017


---
# <a name="configure-a-vpn-gateway-in-the-classic-portal"></a>Configurar um gateway de VPN no Portal Clássico 
Se quiser criar uma conexão segura entre instalações entre o Azure e seu local, será preciso criar um gateway de rede virtual. Um gateway de VPN é um tipo específico de gateway de rede virtual. No modelo de implantação clássico, um gateway de VPN pode ser de um dos dois tipos de roteamento de VPN: estático ou dinâmico. O tipo de VPN escolhido depende de seu plano de design de rede e do dispositivo VPN local que deseja usar. Para saber mais sobre dispositivos VPN, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-overview"></a>Visão geral de configuração
As etapas a seguir guiam você pela configuração do seu gateway de VPN no Portal Clássico do Azure. Essas etapas se aplicam a gateways de redes virtuais que foram criados usando o modelo de implantação clássico. Atualmente, nem todos os parâmetros de configuração para gateways estão disponíveis no Portal do Azure. Quando estiverem, criaremos um novo conjunto de instruções que se aplicam ao Portal do Azure.

### <a name="before-you-begin"></a>Antes de começar
Antes de configurar o gateway, primeiro você precisa criar sua rede virtual. Para ver etapas para criar uma rede virtual para conectividade entre locais, consulte [Configurar uma rede virtual com uma conexão VPN site a site](vpn-gateway-site-to-site-create.md) ou [Configurar uma rede virtual com uma conexão VPN ponto a site](vpn-gateway-point-to-site-create.md). Em seguida, use as etapas a seguir para configurar o gateway de VPN e coletar as informações necessárias para configurar seu dispositivo VPN. 

Se você já tiver um gateway de VPN e quiser alterar o tipo de roteamento de VPN, consulte [Como alterar o tipo de roteamento de VPN para o gateway](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Criar um gateway de VPN
1. No [Portal Clássico do Azure](https://manage.windowsazure.com), na página **Redes**, verifique se a coluna de status para sua rede virtual indica **Criado**.
2. Na coluna **Nome** , clique no nome de sua rede virtual.
3. Na página **Painel** , observe que essa rede virtual ainda não tem um gateway configurado. Você verá esse status se alterar à medida que percorrer as etapas para configurar seu gateway.

![Gateway não criado](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)

Em seguida, na parte inferior da página, clique em **Criar Gateway**. Você pode selecionar um *Roteamento Estático* ou um *Roteamento Dinâmico*. O tipo de roteamento de VPN selecionado dependerá de alguns fatores. Por exemplo, para que seu dispositivo VPN dá suporte e se você precisa dar suporte a conexões de ponto a site. Verifique [Sobre dispositivos VPN para conectividade de rede virtual](vpn-gateway-about-vpn-devices.md) para verificar o tipo de roteamento de VPN de que você precisa. Após a criação do gateway, não será possível alternar entre os tipos de roteamento de VPN de gateway sem precisar excluir e recriar o gateway. Quando o sistema solicitar que você confirme se deseja criar o gateway, clique em **Sim**.

![Tipo de roteamento do VPN do gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Quando o gateway estiver sendo criado, observe que o gráfico de gateway na página muda para amarelo e indica *Criando Gateway*. Pode levar até 45 minutos para que o gateway seja criado. Aguarde até que o gateway seja concluído antes de poder prosseguir com outras definições de configuração.

![Criação de gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Quando o gateway mudar para *Conectando*, você poderá coletar as informações necessárias para o seu dispositivo VPN.

![Conexão de gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="site-to-site-connections"></a>Conexões site a site

### <a name="step-1-gather-information-for-your-vpn-device-configuration"></a>Etapa 1. Coletar informações de configuração do dispositivo VPN
Se estiver criando uma conexão site a site, depois que o gateway tiver sido criado, colete as informações para a configuração do dispositivo VPN. Essas informações estão localizadas na página **Painel** para sua rede virtual:

1. **Endereço IP do gateway -** o endereço IP pode ser encontrado na página **Painel**. Você não poderá vê-lo após a conclusão da criação do gateway.
2. **Chave compartilhada -** clique em **Gerenciar Chave** na parte inferior da tela. Clique no ícone ao lado da chave para copiá-la para a área de transferência e, em seguida, cole e salve a chave. Observe que esse botão funcionará apenas quando houver um único túnel VPN S2S. Se você tiver vários túneis VPN S2S, use o cmdlet do PowerShell ou a API *Obter Chave Compartilhada do Gateway de Rede Virtual*.

![Gerenciar Chave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)

### <a name="step-2--configure-your-vpn-device"></a>Etapa 2.  Configurar o dispositivo de VPN
As conexões Site a Site para uma rede local exigem um dispositivo VPN. Enquanto não fornecemos as etapas de configuração para todos os dispositivos VPN, você pode achar úteis as informações nos links a seguir:

- Para obter informações sobre os dispositivos VPN compatíveis, consulte [Dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- Para obter links para as configurações do dispositivo, consulte [Dispositivos VPN Validados](vpn-gateway-about-vpn-devices.md#devicetable). Esses links são fornecidos em uma base de melhor esforço. Sempre é melhor verificar com o fabricante do dispositivo para obter as informações de configuração mais recentes.
- Para obter informações sobre a edição dos exemplos de configuração do dispositivo, consulte [Edição de exemplos](vpn-gateway-about-vpn-devices.md#editing).
- Para obter os parâmetros IPsec/IKE, consulte [Parâmetros](vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar seu dispositivo VPN, verifique se há [Problemas de compatibilidade de dispositivo conhecidos](vpn-gateway-about-vpn-devices.md#known) em relação ao dispositivo VPN que você deseja usar.

Ao configurar seu dispositivo VPN, você precisará dos seguintes itens:

- O endereço IP público do seu gateway de rede virtual. Você pode localizar isso acessando a folha **Visão geral** de sua rede virtual.
- Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada muito básica. Você deve gerar uma chave mais complexa para uso.

Depois que o dispositivo VPN tiver sido configurado, você poderá exibir as informações de conexão atualizadas na página Painel para sua rede virtual.

### <a name="step-3-verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Etapa 3. Verificar seus intervalos de rede local e de endereços IP do gateway de VPN
#### <a name="verify-your-vpn-gateway-ip-address"></a>Verificar o endereço IP do gateway de VPN
Para que o gateway se conecte adequadamente, o endereço IP do seu dispositivo VPN deverá ser configurado de forma correta para a Rede Local especificada para sua configuração entre locais. Normalmente, isso é configurado durante o processo de configuração de site a site. No entanto, se você usou essa rede local com um dispositivo diferente ou se o endereço IP tiver sido alterado para a rede local, edite as configurações para especificar o endereço IP de Gateway correto.

1. Para verificar o endereço IP do gateway, clique em **Redes** no painel esquerdo do portal e então selecione **Redes Locais** na parte superior da página. Você verá o Endereço do Gateway de VPN para cada rede local que criou. Para editar o endereço IP, selecione a rede virtual e clique em **Editar** na parte inferior da página.
2. Na página **Especificar os detalhes da sua rede local** , edite o endereço IP e então clique na seta de avanço na parte inferior da página.
3. Na página **Especificar o espaço de endereço** , clique na marca de seleção no canto inferior direito para salvar suas configurações.

#### <a name="verify-the-address-ranges-for-your-local-networks"></a>Verifique os intervalos de endereços para suas redes locais
Para o tráfego correto fluir através do gateway para sua localização, você precisa verificar se cada intervalo de endereços IP está especificado. Cada intervalo deve estar listado na sua configuração de **Redes Locais** do Azure. Dependendo da configuração de rede da sua localização local, essa pode ser uma tarefa um pouco grande. O tráfego associado a um endereço IP contido nos intervalos listados será enviado pelo gateway de VPN da rede virtual. Os intervalos listados não precisam ser intervalos privados, embora seja necessário verificar se a sua configuração local pode receber o tráfego de entrada.

Para adicionar ou editar os intervalos de uma Rede Local, use as seguintes etapas:

1. Para verificar os intervalos de endereços IP para uma rede local, clique em **Redes** no painel esquerdo do portal e então selecione **Redes Locais** na parte superior da página. No portal, a maneira mais fácil de exibir os intervalos listados é na página **Editar** . Para ver seus intervalos, selecione a rede virtual e clique em **Editar** na parte inferior da página.
2. Na página **Especificar os detalhes da sua rede local** , não faça nenhuma alteração. Clique na seta de avanço na parte inferior da página.
3. Na página **Especificar o espaço de endereço** , faça as alterações de espaço de endereço de rede. Em seguida, clique na marca de seleção para salvar sua configuração.

## <a name="how-to-view-gateway-traffic"></a>Como exibir o tráfego do gateway
Você pode exibir seu gateway e o tráfego do gateway da página **Painel** da Rede Virtual.

Na página **Painel** , você pode exibir o seguinte:

* A quantidade de dados que está fluindo pelo gateway, tanto pela entrada de dados como pela saída de dados.
* Os nomes dos servidores DNS especificados para sua rede virtual.
* A conexão entre o gateway e seu dispositivo VPN.
* A chave compartilhada é usada para configurar a conexão do gateway para seu dispositivo VPN.

## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Como alterar o tipo de roteamento de VPN para o gateway
Como algumas configurações de conectividade estão disponíveis apenas para determinados tipos de roteamento de gateway, você pode achar que precisa alterar o tipo de roteamento de gateway VPN de um gateway de VPN existente. Por exemplo, talvez você queira adicionar conectividade ponto a site a uma conexão site a site já existente com um gateway estático. As conexões ponto a site exigem um gateway dinâmico. Isso significa que para configurar uma conexão P2S, você precisa alterar seu tipo de roteamento de VPN de gateway de estático para dinâmico.

Se precisar alterar um tipo de roteamento de VPN de gateway, você deverá excluir o gateway existente e então criar um novo gateway com o novo tipo de roteamento. Não é necessário excluir a rede virtual inteira para alterar o tipo de roteamento do gateway.

Antes de alterar seu tipo de roteamento de gateway de VPN, verifique se o seu dispositivo VPN dá suporte ao tipo de roteamento que você deseja usar. Para baixar os novos exemplos de configuração de roteamento e verificar os requisitos do dispositivo VPN, veja [Sobre dispositivos VPN para conectividade de rede virtual](vpn-gateway-about-vpn-devices.md).

> [!IMPORTANT]
> Quando você exclui um gateway de VPN da rede virtual, o VIP atribuído ao gateway é liberado. Quando você recriar o gateway, um novo VIP será atribuído a ele.
> 
> 

1. **Exclua o gateway de VPN existente.**
   
    Na página **Painel** para sua rede virtual, navegue até a parte inferior da página e clique em **Excluir Gateway**. Aguarde a notificação de que o gateway foi excluído. Após receber a notificação na tela de que o gateway foi excluído, você poderá criar um novo gateway.
2. **Criar um gateway de VPN.**
   
    Use o procedimento na parte superior da página para criar um novo gateway: [Criar um gateway de VPN](#create-a-vpn-gateway).

## <a name="next-steps"></a>Próximas etapas
Você pode adicionar máquinas virtuais à sua rede virtual. Veja [Como criar uma máquina virtual personalizada](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Se você quiser configurar uma conexão VPN ponto a site, consulte [Configurar uma conexão VPN ponto a site](vpn-gateway-point-to-site-create.md).


