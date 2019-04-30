---
title: Configurações de diretiva de firewall do aplicativo web da frente com o Azure
description: Saiba o firewall do aplicativo web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459361"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Configurações de diretiva de firewall do aplicativo web da frente com o Azure

Uma política de Firewall de aplicativo da Web (WAF) permite que você controle o acesso aos seus aplicativos web por um conjunto de regras personalizadas e gerenciados. O nome da política de WAF deve ser exclusivo. Se você tentar usar um nome existente, você receberá um erro de validação. Há várias configurações de nível de política que se aplicam a todas as regras especificadas para essa política, conforme descrito neste artigo.

## <a name="waf-state"></a>Estado de WAF

Uma política de WAF para frente pode estar em um dos dois estados:
- **Habilitado:** Quando uma política está habilitada, WAF ativamente está inspecionando as solicitações de entrada e usa as ações correspondentes de acordo com as definições de regra
- **Desabilitado:** - quando uma política estiver desabilitada, inspeção de WAF está em pausa. Solicitações de entrada ignorarão WAF e são enviadas ao back-ends com base no roteamento de porta da frente.

## <a name="waf-mode"></a>Modo de WAF

Política de WAF pode ser configurada para ser executado em dois modos a seguir:

- **Modo de detecção** quando executado no modo de detecção, WAF não realizar nenhuma ação que não seja o monitor e registrar a solicitação e sua regra correspondente do WAF em logs de WAF. Ativar o log de diagnóstico para a frente (ao usar o portal, isso pode ser conseguido indo para o **diagnóstico** seção no portal do Azure).

- **Modo de prevenção** quando configurado para ser executado no modo de prevenção, o WAF usa a ação especificada se uma solicitação corresponde a uma regra. Todas as solicitações correspondentes também são registradas nos logs de WAF.

## <a name="waf-response-for-blocked-requests"></a>Resposta de WAF para solicitações bloqueadas

Por padrão, quando o WAF bloqueia uma solicitação devido a uma regra correspondente, ele retorna um código de 403 status com - **a solicitação é bloqueada** mensagem. Uma cadeia de caracteres de referência também é retornada para registro em log.

Você pode definir um código de status de resposta personalizada e uma mensagem de resposta quando uma solicitação é bloqueada pelo WAF. Os seguintes códigos de status personalizadas têm suporte:

- 200    OK
- 403 Proibido
- 405 método não permitido
- 406 não aceitável
- 429 número excessivo de solicitações

Mensagem de resposta e o código de status de resposta personalizada é uma configuração de nível de política. Quando ela estiver configurada, todas as solicitações bloqueadas obtém o mesmo status de resposta personalizada e a mensagem de resposta.

## <a name="uri-for-redirect-action"></a>URI de redirecionamento de ação

Será necessário definir um URI para redirecionar as solicitações como se o **REDIRECIONAR** ação é selecionada para qualquer uma das regras contidas em uma política de WAF. Esse redirecionamento URI deve ser um site válido do HTTP (S) e uma vez configurado, todas as solicitações de regras de correspondência com uma ação de "REDIRECIONAMENTO" serão redirecionadas para o site especificado.


## <a name="next-steps"></a>Próximas etapas
- Saiba como definir o WAF [respostas personalizadas](waf-front-door-configure-custom-response-code.md)
