---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2c05feffc0b0cebe71083b8ec918204843d0720
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409809"
---
1. No portal, do lado esquerdo, clique em **+Criar um recurso** e digite “gateway de rede virtual” na pesquisa. Localize **Gateway de rede virtual** na pesquisa, retorne e clique na entrada. Na página **Gateway da rede virtual**, clique em **Criar** na parte inferior da página para abrir a página **Criar gateway da rede virtual**.
2. Na página **Criar gateway de rede virtual**, preencha os valores para seu gateway de rede virtual.

  ![Criar campos na página do gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Criar campos na página do gateway de rede virtual")
3. Na página **Criar gateway de rede virtual**, especifique os valores do seu gateway de rede virtual.

  - **Nome**: nomeie o seu gateway. Isso não é igual a nomear uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
  - **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual do tipo **VPN**. 
  - **Tipo de VPN**: selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações exige um tipo de VPN baseado em rota.
  - **SKU**: selecione o SKU de gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Para saber mais sobre os SKUs de gateway, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    Só selecione **Habilitar o modo ativo-ativo** se você estiver criando uma configuração de gateway ativo-ativo. Caso contrário, deixe essa configuração desmarcada.
  - **Local**: talvez seja necessário rolar para ver o Local. Ajuste o campo **Local** para apontar para o local onde se encontra sua rede virtual. Por exemplo, Oeste dos EUA. Se o local não estiver apontando para a região onde reside sua rede virtual, ao selecionar uma rede virtual na próxima etapa, ela não vai aparecer na lista suspensa.
  - **Rede virtual**: escolha a rede virtual à qual você deseja adicionar este gateway. Clique em **Rede virtual** para abrir a página ‘Escolher uma rede virtual’. Selecione a rede virtual. Se você não vir a sua rede virtual, verifique se o campo Local está apontando para a região na qual sua rede virtual está localizada.
  - **Intervalo de endereços de sub-rede de gateway**: você só verá essa configuração se anteriormente não tiver criado uma sub-rede de gateway para sua rede virtual. Se você criou uma sub-rede de gateway válida previamente, essa configuração não será exibida.
  - **Endereço IP público**: essa configuração especifica o objeto de endereço IP público associado ao gateway da VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

    - Deixe **Criar novo** selecionado.
    - Na caixa de texto, digite um **Nome** para seu endereço IP público.

4. Deixe **Configurar BGP ASN** desmarcada, a menos que sua configuração exija especificamente essa configuração. Se você precisar dessa configuração, o ASN padrão é 65515, embora isso possa ser alterado.
5. Verifique as configurações. Você pode selecionar **Fixar no painel** na parte inferior da página se quiser que seu gateway apareça no painel. 
6. Clique em **Criar** para começar a criar o gateway de VPN. As configurações são validadas, e você verá o bloco "Implantar gateway de rede virtual" no painel. A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

Depois de criar o gateway, exiba o endereço IP atribuído a ele observando a rede virtual no portal. O gateway aparecerá como um dispositivo conectado. Você pode clicar no dispositivo conectado (seu gateway de rede virtual) para exibir mais informações.