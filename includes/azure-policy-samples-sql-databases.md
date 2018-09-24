---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003572"
---
### <a name="sql-databases"></a>BANCOS DE DADOS SQL

|  |  |
|---------|---------|
| [SKUs do BD SQL permitidas](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Exige que bancos de dados SQL usem uma SKU aprovada. Especifique uma matriz de IDs de SKU permitidas ou uma matriz de nomes de SKU permitidos. |
| [Auditar a configuração de detecção de ameaças no nível do BD](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar criptografia do Banco de Dados SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Audita se o banco de dados SQL não tem a Transparent Data Encryption habilitada. |
| [Auditar a Configuração de Auditoria no Nível do BD SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Audita as configurações de auditoria de banco de dados SQL se essas configurações não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas.  |
| [Auditar o status da Transparent Data Encryption](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | Auditará a Transparent Data Encryption do banco de dados SQL se ela não estiver habilitada.  |