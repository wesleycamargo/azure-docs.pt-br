---
title: Fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o Backup do Azure
description: Descreve como fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o Backup do Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 3342b15511305ab337d9b5032080e205e36150d3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049806"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação

Este artigo descreve como fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o [Backup do Azure](backup-overview.md) service. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Verifique se o suporte e pré-requisitos para um backup.
> * Prepare VMs do Azure. Instale o agente da VM do Azure, se necessário, e verifique se o acesso de saída para VMs.
> * Crie um cofre.
> * Descobrir VMs e configurar uma política de backup.
> * Habilite o backup para VMs do Azure.


> [!NOTE]
> Este artigo descreve como configurar um cofre e selecione VMs para fazer backup. Isso é útil se você quiser fazer backup de várias VMs. Como alternativa, você pode [fazer backup de uma única VM do Azure](backup-azure-vms-first-look-arm.md) diretamente das configurações de VM.

## <a name="before-you-start"></a>Antes de começar


- [Revisão](backup-architecture.md#architecture-direct-backup-of-azure-vms) a arquitetura de backup de VM do Azure.
- [Saiba mais sobre](backup-azure-vms-introduction.md) backup de VM do Azure e a extensão de backup.
- [Examine a matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure.


## <a name="prepare-azure-vms"></a>Preparar VMs do Azure

Em algumas circunstâncias, talvez você precise configurar o agente de VM do Azure em VMs do Azure, ou explicitamente permitir acesso de saída em uma VM.

### <a name="install-the-vm-agent"></a>Instalar o agente de VM 

O Backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do Marketplace do Azure, o agente é instalado e em execução. Se você cria uma VM personalizada ou se você migrar uma máquina local, você talvez precise instalar o agente manualmente, conforme resumido na tabela.

**VM** | **Detalhes**
--- | ---
** Windows** | 1. [Baixe e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o arquivo MSI do agente.<br/><br/> 2. Instale com permissões de administrador no computador.<br/><br/> 3. Verifique se a instalação. Na *C:\WindowsAzure\Packages* na VM, clique com botão direito **WaAppAgent.exe** > **propriedades**. Sobre o **detalhes** guia **versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se você estiver atualizando o agente, certifique-se de que nenhuma operação de backup está em execução, e [reinstalar o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale usando um RPM ou pacote DEB do repositório de pacotes da distribuição. Esse é o método preferencial para instalar e atualizar o agente Linux do Azure. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não recomendamos instalá-lo a partir daí.<br/><br/> Se você estiver atualizando o agente, verifique se nenhuma operação de backup está em execução e os binários de atualização.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

A extensão de backup em execução na máquina virtual precisa de acesso de saída para os endereços IP públicos do Azure.

Em geral, você não precisa explicitamente permitir o acesso de rede de saída para uma VM do Azure para que ele possa se comunicar com o Backup do Azure.
Se suas VMs não podem se conectar e se você vir o erro **ExtensionSnapshotFailedNoNetwork**, você deve permitir o acesso explicitamente. A extensão de backup, em seguida, pode se comunicar com os endereços IP públicos do Azure para o tráfego de backup.


#### <a name="explicitly-allow-outbound-access"></a>Permitir explicitamente o acesso de saída

Se uma VM não pode se conectar ao serviço de Backup, permitir acesso de saída explicitamente usando um dos métodos resumidos na tabela.

**Opção** | **Ação** | **Detalhes** 
--- | --- | --- 
**Configurar regras NSG** | Permitir os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Em vez de permitir e gerenciar todos os intervalos de endereço, você pode adicionar uma regra NSG (grupo) de segurança de rede que permite o acesso ao serviço de Backup do Azure usando um [marca de serviço](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). [Saiba mais](../virtual-network/security-overview.md#service-tags).<br/><br/> Não há nenhum custo adicional.<br/><br/> As regras são simples de gerenciar com marcas de serviço.
**Implantar um proxy** | Implante um servidor de proxy HTTP para rotear o tráfego. | Esse método fornece acesso ao Azure e não apenas o armazenamento por completo.<br/><br/> É permitido o controle granular em relação às URLs de armazenamento.<br/><br/> Há um único ponto de acesso à internet para VMs.<br/><br/> Há custos adicionais para um proxy.
**Configurar o Firewall do Azure** | Permitir o tráfego através do Firewall do Azure na VM, usando uma marca FQDN para o serviço de Backup do Azure. |  Esse método é simple de usar se você tiver o Firewall do Azure configurado na sub-rede de uma rede virtual.<br/><br/> É possível criar suas próprias marcações de FQDN, ou modificar os FQDNs em uma marca.<br/><br/> Se você usa o Managed Disks do Azure, pode ser necessário abrir outra porta (porta 8443) nos firewalls.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurar uma regra NSG para permitir o acesso de saída ao Azure

Se um NSG gerencia o acesso VM, permitir o acesso de saída para o armazenamento de backup para as portas e intervalos necessários.

1. Nas propriedades da VM > **Networking**, selecione **Adicionar regra de porta de saída**.
2. Na **Adicionar regra de segurança de saída**, selecione **avançado**.
3. Em **Fonte**, escolha **VirtualNetwork**.
4. Na **intervalos de porta de origem**, digite um asterisco (*) para permitir o acesso de saída de qualquer porta.
5. Em **Destino**, escolha **Marca de Serviço**. Na lista, selecione **Storage.region**. A região é onde o cofre e as VMs que você deseja fazer backup, estão localizadas.
6. Em **Intervalos de portas de destino**, escolha a porta.
    - VM não gerenciada com conta de armazenamento não criptografada: 80
    - VM não gerenciada com conta de armazenamento criptografada: 443 (configuração padrão)
    - VM gerenciada: 8443.
7. Em **Protocolo**, escolha **TCP**.
8. Em **Prioridade**, especifique um valor de prioridade menor que qualquer regra de negação maior.
   
   Se você tiver uma regra que nega o acesso, o novo permitir a regra deve ser maior. Por exemplo, se você tiver uma regra **Deny_All** definida com prioridade 1000, a nova regra deverá ser definida com um valor inferior a 1000.
9. Forneça um nome e descrição para a regra e, em seguida, selecione **Okey**.

Você pode aplicar a regra de NSG para várias VMs para permitir acesso de saída. Este vídeo orienta você durante o processo.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>Rotear o tráfego de backup por meio de um proxy

Você pode rotear o tráfego de backup por um proxy e, em seguida, fornecer o acesso de proxy aos intervalos necessários do Azure. Configure o proxy de VM para permitir que o seguinte:

- A VM do Azure deve rotear todo o tráfego HTTP voltado para a Internet pública por meio do proxy.
- O proxy deve permitir o tráfego de entrada de VMs na rede virtual aplicável.
- O NSG **NSF-lockdown** precisa de uma regra que permita a saída do tráfego de Internet da VM do proxy.

###### <a name="set-up-the-proxy"></a>Configurar o proxy

Se você não tiver um proxy de conta do sistema, configure um da seguinte maneira:

1. Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Execute **PsExec.exe -i -s cmd.exe** para executar o prompt de comando em uma conta do sistema.
3. Execute o navegador no contexto do sistema. Por exemplo, use **%PROGRAMFILES%\Internet Explorer\iexplore.exe** para o Internet Explorer.  
4. Defina as configurações de proxy.
   - Em máquinas do Linux:
     - Adicione esta linha ao arquivo **/etc/environment**:
       - **http_proxy = http:\/porta de proxy: endereço IP /proxy**
     - Adicione estas linhas ao arquivo **/etc/waagent.conf**:
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Em computadores Windows, nas configurações do navegador, especifique que um proxy deverá ser usado. Se estiver usando um proxy em uma conta de usuário, você poderá usar esse script para aplicar a configuração no nível de conta do sistema.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Permitir conexões de entrada no proxy

Permite conexões de entrada nas configurações do proxy.

1. No Firewall do Windows, abra **Firewall do Windows com segurança avançada**.
2. Clique com o botão direito do mouse em **Regras de Entrada** > **Nova Regra**.
3. Na **tipo de regra**, selecione **personalizado** > **próximo**.
4. Em **Programa**, escolha **Todos os Programas** > **Avançar**.
5. Na **protocolos e portas**:
   - Defina o tipo como **TCP**.
   - Definir **portas locais** à **portas específicas**.
   - Definir **porta remota** à **todas as portas**.
  
6. Conclua o assistente e especifique um nome para a regra.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Adicionar uma regra de exceção ao NSG para o proxy

No NSG **NSF-lockdown**, permita o tráfego de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS).

O script do PowerShell a seguir fornece um exemplo de como habilitar o tráfego.
Em vez de permitir saída para todos os endereços de internet pública, você pode especificar um intervalo de endereços IP (`-DestinationPortRange`), ou usar a marca de serviço storage.region.   

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

### <a name="allow-firewall-access-by-using-an-fqdn-tag"></a>Permitir o acesso ao firewall usando uma marca FQDN

Você pode configurar o Firewall do Azure para permitir o acesso de saída para o tráfego de rede para o Backup do Azure.

- [Saiba como](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) implantar o Firewall do Azure.
- [Leia sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) as marcas de FQDN.

## <a name="modify-storage-replication-settings"></a>Modificar as configurações de replicação de armazenamento

Por padrão, seu cofre tem [armazenamento com redundância geográfica (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). É recomendável usar GRS para o backup primário. Você pode usar [armazenamento localmente redundante (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modificar o tipo de replicação de armazenamento da seguinte maneira:

1. No portal, selecione o novo cofre. Sob **as configurações**, selecione **propriedades**.
2. Na **propriedades**, em **configuração de Backup**, selecione **atualização**.
3. Selecione o tipo de replicação de armazenamento e selecione **salvar**.

![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Descubra as VMs na assinatura e configure o backup.

1. No cofre > **visão geral**, selecione **+ Backup**.

   ![Botão Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Os painéis **Backup** e **Meta de Backup** abrem.

2. Em **Meta de backup** > **Onde sua carga de trabalho é executada?**, escolha **Azure**. Em **Do que você deseja fazer backup?**, escolha **Máquina virtual** >  **OK**.

   ![Painéis Backup e Meta de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Esta etapa registra a extensão da VM com o cofre. O painel **Meta de Backup** fecha e o painel **Política de backup** abre.

3. Na **Política de Backup**, escolha a política que você deseja associar ao cofre. Depois, selecione **OK**.
    - Os detalhes da política padrão estão listados no menu suspenso.
    - Selecione **criar novo** para criar uma política. [Saiba mais](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) sobre como definir uma política.

    ![Painéis "Backup" e "Política de backup"](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. No **selecionar máquinas virtuais** painel, selecione as VMs que usarão a política de backup especificada > **Okey**.

   A máquina virtual selecionada é validada. Você só pode escolher máquinas virtuais na mesma região que o cofre. O backup das VMs só pode ser feito em um único cofre.

   ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Em **Backup**, escolha **Habilitar backup**.

   Esta etapa implanta a política para o cofre e às VMs. Ele também instala a extensão de backup no agente de VM em execução na VM do Azure.
   
   Essa etapa não cria o ponto de recuperação inicial para a VM.

   ![Botão "Habilitar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de habilitar o backup:

- Um backup inicial será executado de acordo com sua agenda de backup.
- O serviço de Backup instala a extensão de backup independentemente de a VM estar em execução.

Uma VM em execução oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o backup da VM é feito mesmo se ela esteja desativada e a extensão não poderá ser instalada. Isso é conhecido como uma VM offline. Nesse caso, o ponto de recuperação será consistente com falha.
    
> [!NOTE]
> O Backup do Azure não oferece suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. Modifique as políticas de backup manualmente, conforme necessário.

## <a name="run-the-initial-backup"></a>Executar o backup inicial

O backup inicial será executado de acordo com a agenda, a menos que você o execute manualmente de modo imediato. Execute-o manualmente da seguinte maneira:

1. No menu do cofre, selecione **itens de Backup**.
2. Na **itens de Backup**, selecione **Máquina Virtual do Azure**.
3. No **itens de Backup** , selecione as reticências (**...** ).
4. Selecione **fazer Backup agora**.
5. Na **fazer Backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido > **Okey**.
6. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode levar algum tempo.



## <a name="next-steps"></a>Próximas etapas

- Solucione problemas com o [agentes de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup de VM do Azure](backup-azure-vms-troubleshoot.md).
- [Restaurar](backup-azure-arm-restore-vms.md) as VMs do Azure.

