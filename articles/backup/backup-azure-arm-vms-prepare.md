---
title: Fazer backup de VMs do Azure em um cofre dos Serviços de Recuperação no Backup do Azure
description: Descreve como fazer backup de VMs do Azure em um cofre dos Serviços de Recuperação no Backup do Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: e782afb971f95a654119d9817edeef02642bee9e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447558"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação

Este artigo descreve como fazer backup de VM do Azure usando um [Backup do Azure](backup-overview.md) implantando e habilitando o backup em um cofre dos Serviços de Recuperação. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Verifique pré-requisitos e cenários com suporte.
> * Prepare VMs do Azure. Instale o agente da VM do Azure, se necessário, e verifique se o acesso de saída para VMs.
> * Crie um cofre.
> * Configurar o armazenamento para o cofre
> * Descubra máquinas virtuais, configure a política e as configurações de backup.
> * Habilitar o backup para VMs do Azure


> [!NOTE]
   > Este artigo descreve como fazer backup de VMs do Azure ao configurar um cofre e escolher as VMs para fazer backup. Isso é útil se você quiser fazer backup de várias VMs. Você também pode fazer [backup de uma VM do Azure](backup-azure-vms-first-look-arm.md) diretamente nas configurações da VM.

## <a name="before-you-start"></a>Antes de começar

O Backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador.

1. [Examine](backup-architecture.md#architecture-direct-backup-of-azure-vms) a arquitetura de backup de VM do Azure.
[Saiba mais sobre](backup-azure-vms-introduction.md) backup de VM do Azure e a extensão de backup.
2. [Examine a matriz de suporte](backup-support-matrix-iaas.md) para backup de VM do Azure.
3. Prepare VMs do Azure. Instale o agente da VM se ele não estiver instalado e verifique o acesso de saída para VMs de que você deseja fazer backup.


## <a name="prepare-azure-vms"></a>Preparar VMs do Azure

Instale o agente da VM, se necessário, e verifique se o acesso de saída de VMs.

### <a name="install-the-vm-agent"></a>Instalar o agente de VM 
Se necessário, instale o agente da seguinte maneira.

**VM** | **Detalhes**
--- | ---
**VMs do Windows** | [Baixe e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o arquivo MSI do agente. Instale com permissões de administrador no computador.<br/><br/> Para verificar a instalação, em *C:\WindowsAzure\Packages* na VM, clique com o botão direito do mouse em WaAppAgent.exe > **Propriedades**, > guia **Detalhes**. **A versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se você estiver atualizando o agente, verifique se nenhuma operação de backup está em execução e [reinstale o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**VMs do Linux** | Instalação usando um RPM ou um pacote DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o Azure do Agente Linux do Azure. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não recomendamos instalá-lo a partir daí.<br/><br/> Se você estiver atualizando o agente, verifique se nenhuma operação de backup está em execução e atualize os binários. 


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

A extensão de backup em execução na VM deve ter acesso de saída aos endereços IP públicos do Azure.

- Não é necessário acesso de rede de saída explícito para a VM do Azure para se comunicar com o Serviço de Backup do Azure.
- No entanto, determinadas máquinas virtuais mais antigas podem enfrentar problemas e falhar com o erro **ExtensionSnapshotFailedNoNetwork** ao tentarem se conectar. Neste caso, use uma das opções a seguir para que a extensão de backup possa se comunicar com os endereços IP públicos do Azure para o tráfego de backup.

   **Opção** | **Ação** ** | **Vantagens** | **Desvantagens**
   --- | --- | --- | ---
   **Configurar regras de NSG** | Permitir os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/>  Você pode adicionar uma regra que permita acesso ao serviço de Backup do Azure usando [uma tag de serviço](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure) em vez de permitir e gerenciar individualmente cada intervalo de endereços. [Saiba mais](../virtual-network/security-overview.md#service-tags) sobre marcas de serviço. | Sem custo adicional. Simples de gerenciar com marcas de serviço
   **Implantar um proxy** | Implante um servidor de proxy HTTP para rotear o tráfego. | Fornece acesso ao Azure por completo, não somente ao armazenamento. É permitido o controle granular em relação às URLs de armazenamento.<br/><br/> Único ponto de acesso à Internet para VMs.<br/><br/> Custos adicionais para o proxy.<br/><br/> 
   **Configurar o Firewall do Azure** | permite o tráfego por meio do Firewall do Azure na VM, usando uma marca de FQDN para o serviço de Backup do Azure.|  Simples de usar se você tiver o Firewall do Azure configurado em uma sub-rede da VNet | Não é possível criar suas próprias marcas de FQDN ou modificar os FQDNs em uma marca.<br/><br/> Se você usa o Managed Disks do Azure, pode ser necessário abrir outra porta (porta 8443) nos firewalls.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurar uma regra NSG para permitir o acesso de saída ao Azure

Se sua VM do Azure tiver acesso gerenciado por um NSG, permita o acesso de saída do armazenamento de backup aos intervalos e portas necessários.

1. Na VM > **Rede**, clique em **Adicionar regra de porta de saída**.
2. Em **Adicionar regra de segurança de saída**, clique em **Avançado**.
3. Em **Fonte**, escolha **VirtualNetwork**.
4. Nos **Intervalos de porta de origem**, digite um asterisco (*) para permitir o acesso de saída de qualquer porta.
5. Em **Destino**, escolha **Marca de Serviço**. Na lista, selecione **Storage.region**. A região é a região em que o cofre e as VMs que você deseja fazer backup estão localizados.
6. Em **Intervalos de portas de destino**, escolha a porta.
    - VM não gerenciada com conta de armazenamento não criptografada: 80
    - VM não gerenciada com conta de armazenamento criptografada: 443 (configuração padrão)
    - VM gerenciada: 8443.
7. Em **Protocolo**, escolha **TCP**.
8. Em **Prioridade**, especifique um valor de prioridade menor que qualquer regra de negação maior. Se você tiver uma regra que nega o acesso, a nova regra de permissão deverá ser maior. Por exemplo, se você tiver uma regra **Deny_All** definida com prioridade 1000, a nova regra deverá ser definida com um valor inferior a 1000.
9. Forneça um nome e descrição para a regra e, em seguida, clique em **OK**.

Você pode aplicar a regra de NSG para várias VMs para permitir acesso de saída.

Este vídeo orienta você durante o processo.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>Rotear o tráfego de backup por meio de um proxy

Você pode rotear o tráfego de backup por um proxy e, em seguida, fornecer o acesso de proxy aos intervalos necessários do Azure.
Configure sua VM do proxy para permitir o seguinte:

- A VM do Azure deve rotear todo o tráfego HTTP voltado para a Internet pública por meio do proxy.
- O proxy deve permitir o tráfego de entrada a partir de VMs na rede virtual aplicável (VNet).
- O NSG **NSF-lockdown** precisa de uma regra que permita a saída do tráfego de Internet da VM do proxy.

##### <a name="set-up-the-proxy"></a>Configurar o proxy
Se você não tiver um proxy de conta do sistema, configure um da seguinte maneira:

1. Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553).

2. Execute **PsExec.exe -i -s cmd.exe** para executar o prompt de comando em uma conta do sistema.
3. Execute o navegador no contexto do sistema. Por exemplo: **PROGRAMFILES%\Internet Explorer\iexplore.exe** para o Internet Explorer.  
4. Defina as configurações de proxy.
    - Em máquinas do Linux:
        - Adicione esta linha ao arquivo **/etc/environment**:
            - endereço IP **proxy_http=http://proxy:porta do proxy**
        - Adicione estas linhas ao arquivo **/etc/waagent.conf**:
            - **Endereço IP HttpProxy.Host=proxy**
            - **Porta HttpProxy.Port=proxy**
    - Em computadores Windows, nas configurações do navegador, especifique que um proxy deverá ser usado. Se estiver usando um proxy em uma conta de usuário, você poderá usar esse script para aplicar a configuração no nível de conta do sistema.
        ```powershell
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

##### <a name="allow-incoming-connections-on-the-proxy"></a>Permitir conexões de entrada no proxy

Permite conexões de entrada nas configurações do proxy.

- Por exemplo, abra o **Firewall do Windows com Segurança Avançada**.
    - Clique com o botão direito do mouse em **Regras de Entrada** > **Nova Regra**.
    - Em **Tipo de Regra** escolha **Personalizado** > **Avançar**.
    - Em **Programa**, escolha **Todos os Programas** > **Avançar**.
    - Em **Protocolos e portas**, defina o tipo como **TCP**, **Portas locais** como **Portas específicas** e **Porta remota** como **Todas as portas**.
    - Conclua o assistente e especifique um nome para a regra.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Adicionar uma regra de exceção ao NSG para o proxy

No NSG **NSF-lockdown**, permita o tráfego de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS).

- O script do PowerShell a seguir fornece um exemplo de como habilitar o tráfego.
- Em vez de permitir a saída para todos os endereços públicos de Internet, você pode especificar um intervalo de endereços IP (-DestinationPortRange) ou usar a marca de serviço storage.region.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Permitir o acesso do firewall com marca de FQDN

Você pode configurar o Firewall do Azure para permitir o acesso de saída para o tráfego de rede para o Backup do Azure.

- [Saiba como](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) implantar o Firewall do Azure.
- [Leia sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) as marcas de FQDN.

## <a name="create-a-vault"></a>Criar um cofre

Um cofre armazena backups e pontos de recuperação criados ao longo do tempo e armazena as políticas de backup associadas às máquinas submetidas a backup. Crie um cofre da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu **Hub**, escolha **Procurar** e digite **Serviços de Recuperação**. Selecione **Cofres de Serviços de Recuperação**.

    ![Digitando na caixa e selecionando "Cofres de Serviços de Recuperação" nos resultados](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. No menu **Cofres de Serviços de Recuperação**, selecione **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    ![Painel "Cofres de Serviços de Recuperação"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Em **cofres dos Serviços de Recuperação** >  **Nome**, insira um nome amigável para identificar o cofre.
    - O nome deve ser exclusivo para a assinatura do Azure.
    - Ele pode conter caracteres de 2 a 50.
    - Ele deve começar com uma letra e pode conter apenas letras, números e hifens.
5. Selecione **Assinatura** para ver a lista de assinaturas disponíveis. Se você não tiver certeza sobre qual assinatura usar, utilize a assinatura padrão (ou a sugerida). Só haverá múltiplas opções se sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure.
6. Clique em **Grupo de recursos** para ver a lista disponível de grupos de recursos ou em **Novo** para criar um grupo de recursos. [Saiba mais](../azure-resource-manager/resource-group-overview.md) sobre grupos de recursos.
7. Clique em **Localização** para selecionar a região geográfica do cofre. O cofre *deve* estar na mesma região que as máquinas virtuais que você deseja fazer backup.
8. Selecione **Criar**.
    - Pode levar algum tempo para que o cofre seja criado.
    - Monitore as notificações de status na área superior direita do portal.
    ![Lista de cofres de backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Depois que o cofre é criado, ele aparece na lista de cofres de Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

## <a name="set-up-storage-replication"></a>Configurar a replicação de armazenamento

Por padrão, seu cofre tem [armazenamento com redundância geográfica (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). É recomendável usar o GRS no backup principal, mas você pode usar o [armazenamento localmente redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) como uma opção mais barata.

Modifique a replicação de armazenamento da seguinte maneira:

1. No cofre > **Infraestrutura de Backup**, clique em **configuração de Backup**

   ![Lista de cofres de backup](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. Em **Configuração de Backup**, modifique o método de redundância de armazenamento conforme necessário e escolha **Salvar**.


## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Descubra as VMs na assinatura e configure o backup.

1. No cofre > **Visão Geral**, clique em **+ Backup**

   ![Botão Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Os painéis **Backup** e **Meta de Backup** abrem.

2. Em **Meta de backup**> **Onde sua carga de trabalho é executada?**, escolha **Azure**. Em **Do que você deseja fazer backup?**, escolha **Máquina virtual** >  **OK**. Isso registra a extensão da VM no cofre.

   ![Painéis Backup e Meta de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Esta etapa registra a extensão da VM com o cofre. O painel **Meta de Backup** fecha e o painel **Política de backup** abre.

3. Na **Política de Backup**, escolha a política que você deseja associar ao cofre. Em seguida, clique em **OK**.
    - Os detalhes da política padrão estão listados no menu suspenso.
    - Clique em **Criar Nova** para criar uma política. [Saiba mais](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) sobre como definir uma política.
    

    ![Painéis "Backup" e "Política de backup"](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. No painel **Escolher máquinas virtuais**, escolha as VMs que usarão a política de backup especificada > **OK**.

    - A máquina virtual selecionada é validada.
    - Você só pode escolher máquinas virtuais na mesma região que o cofre. O backup das VMs só pode ser feito em um único cofre.

   ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Em **Backup**, escolha **Habilitar backup**.

   - Isso implantará a política no cofre e nas VMs e instalará a extensão de backup no agente da VM em execução na VM do Azure.
   - Essa etapa não cria o ponto de recuperação inicial para a VM.

   ![Botão "Habilitar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de habilitar o backup:

- Um backup inicial será executado de acordo com sua agenda de backup.
- O serviço de Backup instala a extensão de backup independentemente de a VM estar em execução.
    - Uma VM em execução oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo.
    -  No entanto, o backup da VM é feito mesmo se ela esteja desativada e a extensão não poderá ser instalada. Isso é conhecido como *VM offline*. Nesse caso, o ponto de recuperação será *consistente com a falha*.
    Observe que o Backup do Azure não oferece suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. Modifique as políticas de backup manualmente, conforme necessário.
  
 ## <a name="run-the-initial-backup"></a>Executar o backup inicial

O backup inicial será executado de acordo com a agenda, a menos que você o execute manualmente de modo imediato. Execute-o manualmente da seguinte maneira:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de Backup**, clique em **Máquina Virtual do Azure**.
3. Na lista **Itens de Backup**, clique nas reticências **...**.
4. Clique em **Fazer backup agora**.
5. Em **Fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido > **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.



## <a name="next-steps"></a>Próximas etapas

- Solucionar problemas que ocorrem com os [agentes de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou o [backup de VM do Azure](backup-azure-vms-troubleshoot.md).
- [Fazer backup de VMs do Azure](backup-azure-vms-first-look-arm.md)
