---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c1680f5b5aead08a927bac4b3234f57a78527248
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
1. No lado esquerdo da página do portal, clique em **+** e digite 'Gateway de Rede Virtual' na pesquisa. Em **Resultados**, localize e clique em **Gateway de rede virtual**.
2. Na parte inferior da página ‘Gateway de rede virtual’, clique em **Criar**. Isso abre a página **Criar gateway de rede virtual**.
3. Na página **Criar gateway de rede virtual**, especifique os valores do seu gateway de rede virtual.

    ![Campos da página Criar gateway de rede virtual](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Novo gateway")

  - **Nome**: nomeie o seu gateway. Isso não é igual a nomear uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
  - **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual do tipo **VPN**. 
  - **Tipo de VPN**: selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações exige um tipo de VPN baseado em rota.
  - **SKU**: selecione o SKU de gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Para saber mais sobre os SKUs de gateway, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Local**: talvez seja necessário rolar para ver o Local. Ajuste o campo **Local** para apontar para o local onde se encontra sua rede virtual. Se o local não estiver apontando para a região onde reside sua rede virtual, ao selecionar uma rede virtual na próxima etapa, ela não vai aparecer na lista suspensa.
  - **Rede virtual**: escolha a rede virtual à qual você deseja adicionar este gateway. Clique em **Rede virtual** para abrir a página ‘Escolher uma rede virtual’. Selecione a rede virtual. Se você não vir a sua rede virtual, verifique se o campo Local está apontando para a região na qual sua rede virtual está localizada.
  - **Intervalo de endereços de sub-rede de gateway**: você só verá essa configuração se anteriormente não tiver criado uma sub-rede de gateway para sua rede virtual. Se você criou uma sub-rede de gateway válida previamente, essa configuração não será exibida.
  - **Primeira configuração de IP**: a página ‘Escolher endereço IP público’ cria um objeto de endereço IP público que se associa ao gateway de VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

    - Primeiro, clique em **Criar configuração de IP de gateway** para abrir a página ‘Escolher endereço IP público’, depois clique em **+Criar novo** para abrir a página ‘Criar endereço IP público’.
    - A seguir, dê um **Nome** para o seu endereço IP público. Deixe o SKU como **Básico** a menos que haja um motivo específico para alterá-lo para algo diferente, depois clique em **OK** na parte inferior dessa página para salvar suas alterações.

      ![Criar IP público](./media/vpn-gateway-add-gateway-portal-include/public-ip-address-name.png "Criar IP")

4. Verifique as configurações. Você pode selecionar **Fixar no painel** na parte inferior da página se quiser que seu gateway apareça no painel. 
5. Clique em **Criar** para começar a criar o gateway de VPN. As configurações são validadas, e você verá o bloco "Implantar gateway de rede virtual" no painel. A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

Depois de criar o gateway, exiba o endereço IP atribuído a ele observando a rede virtual no portal. O gateway aparecerá como um dispositivo conectado. Você pode clicar no dispositivo conectado (seu gateway de rede virtual) para exibir mais informações.