---
title: Azure Front Door Service – Segurança de camada de aplicativo | Microsoft Docs
description: Este artigo ajuda a entender como o Azure Front Door Service permite proteger os back-ends de seus aplicativos
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984369"
---
# <a name="application-layer-security-with-front-door"></a>Segurança da camada de aplicativo com o Front Door
O Azure Front Door Service fornece funcionalidade de proteção de aplicativo Web para proteger seus aplicativos Web contra ataques de rede e explorações de vulnerabilidades da Web comuns, como Injeção de SQL ou XSS (Cross Site Scripting). Habilitado para front-ends HTTP(S), a segurança de camada de aplicativo do Front Door é distribuída globalmente e permanece sempre ativa, impedindo ataques mal-intencionados na borda da rede do Azure, longe de seus back-ends. Com mais segurança e otimização de desempenho, o Front Door oferece experiências da Web rápidas e seguras aos usuários finais.

## <a name="application-protection"></a>Proteção do aplicativo
A proteção de aplicativo do Front Door é configurada em cada ambiente de borda em todo o mundo, de forma alinhada com os aplicativos, e impede automaticamente o tráfego não HTTP(S) de acessar seus aplicativos Web. Nossa arquitetura distribuída multilocatário permite a proteção global em escala sem sacrificar o desempenho. Para cargas de trabalho HTTP(S), o serviço de proteção de aplicativo Web do Front Door fornece um mecanismo de regras avançado para regras personalizadas, um conjunto de regras pré-configurado contra ataques comuns e o registro em log detalhado de todas as solicitações que correspondem a uma regra. Há suporte somente para ações flexíveis como permitir, bloquear ou registrar em log.

## <a name="custom-access-control-rules"></a>Regras personalizadas de controle de acesso
- **Permissões de IP lista e a lista de bloqueios:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos web com base na lista de endereços IP do cliente. Há suporte para IP v4 e IP v6
- **Controle de acesso com base geográfica:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos web com base em um IP do cliente é de código do país
- **Parâmetros HTTP de filtragem:** Você pode configurar regras de acesso personalizado com base na correspondência de parâmetros de solicitação de HTTP (s), incluindo os cabeçalhos, URL e cadeias de caracteres de consulta

## <a name="azure-managed-rules"></a>Regras gerenciadas pelo Azure
- Um conjunto predefinido de regras relacionadas às vulnerabilidades de OWASP mais comuns é habilitado por padrão. Na versão prévia, o conjunto de regras inclui a verificação de solicitações de SQLI e XSS. Mais regras serão adicionadas. Você pode optar por começar com a ação de somente registrar em log para validar se as regras pré-configuradas funcionam conforme o esperado nos aplicativos 

## <a name="rate-limiting"></a>Limitação de taxa
- Uma regra de controle de taxa tem a finalidade de limitar tráfego anormalmente alto proveniente de qualquer IP de cliente.  Você pode definir um número limite de solicitações Web permitidas para um IP de cliente no intervalo de um minuto.

## <a name="centralized-protection-policy"></a>Política de proteção centralizada
- Você pode definir várias regras de proteção e adicioná-las a uma política em ordem de prioridade. As regras personalizadas têm prioridade mais alta que o conjunto de regras gerenciadas para permitir exceções. Uma única política é associada ao aplicativo Web.  A mesma política de proteção do aplicativo Web é replicada para todos os servidores de borda em todas as localizações, garantindo uma política de segurança consistente entre todas as regiões

## <a name="configuration"></a>Configuração
- Durante a versão prévia, você pode usar APIs REST, o PowerShell ou a CLI para criar e implantar políticas e regras de proteção de aplicativo do Front Door. O acesso ao portal terá suporte antes que o serviço fique disponível. 


## <a name="monitoring"></a>Monitoramento
O Front Door possibilita monitorar aplicativos Web contra ataques usando métricas em tempo real que são integradas ao Azure Monitor para controlar alertas e monitorar tendências com facilidade.

## <a name="pricing"></a>Preços
A segurança da camada de aplicativo do Front Door é gratuita durante a versão prévia.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
