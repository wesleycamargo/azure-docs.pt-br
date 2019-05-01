---
title: Atributos de segurança comuns para mensagens do barramento de serviço do Azure
description: Uma lista de verificação de atributos de segurança comuns para avaliar mensagens do barramento de serviço do Azure
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513527"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>Atributos de segurança comuns para mensagens do barramento de serviço do Azure

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comuns integrados de mensagens do barramento de serviço do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>|  Sim para criptografia em repouso do lado do servidor por padrão. | Chaves gerenciadas pelo cliente e o BYOK ainda não têm suporte. Criptografia do cliente é responsabilidade do cliente |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de rede virtual</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Dá suporte ao mecanismo HTTPS/TLS padrão. |
| Tratamento de chaves de criptografia (CMK, BYOK, etc.)| Não  |   |
| Criptografia de nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas à API criptografadas| Sim | Chamadas à API são feitas por meio [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte de ponto de extremidade de serviço| Sim (somente para a camada Premium) | Há suporte para pontos de extremidade de serviço de rede virtual para [camada Premium do barramento de serviço](service-bus-premium-messaging.md) apenas. |
| Suporte à injeção de rede virtual| Não  | |
| Isolamento de rede e suporte de firewall| Sim (somente para a camada Premium) |  |
| Forçado suporte por túnel| Não  |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Com suporte via [o Azure Monitor e alertas](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Authentication| Sim | Gerenciado por meio [Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md); consulte [do barramento de serviço de autenticação e autorização](service-bus-authentication-and-authorization.md).|
| Autorização| Sim | Oferece suporte à autorização por meio [RBAC](service-bus-role-based-access-control.md) (versão prévia) e o token de SAS, consulte [do barramento de serviço de autenticação e autorização](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Auditoria e log de plano de controle e gerenciamento| Sim | Logs de operações estão disponíveis; ver [logs de diagnóstico do barramento de serviço](service-bus-diagnostic-logs.md).  |
| Auditoria e log de plano de dados| Não  |  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Dá suporte a controle de versão de provedor de recursos por meio de [API do Azure Resource Manager](/rest/api/resources/).|
