---
title: Atributos de segurança comuns para o Backup do Azure
description: Uma lista de verificação de atributos de segurança comuns para avaliar o Backup do Azure
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679665"
---
# <a name="common-security-attributes-for-azure-backup"></a>Atributos de segurança comuns para o Backup do Azure

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comum embutidos no Backup do Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | Usar a criptografia do serviço de armazenamento para contas de armazenamento. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Não  | Usar HTTPS. |
| Manipulação de chaves de criptografia (CMK, BYOK, etc.)| Não  |  |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| Não  |  |
| Chamadas criptografadas à API| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Status do ponto de extremidade de serviço| Não  |  |
| Suporte à injeção de VNET| Não  |  |
| Isolamento de rede/Suporte a firewall| Sim | Há suporte para túnel forçado no backup de VM. Não há suporte para túnel forçado em cargas de trabalho em execução dentro de VMs. |
| Suporte para túnel forçado | Não  |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Há suporte para o Log Analytics por meio dos logs de diagnóstico. Ver [monitorar o Backup do Azure protegidos cargas de trabalho usando o Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

## <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Gerenciamento de acesso - Autorização| Sim | São usadas regras de RBAC internas e criadas pelo cliente. Ver [controle de acesso baseado em função para gerenciar pontos de recuperação de Backup do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Todas as ações disparadas pelo cliente no portal do Azure são registradas em logs de atividades. |
| Registro em log e auditoria de plano de dados| Não  | O plano de dados do Backup do Azure não pode ser acessado diretamente.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |