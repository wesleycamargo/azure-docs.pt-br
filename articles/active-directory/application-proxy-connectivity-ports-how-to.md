---
title: Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy | Microsoft Docs
description: Descubra quais portas abrir para o Application Proxy do Azure AD funcionar corretamente
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 72acfbd21159e15fe237be6d509cb2c4a2b1bffd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy

Para ver uma lista completa das portas necessárias e a função de cada porta, consulte a seção pré-requisitos da [documentação de Application Proxy](manage-apps/application-proxy-enable.md). observe que o Application Proxy usa apenas as portas de saída.

Você também pode verificar se você tem todas as portas abertas necessárias, abrindo a [ferramenta de teste de portas do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) de sua rede local. Mais marcas de seleção verde significa maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Proxy de aplicativo

Estamos trabalhando em uma maneira para que você saiba que essas regiões precisam estar verdes para você. Por enquanto, certifique-se de que todas estão verdes. EUA Central também é necessária, independentemente de qual região você está.

Para certificar-se de que a ferramenta oferece os resultados certos, certifique-se de:

-   Abrir a ferramenta em um navegador do servidor na qual você instalou o conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis a seu conector também estejam aplicados a esta página. Isso pode ser feito no Internet Explorer, vá para **Configurações** -&gt;**Opções da Internet** -&gt;**Conexões** -&gt;**Configurações da Lan**. Nessa página, você vê o campo "Use um Servidor Proxy para sua LAN". Marque essa caixa e coloque o endereço do proxy no campo "Endereço".

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](manage-apps/application-proxy-connectors.md)
