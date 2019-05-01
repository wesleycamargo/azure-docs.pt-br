---
title: Atributos comuns de segurança para o Azure Key Vault
description: Uma lista de verificação de atributos de segurança comuns para avaliar o Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 652e6ecb9ef9506fd306942eae45b13b52ae1c24
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691821"
---
# <a name="common-security-attributes-for-azure-key-vault"></a>Atributos comuns de segurança para o Azure Key Vault

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comum embutidos no Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | Todos os objetos são criptografados. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de rede virtual</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Toda a comunicação é por meio de chamadas à API criptografadas |
| Tratamento de chaves de criptografia (CMK, BYOK, etc.)| Sim | O cliente controla todas as chaves em seu Cofre de chaves. Quando as chaves de HSM (módulo) com suporte de segurança de hardware são especificadas, um HSM do FIPS nível 2 protege a chave, o certificado ou o segredo. |
| Criptografia de nível de coluna (serviços de dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Usando HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte de ponto de extremidade de serviço| Sim | Usando pontos de extremidade de serviço de rede Virtual (VNet). |
| Suporte à injeção de rede virtual| Não  |  |
| Isolamento de rede e suporte de firewall| Sim | Usando regras de firewall de rede virtual. |
| Forçado suporte por túnel| Não  |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Usando o Log Analytics. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Authentication| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | Usando a política de acesso ao Key Vault. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Usando o Log Analytics. |
| Auditoria e log de plano de dados| Sim | Usando o Log Analytics. |

## <a name="access-controls"></a>Controles de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Controles de acesso do plano de controle/gerenciamento | Sim | RBAC (Controle de Acesso Baseado em Função) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso ao Key Vault |