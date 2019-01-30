---
title: Habilitar o backup do portal de administração para o Azure Stack | Microsoft Docs
description: Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que o Azure Stack pode ser restaurado se houver uma falha.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: db2c55ec30e766496b98ef66b584df26f2dfe116
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239274"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Habilitar o backup do portal de administração para o Azure Stack
Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que o Azure Stack pode gerar backups. Você pode usar esses backups para restaurar seu ambiente usando a recuperação no caso de nuvem [uma falha catastrófica](./azure-stack-backup-recover-data.md). O objetivo de recuperação de nuvem é garantir que seus operadores e usuários podem fazer logon novamente no portal após a conclusão da recuperação. Os usuários terão suas assinaturas restauradas incluindo permissões de acesso baseado em função e funções, originais planos, ofertas e computação definida anteriormente, armazenamento e cotas de rede.

No entanto, o serviço de Backup de infraestrutura não fazer backup de VMs de IaaS, configurações de rede e recursos de armazenamento, como contas de armazenamento, blobs, tabelas, e assim por diante, para que os usuários que fazem logon após a recuperação de nuvem for concluído não verão qualquer um dos seus previamente existente recursos. Plataforma como serviço (PaaS) recursos e dados também não passam por backup pelo serviço. 

Os administradores e usuários são responsáveis por fazer backup e restaurar os recursos de IaaS e PaaS separadamente dos processos de backup de infraestrutura. Para obter informações sobre como fazer backup de recursos de IaaS e PaaS, consulte os links a seguir:

- [Máquinas virtuais](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Habilitar ou reconfigurar o backup

1. Abra o [portal de administração do Azure Stack](azure-stack-manage-portals.md).
2. Selecione **todos os serviços**e, em seguida, sob o **administração** categoria, selecione **backup da infra-estrutura**. Escolher **Configuration** na **backup de infraestrutura** folha.
3. Digite o caminho para o **local de armazenamento de Backup**. Use uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivos hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local dos recursos, como arquivos compartilhados ou dispositivos. Para o serviço, você pode usar um endereço IP. Para garantir a disponibilidade dos dados de backup após um desastre, o dispositivo deve estar em um local separado.

    > [!Note]  
    > Se seu ambiente oferece suporte a resolução de nome de rede de infraestrutura do Azure Stack para seu ambiente corporativo, você pode usar um FQDN em vez do IP.
    
4. Tipo de **nome de usuário** usando o domínio e o nome de usuário com acesso suficiente para ler e gravar arquivos. Por exemplo, `Contoso\backupshareuser`.
5. Tipo de **senha** para o usuário.
6. Digite a senha novamente até **Confirmar senha**.
7. O **frequência nas horas** determina a frequência com que os backups são criados. O valor padrão é 12. O Agendador dá suporte a um máximo de 12 e um mínimo de 4. 
8. O **período de retenção em dias** determina quantos dias de backups são preservados em local externo. O valor padrão é 7. O Agendador dá suporte a um máximo de 14 e um mínimo de 2. Backups mais antigos que o período de retenção são automaticamente excluídos do local externo.

    > [!Note]  
    > Se você quiser arquivar backups mais antigos que o período de retenção, certifique-se os arquivos de backup antes que o Agendador exclui os backups. Se você reduzir o período de retenção de backup (por exemplo, de 7 dias para 5 dias), o Agendador excluirá todos os backups mais antigos que o novo período de retenção. Verifique se que você está okey com os backups excluídos antes de atualizar esse valor. 

9. Fornecer uma chave pré-compartilhada na **chave de criptografia** caixa. Arquivos de backup são criptografados usando essa chave. Certifique-se de armazenar essa chave em um local seguro. Depois que você defina essa chave pela primeira vez ou gire a chave no futuro, você não pode exibir a chave dessa interface. Para criar a chave, execute os seguintes comandos do PowerShell do Azure Stack:
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Selecione **Okey** para salvar suas configurações de backup do controlador.

    ![O Azure Stack - configurações de controlador de Backup](media/azure-stack-backup/backup-controller-settings.png)

## <a name="start-backup"></a>Iniciar o backup
Para iniciar um backup, clique em **fazer Backup agora** para iniciar um backup sob demanda. Um backup sob demanda não modificará o tempo para o próximo backup agendado. Depois que a tarefa for concluída, você pode confirmar as configurações no **Essentials**:

![O Azure Stack - backup sob demanda](media/azure-stack-backup/scheduled-backup.png)

Você também pode executar o cmdlet do PowerShell **AzsBackup início** em seu computador de administração do Azure Stack. Para obter mais informações, consulte [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Habilitar ou desabilitar backups automáticos
Os backups são agendados automaticamente quando você habilita o backup. Você pode verificar o próximo horário de backup de agendamento **Essentials**. 

![O Azure Stack - backup sob demanda](media/azure-stack-backup/on-demand-backup.png)

Se você precisar desabilitar futuros backups agendados, clique em **desabilitar Backups automáticos**. Desabilitantes backups automáticos manterão as configurações de backup configuradas e manterão o agendamento de backup. Essa ação simplesmente informa ao Agendador para ignorar os backups futuros. 

![O Azure Stack - desabilitar agendado backups](media/azure-stack-backup/disable-auto-backup.png)

Confirme que os backups futuros agendados foram desabilitados no **Essentials**:

![O Azure Stack - confirmar backups foram desabilitados](media/azure-stack-backup/confirm-disable.png)

Clique em **habilitar Backups automáticos** para informar o Agendador para iniciar backups futuros no horário agendado. 

![O Azure Stack - ativar programada backups](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Se você tiver configurado o backup de infraestrutura antes de atualizar para 1807, backups automáticos serão desabilitados. Dessa forma os backups iniciados pelo Azure Stack não entrem em conflito com iniciado por uma tarefa externa do mecanismo de agendamento de backups. Depois de desabilitar qualquer Agendador de tarefas externos, clique em **habilitar Backups automáticos**.


## <a name="next-steps"></a>Próximas etapas

- Aprenda a executar um backup. Ver [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Saiba como verificar se o backup foi executado. Ver [concluído no portal de administração de backup de confirmar](azure-stack-backup-back-up-azure-stack.md).
