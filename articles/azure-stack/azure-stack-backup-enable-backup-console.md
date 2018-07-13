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
ms.date: 07/11/2018
ms.author: jeffgilb
ms.openlocfilehash: fba04490aca4c7123ca478ae07a5f0c865d9a826
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968690"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Habilitar o backup do portal de administração para o Azure Stack
Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que o Azure Stack pode gerar backups. Você pode usar esses backups para restaurar seu ambiente usando a recuperação no caso de nuvem [uma falha catastrófica](.\azure-stack-backup-recover-data.md). O objetivo de recuperação de nuvem é garantir que seus operadores e usuários podem fazer logon novamente no portal após a conclusão da recuperação. Os usuários terão suas assinaturas restauradas incluindo permissões de acesso baseado em função e funções, originais planos, ofertas e computação definida anteriormente, armazenamento e cotas de rede.

No entanto, o serviço de Backup de infraestrutura não fazer backup de VMs de IaaS, configurações de rede e recursos de armazenamento, como contas de armazenamento, blobs, tabelas, e assim por diante, para que os usuários que fazem logon após a recuperação de nuvem for concluído não verão qualquer um dos seus previamente existente recursos. Plataforma como serviço (PaaS) recursos e dados também não passam por backup pelo serviço. 

Os administradores e usuários são responsáveis por fazer backup e restaurar os recursos de IaaS e PaaS separadamente dos processos de backup de infraestrutura. Para obter informações sobre como fazer backup de recursos de IaaS e PaaS, consulte os links a seguir:

- [Máquinas virtuais](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Antes de habilitar o backup por meio do console, você precisará configurar o serviço de backup. Você pode configurar o serviço de backup usando o PowerShell. Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Habilitar o backup

1. Abra o portal de administração do Azure Stack em [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selecione **mais serviços** > **backup da infra-estrutura**. Escolher **Configuration** na **backup de infraestrutura** folha.

    ![O Azure Stack - configurações de controlador de Backup](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Digite o caminho para o **local de armazenamento de Backup**. Use uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivos hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local dos recursos, como arquivos compartilhados ou dispositivos. Para o serviço, você pode usar um endereço IP. Para garantir a disponibilidade dos dados de backup após um desastre, o dispositivo deve estar em um local separado.
    > [!Note]  
    > Se seu ambiente oferece suporte a resolução de nome de rede de infraestrutura do Azure Stack para seu ambiente corporativo, você pode usar um FQDN em vez do IP.
4. Tipo de **nome de usuário** usando o domínio e o nome de usuário com acesso suficiente para ler e gravar arquivos. Por exemplo, `Contoso\backupshareuser`.
5. Tipo de **senha** para o usuário.
5. Digite a senha novamente até **Confirmar senha**.
6. Fornecer uma chave pré-compartilhada na **chave de criptografia** caixa. Arquivos de backup são criptografados usando essa chave. Certifique-se de armazenar essa chave em um local seguro. Depois que você defina essa chave pela primeira vez ou gire a chave no futuro, você não pode exibir essa chave dessa interface. Para obter mais instruções gerar uma chave pré-compartilhada, execute os scripts em [habilitar o Backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).
7. Selecione **Okey** para salvar suas configurações de backup do controlador.

Para executar um backup, você precisará baixar as ferramentas do Azure Stack e, em seguida, execute o cmdlet do PowerShell **AzSBackup início** no nó de administração do Azure Stack. Para obter mais informações, consulte [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Próximas etapas

- Aprenda a executar um backup. Ver [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Saiba como verificar se o backup foi executado. Ver [concluído no portal de administração de backup de confirmar](azure-stack-backup-back-up-azure-stack.md).
