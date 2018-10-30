---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226479"
---
### <a name="sql-databases"></a>BANCOS DE DADOS SQL

|  |  |
|---------|---------|
| [SKUs do BD SQL permitidas](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Exige que bancos de dados SQL usem uma SKU aprovada. Especifique uma matriz de IDs de SKU permitidas ou uma matriz de nomes de SKU permitidos. |
| [Auditar a configuração de detecção de ameaças no nível do BD](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar criptografia do Banco de Dados SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Audita se o banco de dados SQL não tem a Transparent Data Encryption habilitada. |
| [Auditar a Configuração de Auditoria no Nível do BD SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Audita as configurações de auditoria de banco de dados SQL se essas configurações não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas.  |