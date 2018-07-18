---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664409"
---
### <a name="sql-databases"></a>BANCOS DE DADOS SQL

|  |  |
|---------|---------|
| [SKUs do BD SQL permitidas](../articles/azure-policy/scripts/allowed-sql-db-skus.md) | Exige que bancos de dados SQL usem uma SKU aprovada. Especifique uma matriz de IDs de SKU permitidas ou uma matriz de nomes de SKU permitidos. |
| [Auditar a configuração de detecção de ameaças no nível do BD](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar criptografia do Banco de Dados SQL](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Audita se o banco de dados SQL não tem a Transparent Data Encryption habilitada. |
| [Auditar a Configuração de Auditoria no Nível do BD SQL](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Audita as configurações de auditoria de banco de dados SQL se essas configurações não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas.  |
| [Auditar o status da Transparent Data Encryption](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) | Auditará a Transparent Data Encryption do banco de dados SQL se ela não estiver habilitada.  |