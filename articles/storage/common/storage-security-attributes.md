---
title: Atributos comuns de segurança do armazenamento do Azure
description: Uma lista de verificação de atributos de segurança comuns para avaliar o armazenamento do Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 33765d57f1a0e5788011b2d9d9c2f57d06713ddb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680682"
---
# <a name="common-security-attributes-for-azure-storage"></a>Atributos comuns de segurança do armazenamento do Azure

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo documenta os atributos comuns de segurança incorporados no armazenamento do Azure. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim |  |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Suporte a mecanismos HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de ser transmitido para o serviço. |
| Manipulação de chaves de criptografia (CMK, BYOK, etc.)| Sim | Ver [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Criptografia de nível de coluna (Serviços de Dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte para ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNET| N/D |  |
| Isolamento de rede/Suporte a firewall| Sim | |
| Suporte para túnel forçado | N/D |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Métricas do Azure Monitor disponível agora, registra a visualização inicial |

## <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | Azure Active Directory, chave compartilhada, o token de acesso compartilhado. |
| Gerenciamento de acesso - Autorização| Sim | Autorização de suporte por meio de RBAC, ACLs POSIX e Tokens de SAS |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento | Sim | Log de atividades do Azure Resource Manager |
| Registro em log e auditoria de plano de dados| Sim | Os Logs de diagnóstico do serviço e registro em log do Azure Monitor visualização inicial  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Suporte a controle de versão do provedor de recursos por meio de APIs do Gerenciador de recursos do Azure |