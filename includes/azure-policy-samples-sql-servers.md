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
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664398"
---
### <a name="sql-servers"></a>SQL Servers

|  |  |
|---------|---------|
| [Auditar nenhum administrador do Azure Active Directory](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Auditar quando não há nenhum administrador do Azure Active Directory atribuído ao SQL server. |
| [Auditar a configuração de detecção de ameaças no nível do servidor](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar as configurações de auditoria do SQL Server](../articles/azure-policy/scripts/sql-server-audit.md) | Audita o SQL Server para verificar se as configurações de auditoria estão habilitadas. |
| [Auditar a Configuração da Auditoria no Nível do SQL Server](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Auditará as configurações de auditoria do SQL Server se elas não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas. |
| [Exigir SQL Server versão 12.0](../articles/azure-policy/scripts/req-sql-12.md) | Exige que servidores SQL usem a versão 12.0.  |