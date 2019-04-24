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
ms.openlocfilehash: cf012a452d1e43134d955e03ccf34e153b1282ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253815"
---
# <a name="common-security-attributes-for-azure-backup"></a>Atributos de segurança comuns para o Backup do Azure

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comum embutidos no Backup do Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | Usar a criptografia do serviço de armazenamento para contas de armazenamento. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Não  | Usar HTTPS. |
| Tratamento de chaves de criptografia (CMK, BYOK, etc.)| Não  |  |
| Criptografia de nível de coluna (serviços de dados do Azure)| Não  |  |
| Chamadas criptografadas à API| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte de ponto de extremidade de serviço| Não  |  |
| suporte à injeção de rede virtual| Não  |  |
| Isolamento de rede e suporte de firewall| Sim | Há suporte para túnel forçado no backup de VM. Não há suporte para túnel forçado em cargas de trabalho em execução dentro de VMs. |
| Forçado suporte por túnel| Não  |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Há suporte para o Log Analytics por meio dos logs de diagnóstico. Ver [monitorar o Backup do Azure protegidos cargas de trabalho usando o Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Authentication| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | São usadas regras de RBAC internas e criadas pelo cliente. Ver [controle de acesso baseado em função para gerenciar pontos de recuperação de Backup do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Auditoria e log de plano de controle e gerenciamento| Sim | Todas as ações disparadas pelo cliente no portal do Azure são registradas em logs de atividades. |
| Auditoria e log de plano de dados| Não  | O plano de dados do Backup do Azure não pode ser acessado diretamente.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |