---
title: Preparar-se para uma alteração de endereço IP de entrada – Serviço de Aplicativo do Azure
description: Se seu endereço IP de entrada vai ser alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aaa89b5a3bb1af6878ed21e0160a534a1c989228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270057"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP de entrada

Se você recebeu uma notificação de que o endereço IP de entrada do seu aplicativo do Serviço de Aplicativo do Azure está mudando, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinar se você precisa fazer alguma coisa

* Opção 1: se seu aplicativo do Serviço de Aplicativo não tiver um domínio personalizado, nenhuma ação será necessária.

* Opção 2: se apenas um registro CNAME (registro DNS que aponta para um URI) estiver configurado no Portal de Registro de Domínio (provedor DNS de terceiros ou DNS do Azure), nenhuma ação será necessária.

* Opção 3: se um registro A (registro DNS que aponta diretamente para o endereço IP) estiver configurado no Portal de Registro de Domínio (provedor DNS de terceiros ou DNS do Azure), substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

* Opção 4: se seu aplicativo estiver atrás de um balanceador de carga, filtro de IP ou qualquer outro mecanismo de IP que exija um endereço IP do aplicativo, substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Você pode encontrar o novo Endereço IP de entrada no Portal do Azure.

O novo endereço IP de entrada que está sendo fornecido para o aplicativo está no portal no campo **Endereço IP Virtual**. Esse novo endereço IP e o antigo estão conectados ao seu aplicativo agora, e mais tarde o antigo será desconectado.

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicativos**.

3.  Selecione o aplicativo do Serviço de Aplicativo na lista.

1.  Se o aplicativo é um aplicativo de funções, consulte [aplicativo de funções endereço IP de entrada](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  No cabeçalho **Configurações**, clique em **Propriedades** no painel de navegação esquerdo e localize a seção rotulada como **Endereço IP Virtual**.

5. Copie o endereço IP e reconfigure o registro de domínio ou o mecanismo de IP.

## <a name="next-steps"></a>Próximas etapas

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no Serviço de Aplicativo do Azure, consulte [Entrada e endereços IP de saída no Serviço de Aplicativo do Azure](overview-inbound-outbound-ips.md).
