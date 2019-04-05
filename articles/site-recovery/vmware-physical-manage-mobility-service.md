---
title: Gerenciar o agente de mobilidade em servidores para recuperação de desastre de VMs VMware e servidores físicos com o Azure Site Recovery | Microsoft Docs
description: Gerencie o agente do serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59043801"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Gerenciar o agente de mobilidade em computadores protegidos

Você configurar o agente de mobilidade no servidor quando você usa o Azure Site Recovery para recuperação de desastre de VMs VMware e servidores físicos no Azure. O agente de mobilidade coordena a comunicação entre seu computador protegido, o servidor de configuração de servidor/processo de expansão e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o agente de mobilidade após sua implantação.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Atualizar serviço de mobilidade do portal do Azure

1. Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.
2. No portal, abra o cofre > **Itens replicados**.
3. Se o servidor de configuração estiver na versão mais recente, você verá uma notificação que mostra "Nova atualização do agente de replicação de recuperação do site está disponível. Clique para instalar."

     ![Janela Itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e, em **Atualização do agente**, selecione os computadores nos quais você deseja atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMs de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho Atualizar o Serviço de Mobilidade é iniciado para cada um dos computadores selecionados.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualizar serviço de mobilidade por meio de script do powershell no servidor do Windows

Use o seguinte script para atualizar o serviço de mobilidade em um servidor por meio do cmdlet do shell de energia

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Atualizar a conta usada para instalação por push do serviço de mobilidade

Quando você implantou o Site Recovery para habilitar a instalação por push do serviço de mobilidade, especificou uma conta que o servidor de processo do Site Recovery usa para acessar os computadores e instalar o serviço quando a replicação é habilitada para o computador. Se você quiser atualizar as credenciais dessa conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstalar o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

Desinstale pela interface do usuário ou por um prompt de comando.

- **Na interface de usuário**: No Painel de Controle do computador, selecione **Programas**. Selecione **Servidor de destino mestre/Serviço de Mobilidade do Microsoft Azure Site Recovery** > **Desinstalar**.
- **Em um prompt de comando**: Abra uma janela de prompt de comando como administrador no computador. Execute o comando a seguir: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Em um computador Linux
1. No computador Linux, entre como um usuário **raiz**.
2. Em um terminal, acesse /user/local/ASR.
3. Execute o comando a seguir:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
