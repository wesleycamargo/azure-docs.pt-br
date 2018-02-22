---
title: Instalar o Agente de VM do Azure no modo offline | Microsoft Docs
description: Saiba como instalar o Agente de VM do Azure no modo offline.
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 3770cc3338c89a9bf88e2cf9ec3faa37e2ff109b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalar o Agente de Máquina Virtual do Azure no modo offline 

O Agente de Máquina Virtual do Azure (Agente de VM) fornece recursos úteis, tais como a redefinição de senha de administrador local e o envio de scripts por push. Este artigo mostra como instalar o Agente de VM para uma VM (máquina virtual) offline do Windows. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando usar o Agente de VM no modo offline

Instale o Agente de VM no modo offline nos seguintes cenários:

- A VM do Azure implantada não tem o Agente de VM instalado ou o agente não está funcionando.
- Você esqueceu a senha de administrador da VM ou você não pode acessar a VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Instalar o Agente de VM no modo offline

Use as etapas a seguir para instalar o Agente de VM no modo offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Etapa 1: anexar o disco do sistema operacional da VM a uma outra VM como um disco de dados

1.  Exclua a VM. Ao excluir a VM, verifique se você selecionou a opção **Manter os discos**.

2.  Anexe o disco do sistema operacional como um disco de dados a outra VM (conhecida como uma VM de _solução de problemas_). Para obter mais informações, veja [Anexar um disco de dados a uma VM do Windows no Portal do Azure](attach-managed-disk-portal.md).

3.  Conecte-se à VM de solução de problemas. Abra **Gerenciamento do computador** > **Gerenciamento de disco**. Confirme que o disco do sistema operacional está online e que as letras de unidade estão atribuídas às partições de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Etapa 2: modificar o disco do sistema operacional para instalar o Agente de VM do Azure

1.  Inicie uma conexão de área de trabalho remota para a VM de solução de problemas.

2.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config. Copie todos os arquivos nesta pasta como um backup, para o caso de necessidade de uma reversão.

3.  Inicie o **Editor do Registro** (regedit.exe).

4.  Selecione a chave **HKEY_LOCAL_MACHINE**. No menu, selecione **Arquivo** > **Carregar Hive**:

    ![Carregar o hive](./media/install-vm-agent-offline/load-hive.png)

5.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config\SYSTEM. Como o nome da seção, digite **BROKENSYSTEM**. O novo hive do Registro é exibido sob a chave **HKEY_LOCAL_MACHINE**.

6.  No disco do sistema operacional que você anexou, navegue até a pasta \windows\system32\config\SOFTWARE. Para o nome do software de hive, digite **BROKENSOFTWARE**.

7.  Se o Agente de VM não está funcionando, faça backup da configuração atual.

    >[!NOTE]
    >Se a VM não tem o agente instalado, vá para a etapa 8. 
      
    1. Renomeie a pasta de \windowsazure para \windowsazure.old.

    2. Exporte os registros a seguir:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Use os arquivos existentes na solução de problemas de VM como um repositório para a instalação do Agente de VM. Conclua as seguintes etapas:

    1. Da VM de solução de problemas, exporte as seguintes subchaves no formato de Registro (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Exportar as subchaves do Registro](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite os arquivos de Registro. Em cada arquivo, altere o valor de entrada **SYSTEM** para **BROKENSYSTEM** (conforme mostrado nas imagens a seguir) e salve o arquivo.

        ![Alterar os valores de subchaves do Registro](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os arquivos de Registro para o repositório clicando duas vezes em cada arquivo de Registro.

    4. Confirme se as três subchaves a seguir são importadas com êxito para o hive **BROKENSYSTEM**:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Copie a pasta do Agente de VM de C:\windowsazure\packages para o &lt;disco do sistema operacional que você anexou&gt;: \windowsazure\packages.

    ![Copie os arquivos do Agente de VM para o disco do sistema operacional](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Não copie a pasta **logs**. Depois que o serviço é iniciado, novos logs são gerados.

10.  Selecione **BROKENSYSTEM**. No menu, selecione **Arquivo** > **Descarregar Hive**.

11.  Selecione **BROKENSOFTWARE**. No menu, selecione **Arquivo** > **Descarregar Hive**.

12.  Desanexe o disco do sistema operacional e, em seguida, recrie a VM usando o disco do sistema operacional.

13.  Acesse a VM. Observe que o RdAgent está em execução e os logs estão sendo gerados.

Se você cria a VM usando o modelo de implantação clássico, está pronto.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Use a propriedade ProvisionGuestAgent para VMs criadas com o Azure Resource Manager

Se você criar a VM usando o modelo de implantação do Resource Manager, use o módulo Azure PowerShell para atualizar a propriedade **ProvisionGuestAgent**. A propriedade informa o Azure que a VM tem o Agente de VM instalado.

Para definir a propriedade **ProvisionGuestAgent**, execute os seguintes comandos do Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Em seguida, execute o `Get-AzureVM` comando. Observe que a propriedade **GuestAgentStatus** agora é populada por dados:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Agente de Máquina Virtual do Azure](agent-user-guide.md)
- [Recursos e extensões da máquina virtual para Windows](extensions-features.md)
