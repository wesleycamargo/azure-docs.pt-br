---
title: Como se preparar para uma alteração de endereço IP de entrada - Azure
description: Se seu endereço IP de entrada vai ser alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
services: app-service\web
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: tdykstra
ms.openlocfilehash: 5d30357e5308de0224590703bb5e68fbe73b882b
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343426"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP de entrada

Se você recebeu uma notificação de que o endereço IP de entrada do seu aplicativo do Serviço de Aplicativo do Azure está mudando, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinar se você precisa fazer alguma coisa

* Opção 1: se seu aplicativo do Serviço de Aplicativo não tiver um Domínio Personalizado, nenhuma ação é necessária.

* Opção 2: se apenas um registro CNAME (registro DNS que aponta para um URI) é configurado no Portal de Registro de Domínio (Provedor DNS de terceiros ou DNS do Azure), nenhuma ação é necessária.

* Opção 3: se um registro (registro DNS que aponta diretamente para o endereço IP) for configurado no Portal de Registro de Domínio (Provedor DNS de terceiros ou DNS do Azure), substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

* Opção 4: se seu aplicativo estiver atrás de um balanceador de carga, filtro de IP ou qualquer outro mecanismo IP que requer um endereço IP do aplicativo, substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Você pode encontrar o novo Endereço IP de entrada no Portal do Azure.

O novo endereço IP de entrada que está sendo fornecido para o aplicativo está no portal no campo **Endereço IP Virtual**. Esse novo endereço IP e o antigo estão conectados ao seu aplicativo agora, e mais tarde o antigo será desconectado.

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicativos**.

3.  Selecione o aplicativo do Serviço de Aplicativo na lista.

4.  Se o aplicativo é um aplicativo de funções, consulte [aplicativo de funções endereço IP de entrada](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  No cabeçalho **Configurações**, clique em **Propriedades** no painel de navegação esquerdo e localize a seção rotulada como **Endereço IP Virtual**.

5. Copie o endereço IP e reconfigure o registro de domínio ou o mecanismo de IP.

## <a name="next-steps"></a>Próximas etapas

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no Serviço de Aplicativo do Azure, consulte [Entrada e endereços IP de saída no Serviço de Aplicativo do Azure](app-service-ip-addresses.md).
