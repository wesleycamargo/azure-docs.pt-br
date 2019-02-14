---
title: Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy | Microsoft Docs
description: Descubra quais portas abrir para o Application Proxy do Azure AD funcionar corretamente
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: efc8c0f0581878d41eb62cbfbd88aec730ecf789
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175227"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy

Para ver uma lista completa das portas necessárias e a função de cada porta, consulte a seção pré-requisitos da [documentação de Application Proxy](application-proxy-add-on-premises-application.md). observe que o Application Proxy usa apenas as portas de saída.

Você também pode verificar se você tem todas as portas abertas necessárias, abrindo a [ferramenta de teste de portas do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) de sua rede local. Mais marcas de seleção verde significa maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Proxy de aplicativo

Estamos trabalhando em uma maneira para que você saiba que essas regiões precisam estar verdes para você. Por enquanto, certifique-se de que todas estão verdes. EUA Central também é necessária, independentemente de qual região você está.

Para certificar-se de que a ferramenta oferece os resultados certos, certifique-se de:

-   Abrir a ferramenta em um navegador do servidor na qual você instalou o conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis a seu conector também estejam aplicados a esta página. Isso pode ser feito no Internet Explorer, vá para **Configurações** -&gt;**Opções da Internet** -&gt;**Conexões** -&gt;**Configurações da Lan**. Nessa página, você vê o campo "Use um Servidor Proxy para sua LAN". Marque essa caixa e coloque o endereço do proxy no campo "Endereço".

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
