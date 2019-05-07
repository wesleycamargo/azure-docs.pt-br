---
title: Atributos de segurança para o banco de dados SQL
description: Uma lista de verificação de atributos de segurança para avaliar o banco de dados SQL
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: df1ffa07c9b813ee3da4952bbcc394f43c69b7ac
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204232"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributos de segurança para o banco de dados SQL

Este artigo documenta os atributos de segurança comuns criados no banco de dados SQL.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Banco de dados SQL do Azure inclui tanto [banco de dados individual](sql-database-single-index.yml) e [instância gerenciada](sql-database-managed-instance.md). As entradas abaixo se aplicam a ambas as ofertas, exceto quando indicado o contrário.

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | Conhecido como "criptografia em uso", conforme descrito no artigo [Always Encrypted](sql-database-always-encrypted.md). Criptografia do lado do serviço usa [a transparent data encryption](transparent-data-encryption-azure-sql.md) (TDE).|
| Criptografia em trânsito:<ul><li>Criptografia de ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Usar HTTPS. |
| Manipulação de chaves de criptografia (CMK, BYOK, etc.)| Sim | Tratamento de chaves gerenciados pelo serviço e gerenciadas pelo cliente são oferecidos (o último por meio [Azure Key Vault](../key-vault/index.yml). |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| Sim | Por meio [Always Encrypted](sql-database-always-encrypted.md). |
| Chamadas criptografadas à API| Sim | Usando HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte para ponto de extremidade de serviço| Sim | Aplica-se ao [banco de dados único](sql-database-single-index.yml) apenas. |
| Suporte à injeção de VNET| Sim | Aplica-se ao [instância gerenciada](sql-database-managed-instance.md) apenas. |
| Isolamento de rede/Suporte a firewall| Sim | Firewall em ambos os banco de dados – e nível de servidor; isolamento de rede [instância gerenciada](sql-database-managed-instance.md) apenas |
| Suporte para túnel forçado | Sim | [a instância gerenciada](sql-database-managed-instance.md) via [do Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | A solução SIEM de terceiros da Imperva (SecureSphere) também é suportada, por meio [Hubs de eventos](../event-hubs/index.yml) integração via [auditoria SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | Azure Active Directory. |
| Gerenciamento de acesso - Autorização| Sim |  |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Sim para apenas alguns eventos. |
| Registro em log e auditoria de plano de dados | Sim | Por meio [auditoria SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Não   | | 

## <a name="additional-security-attributes-for-sql-database"></a>Atributos de segurança adicionais para o banco de dados SQL

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Preventivas: avaliação de vulnerabilidade | Sim | Ver [serviço ajuda você a identificar vulnerabilidades de banco de dados de avaliação de vulnerabilidade de SQL](sql-vulnerability-assessment.md). |
| Preventivas: descoberta de dados e a classificação  | Sim | Ver [descoberta de dados do banco de dados SQL e SQL Data Warehouse e classificação](sql-database-data-discovery-and-classification.md). |
| Detecção: detecção de ameaças | Sim | Ver [proteção avançada contra ameaças do banco de dados SQL do Azure](sql-database-threat-detection-overview.md). |
