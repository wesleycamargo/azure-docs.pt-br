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
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003568"
---
### <a name="sql-servers"></a>SQL Servers

|  |  |
|---------|---------|
| [Auditar nenhum administrador do Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Auditar quando não há nenhum administrador do Azure Active Directory atribuído ao SQL server. |
| [Auditar a configuração de detecção de ameaças no nível do servidor](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar as configurações de auditoria do SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Audita o SQL Server para verificar se as configurações de auditoria estão habilitadas. |
| [Auditar a Configuração da Auditoria no Nível do SQL Server](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) | Auditará as configurações de auditoria do SQL Server se elas não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas. |
| [Exigir SQL Server versão 12.0](../articles/governance/policy/samples/req-sql-12.md) | Exige que servidores SQL usem a versão 12.0.  |