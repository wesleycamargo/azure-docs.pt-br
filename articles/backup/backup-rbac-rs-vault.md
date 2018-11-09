---
title: Gerenciar backups com o Controle de Acesso baseado em função do Azure
description: Use o Controle de Acesso baseado em função para gerenciar o acesso a operações de gerenciamento de backups em um cofre dos Serviços de Recuperação.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: f293f642db2bd526e761ff570ce97a33845808b7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412798"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Use o Controle de Acesso baseado em função para gerenciar pontos de recuperação de Backup do Azure
O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

> [!IMPORTANT]
> A funções fornecidas pelo Backup do Azure são limitadas a ações que podem ser executadas no Portal do Azure ou em cmdlets do PowerShell do cofre de Serviços de Recuperação. As ações executadas na interface de usuário do Cliente do Agente de backup do Azure, na interface de usuário do System Center Data Protection Manager ou no Servidor de Backup do Azure estão fora do controle dessas funções.

O Backup do Azure oferece 3 funções internas para controlar as operações de gerenciamento de backups. Saiba mais sobre as [funções internas do RBAC do Azure](../role-based-access-control/built-in-roles.md)

* [Colaborador de Backup](../role-based-access-control/built-in-roles.md#backup-contributor): essa função tem todas as permissões para criar e gerenciar backups, exceto criar o cofre dos Serviços de Recuperação e fornecer acesso a outras pessoas. Imagine essa função como administrador do gerenciamento de backups, que pode executar todas as operações de gerenciamento de backups.
* [Operador de Backup](../role-based-access-control/built-in-roles.md#backup-operator): essa função tem permissões para fazer tudo que um colaborador faz, exceto remover backups e gerenciar políticas de backup. Essa função é equivalente à de colaborador, com exceção de que não é possível executar operações destrutivas, como interromper backups com exclusão de dados ou remover registro de recursos locais.
* [Leitor de Backup](../role-based-access-control/built-in-roles.md#backup-reader): essa função tem permissões para exibir todas as operações de gerenciamento de backups. Imagine essa função como uma pessoa de monitoramento.

Se você pretende definir suas próprias funções para ter ainda mais controle, confira como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no RBAC do Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapeando funções internas de backup para ações de gerenciamento de backups
A tabela a seguir captura as ações de gerenciamento de backups e a função RBAC mínima correspondente necessária para executar essa operação.

| Operação de gerenciamento | Função RBAC mínima necessária | Escopo exigido |
| --- | --- | --- |
| Criar cofre de Serviços de Recuperação | Colaborador | Grupo de recursos contendo o cofre |
| Habilitar backup de VMs do Azure | Operador de Backup | Grupo de recursos contendo o cofre |
| | Colaborador de Máquina Virtual | Recurso de VM |
| Backup sob demanda de VM | Operador de Backup | Recurso de cofre de recuperação |
| Restaurar VM | Operador de Backup | Grupo de recursos no qual a VM será implantada |
| | Colaborador de Máquina Virtual | Grupo de recursos no qual a VM será implantada |
| Restaurar backup de VM de discos não gerenciados | Operador de Backup | Recurso de cofre de recuperação |
| | Colaborador de Máquina Virtual | Recurso de VM |
| | Colaborador da Conta de Armazenamento | Recurso da conta de armazenamento |
| Restaurar discos gerenciados do backup da VM | Operador de Backup | Recurso de cofre de recuperação |
| | Colaborador de Máquina Virtual | Recurso de VM |
| | Colaborador da Conta de Armazenamento | Recurso da conta de armazenamento |
| | Colaborador | Grupo de recursos para o qual o disco gerenciado será restaurado |
| Restaurar arquivos individuais do backup da VM | Operador de Backup | Recurso de cofre de recuperação |
| | Colaborador de Máquina Virtual | Recurso de VM |
| Criar política de backup para backup da VM do Azure | Colaborador de Backup | Recurso de cofre de recuperação |
| Modificar a política de backup da VM do Azure | Colaborador de Backup | Recurso de cofre de recuperação |
| Excluir a política de backup da VM do Azure | Colaborador de Backup | Recurso de cofre de recuperação |
| Interromper o backup (com retenção de dados ou exclusão de dados) no backup da VM | Colaborador de Backup | Recurso de cofre de recuperação |
| Registrar-se no Windows Server/cliente/SCDPM local ou no Servidor de Backup do Azure | Operador de Backup | Recurso de cofre de recuperação |
| Excluir o Windows Server/cliente/SCDPM local registrado ou o Servidor de Backup do Azure | Colaborador de Backup | Recurso de cofre de recuperação |

## <a name="next-steps"></a>Próximas etapas
* [Controle de Acesso com Base em Funções](../role-based-access-control/role-assignments-portal.md): introdução ao RBAC no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [API REST](../role-based-access-control/role-assignments-rest.md)
* [Solução de problemas de Controle de Acesso Baseado em Função](../role-based-access-control/troubleshooting.md): obtenha sugestões para corrigir problemas comuns.
