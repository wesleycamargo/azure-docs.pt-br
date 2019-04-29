---
title: Firewall do aplicativo web do Azure - perguntas frequentes
description: Esta página fornece respostas para perguntas frequentes sobre o serviço de porta da frente do Azure
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461962"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Perguntas frequentes sobre o firewall do aplicativo web do Azure

Este artigo responde a perguntas comuns sobre a funcionalidade e recursos do WAF (firewall) de aplicativo web do Azure. 

## <a name="what-is-azure-waf"></a>O que é o WAF do Azure?

O WAF do Azure é um firewall do aplicativo web que ajuda a proteger seus aplicativos web contra as ameaças comuns, como SQL injeção, scripts entre sites e outras vulnerabilidades da web. Você pode definir uma política de WAF consiste em uma combinação de regras personalizadas e gerenciadas para controlar o acesso aos seus aplicativos web.

Uma política de WAF do Azure pode ser aplicada a aplicativos web hospedados nos serviços de Gateway de aplicativo ou da frente do Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>O que é o WAF da frente do serviço do Azure? 

Porta da frente do Azure é uma rede de distribuição de conteúdo e aplicativos altamente escalonável, distribuído globalmente. O WAF do Azure, quando integrada com a porta da frente, para de negação de serviço e ataques de aplicativo na borda da rede do Azure direcionados, fontes de ataque antes de entrar na rede virtual, oferece proteção sem sacrificar o desempenho.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Como serei cobrado do WAF do Azure para a porta da frente?
Durante a visualização pública, o uso do WAF na frente são gratuitos. Observe que o encargo de porta da frente é extra. Consulte o preço do serviço de porta da frente [aqui](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>O WAF do Azure dá suporte a HTTPS?

Serviço de porta da frente oferece o descarregamento de SSL. WAF é integrado nativamente com a porta da frente e pode inspecionar uma solicitação depois que ele é descriptografado.

## <a name="does-azure-waf-support-ipv6"></a>O WAF do Azure dá suporte a IPv6?

Sim. Você pode configurar a restrição de IP para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Grau de atualização são conjuntos de regras gerenciado?

Façamos o melhor para acompanhar a alteração do panorama de ameaças. Depois que uma nova regra é atualizada, ele é adicionado ao padrão conjunto de regras com um novo número de versão.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>O que é o tempo de propagação, se eu fizer uma alteração à minha política WAF?

Implantar uma política de WAF globalmente geralmente leva cerca de 5 minutos e geralmente é concluída mais cedo.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Políticas de WAF podem ser diferentes para diferentes regiões?

Quando integrado com o serviço de porta da frente, o WAF é um recurso global. Mesma configuração aplica-se em todos os locais de porta da frente.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Como limitar o acesso ao meu back-end a ser somente da porta da frente?

Você pode configurar a lista de controle de acesso de IP em seu back-end para permitir apenas da frente saídos intervalos de endereços IP e negar qualquer acesso direto da Internet. As marcas de serviço têm suporte para uso em sua rede virtual. Além disso, você pode verificar se o campo de cabeçalho HTTP X-Forwarded-Host é válido para seu aplicativo web.




## <a name="which-azure-waf-options-should-i-choose"></a>Quais opções de WAF do Azure devo escolher?

Há duas opções ao aplicar as políticas de WAF no Azure. O WAF da frente com o Azure é uma solução de segurança de borda distribuídos globalmente. WAF no Gateway de aplicativo é uma solução regional e dedicada. É recomendável que você escolha uma solução com base nas necessidades de desempenho e a segurança geral. Para obter mais informações, consulte [balanceamento de carga com o conjunto de entrega de aplicativos do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Há suporte para recursos do WAF mesmos em todas as plataformas integradas?

Atualmente, ModSec CRS 2.2.9 e regras de CRS 3.0 só têm suporte com o WAF no Gateway de aplicativo. A limitação de taxa, a filtragem geográfica e do Azure gerenciadas regras padrão do conjunto de regras são suportadas apenas com o WAF da frente no Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Proteção contra DDoS é integrada com a porta da frente? 

Distribuído globalmente nas bordas de rede do Azure, porta de entrada do Azure pode absorver e geograficamente isolar os ataques de grande volume. Você pode criar a política personalizada do WAF para bloco automaticamente e a taxa de limitar os ataques de HTTP (s) que têm assinaturas de conhecidos. Ainda mais além disso, você pode habilitar a proteção de DDoS Standard na rede virtual em que o seu back-ends são implantados. Os clientes de proteção contra DDoS Standard do Azure recebem benefícios adicionais, incluindo a proteção de custos, garantia de SLA e o acesso aos especialistas da equipe de resposta rápida de DDoS para ajuda imediata durante um ataque. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [firewall do aplicativo web do Azure](waf-overview.md).
- Saiba mais sobre [do Azure da frente](front-door-overview.md).
