---
title: Preparar-se para fazer backup de VMs do Azure com o Backup do Azure
description: Descreve como preparar as VMs do Azure para backup com o serviço de Backup do Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: ee7a9c407a26f9334a854c98793db8fc01244e2a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994667"
---
# <a name="prepare-to-back-up-azure-vms"></a>Preparar-se para fazer backup de VMs do Azure

Este artigo descreve como se preparar para fazer o backup de uma VM do Azure usando um cofre dos Serviços de Recuperação do [Backup do Azure](backup-introduction-to-azure-backup.md). A preparação para backup inclui:


> [!div class="checklist"]
> * Antes de começar, analise as limitações e os cenários com suporte.
> * Verifique os pré-requisitos, incluindo requisitos de VM do Azure e a conectividade da rede.
> * Crie um cofre.
> * Escolha como o armazenamento deve replicar.
> * Descubra máquinas virtuais, configure a política e as configurações de backup.
> * Habilitar o backup para VMs escolhidas


> [!NOTE]
   > Este artigo descreve como fazer backup de VMs do Azure ao configurar um cofre e escolher as VMs para fazer backup. Isso é útil se você quiser fazer backup de várias VMs. Você também pode fazer backup de uma VM do Azure diretamente nas configurações da VM. [Saiba mais](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>Antes de começar

1. [Obtenha uma visão geral](backup-azure-vms-introduction.md) do Backup do Azure para VMs do Azure.
2. Analise as limitações e os detalhes de suporte abaixo.

   **Suporte/limitação** | **Detalhes**
   --- | ---
   **Sistema operacional Windows** | Windows Server 2008 R2 de 64 bits ou posterior.<br/><br/> Windows Client 7 de 64 bits ou posterior.
   **Sistema operacional Linux** | Você pode fazer backup de distribuições do Linux de 64 bits [com suporte do Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), com exceção do CoreOS Linux.<br/><br/> Analise os sistemas operacionais Linux que [oferecem suporte à restauração de arquivos](backup-azure-restore-files-from-vm.md#for-linux-os).<br/><br/> Outras distribuições do Linux devem funcionar, contanto que o agente de VM esteja disponível na VM e exista suporte para Python. No entanto, não há suporte para essas distribuições.
   **Região** | Você pode fazer backup de VMs do Azure em todas as [regiões com suporte](https://azure.microsoft.com/regions/#services). Se não houver suporte para uma região, você não poderá escolhê-lo ao criar o cofre.<br/><br/> Você não pode fazer backup e restaurar entre regiões do Azure. Em uma única região apenas.
   **Limite do disco de dados** | Você não pode fazer backup de VMs com mais de 16 discos de dados.
   **Armazenamento compartilhado** | Não é recomendável fazer backup de VMs usando o CSV ou o Servidor de Arquivos de Escalabilidade Horizontal. Os gravadores de CSV provavelmente falharão.
   **Criptografia do Linux** | O backup de máquinas virtuais de Linux criptografadas com criptografia LUKS (Linux Unified Key Setup) não é compatível.
   **Consistência das VMs** | O Backup do Azure não dá suporte à consistência de várias VMs.
   **Rede** | Os dados de backup não incluem unidades de rede montadas anexadas à VM.<br/><br/>
   **Instantâneos** | A criação de instantâneos no disco habilitado do Accelerator de gravação ainda não tem suporte. Ele impede o Backup do Azure de tirar um instantâneo consistente com o aplicativo de todos os discos de VM.
   **PowerShell** | Há uma série de ações que só estão disponíveis no PowerShell:<br/><br/> - Restauração de VMs gerenciadas por balanceadores de carga internos/externos ou com vários endereços IP ou adaptadores reservados. [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> - Restauração de uma VM do controlador de domínio em uma configuração de controlador de vários domínios. [Saiba mais](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
   **Hora do sistema** | O Backup do Azure não oferece suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. Modifique as políticas de backup manualmente, conforme necessário.
   **Contas de armazenamento** | Se você estiver usando uma conta de armazenamento restrito de rede, ative **Permitir que serviços Microsoft confiáveis acessem esta conta de armazenamento** para que o serviço de Backup do Azure possa acessar a conta. Não há suporte para a recuperação de nível de item para contas de armazenamento restritas de rede.<br/><br/> Na conta de armazenamento, certifique-se de que as configurações de **Firewalls e redes virtuais** permitem o acesso de **Todas as redes**.


## <a name="prerequisites"></a>Pré-requisitos

- Você deve criar o cofre na mesma região das VMs do Azure que deseja fazer backup.
- Antes de começar, verifique as regiões de VM do Azure.
    - Se você tiver VMs em várias regiões, crie um cofre de backup em cada região.
    - Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre e o serviço de Backup do Azure lidam com isso automaticamente.
- Verifique se o agente de VM está instalado em VMs do Azure que você deseja fazer backup.



### <a name="install-the-vm-agent"></a>Instalar o agente de VM

Para habilitar o backup, o Backup do Azure instala uma extensão de backup (VM Snapshot ou VM Snapshot Linux) no agente da VM que é executado na VM do Azure.
    -  O Agente de VM do Azure é instalado por padrão em qualquer VM Windows implantada de uma imagem do Azure Marketplace. Ao implantar uma imagem do Azure Marketplace do portal, PowerShell, CLI ou de um modelo do Azure Resource Manager, o Agente de VM do Azure também será instalado.
    - Se você migrou uma VM do local, o agente não está instalado. É preciso instalá-lo antes de poder habilitar o backup da VM.

Se necessário, instale o agente da seguinte maneira.

**VM** | **Detalhes**
--- | ---
**VMs do Windows** | [Faça o download e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o agente com permissões de administrador no computador.<br/><br/> Para verificar a instalação, em *C:\WindowsAzure\Packages* na VM, clique com o botão direito do mouse em WaAppAgent.exe > **Propriedades**, > guia **Detalhes**. **A versão do produto** deve ser 2.6.1198.718 ou superior.
**VMs do Linux** | Instalação usando um RPM ou um pacote DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o Azure do Agente Linux do Azure. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não recomendamos instalá-lo a partir daí.
Se você tiver problemas para fazer backup da VM do Azure, use a tabela a seguir para verificar se o agente de VM do Azure está instalado corretamente na máquina virtual. A tabela fornece informações adicionais sobre o agente de VM para VMs Windows e Linux.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

A extensão de backup em execução na VM deve ter acesso de saída aos endereços IP públicos do Azure. Para permitir o acesso, você pode:


- **Regras NSG**: Permitir os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Você pode adicionar uma regra que permita acesso ao serviço de Backup do Azure usando [uma marca de serviço](../virtual-network/security-overview.md#service-tags) em vez de permitir cada intervalo de endereço individualmente e ter que gerenciá-los ao longo do tempo.
- **Proxy**: Implante um servidor de proxy HTTP para rotear o tráfego.
- **Firewall do Azure**: permite o tráfego por meio do Firewall do Azure na VM, usando uma marca de FQDN para o serviço de Backup do Azure.

Ao decidir entre uma das opções, considere as vantagens e desvantagens.

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
**NSG** | Sem custo adicional. Simples de gerenciar com marcas de serviço | Fornece acesso ao Azure por completo, não somente ao armazenamento. |
**Proxy HTTP** | É permitido o controle granular em relação às URLs de armazenamento.<br/><br/> Único ponto de acesso à Internet para VMs.<br/><br/> Custos adicionais para o proxy.
**Marcas de FQDN** | Simples de usar se você tiver o Firewall do Azure configurado em uma sub-rede da VNet | Não é possível criar suas próprias marcas de FQDN ou modificar os FQDNs em uma marca.



Se você usa o Managed Disks do Azure, pode ser necessário abrir outra porta (porta 8443) nos firewalls.



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurar uma regra NSG para permitir o acesso de saída ao Azure

Se sua VM do Azure tiver acesso gerenciado por um NSG, permita o acesso de saída do armazenamento de backup aos intervalos e portas necessários.



1. Na VM > **Rede**, clique em **Adicionar regra de porta de saída**.
- Se você tiver uma regra que nega o acesso, a nova regra de permissão deverá ser maior. Por exemplo, se você tiver uma regra **Deny_All** definida com prioridade 1000, a nova regra deverá ser definida com um valor inferior a 1000.
2. Em **Adicionar regra de segurança de saída**, clique em **Avançado**.
3. Em Fonte, escolha **VirtualNetwork**.
4. Nos **Intervalos de porta de origem**, digite um asterisco (*) para permitir o acesso de saída de qualquer porta.
5. Em **Destino**, escolha **Marca de Serviço**. Na lista, escolha Armazenamento.<region>. A região é a região em que o cofre e as VMs que você deseja fazer backup estão localizados.
6. Em **Intervalos de portas de destino**, escolha a porta.

    - VM com discos não gerenciados e conta de armazenamento não criptografada: 80
    - VM com discos não gerenciados e conta de armazenamento criptografada: 443 (configuração padrão)
    - VM gerenciada: 8443.
1. Em **Protocolo**, escolha **TCP**.
2. Em **Prioridade**, dê a ele um valor de prioridade menor que qualquer regra de negação maior.
3. Forneça um nome e descrição para a regra e, em seguida, clique em **OK**.

Você pode aplicar a regra NSG a várias VMs para permitir o acesso de saída ao Azure para o Backup do Azure.

Este vídeo orienta você durante o processo.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>Rotear o tráfego de backup por meio de um proxy

Você pode rotear o tráfego de backup por um proxy e, em seguida, fornecer o acesso de proxy aos intervalos necessários do Azure.

Configure sua VM do proxy para permitir o seguinte:

- A VM do Azure deve rotear todo o tráfego HTTP voltado para a Internet pública por meio do proxy.
- O proxy deve permitir o tráfego de entrada a partir de VMs na rede virtual aplicável (VNet).
- O NSG **NSF-lockdown** precisa de uma regra que permita a saída do tráfego de Internet da VM do proxy.

Veja como o proxy deve ser configurado. Usamos valores de exemplo. Você deve substitui-los pelos seus próprios valores.

#### <a name="set-up-a-system-account-proxy"></a>Configurar um proxy de conta do sistema
Se você não tiver um proxy de conta do sistema, configure um da seguinte maneira:

1. Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Execute **PsExec.exe -i -s cmd.exe** para executar o prompt de comando em uma conta do sistema.
3. Execute o navegador no contexto do sistema. Por exemplo:  **PROGRAMFILES%\Internet Explorer\iexplore.exe** para o Internet Explorer.  
4. Defina as configurações de proxy.
    - Em máquinas do Linux:
        - Adicione esta linha ao arquivo **/etc/environment**:
            - endereço IP **proxy_http=http://proxy:porta do proxy**
        - Adicione estas linhas ao arquivo **/etc/waagent.conf**:
            - **Endereço IP HttpProxy.Host=proxy**
            - **Porta HttpProxy.Port=proxy**
    - Em computadores Windows, nas configurações do navegador, especifique que um proxy deverá ser usado. Se estiver usando um proxy em uma conta de usuário, você poderá usar esse script para aplicar a configuração no nível de conta do sistema.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>Permitir conexões de entrada no proxy

1. Permite conexões de entrada nas configurações do proxy.
2. Por exemplo, abra o **Firewall do Windows com Segurança Avançada**.
    - Clique com o botão direito do mouse em **Regras de Entrada** > **Nova Regra**.
    - Em **Tipo de Regra** escolha **Personalizado** > **Avançar**.
    - Em **Programa**, escolha **Todos os Programas** > **Avançar**.
    - Em **Protocolos e portas**, defina o tipo como **TCP**, **Portas locais** como **Portas específicas** e **Porta remota** como **Todas as portas**.
    - Conclua o assistente e especifique um nome para a regra.

#### <a name="add-an-exception-rule-to-the-nsg"></a>Adicionar uma regra de exceção ao NSG

No NSG **NSF-lockdown**, permita o tráfego de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS).

- O script do PowerShell a seguir fornece um exemplo de como habilitar o tráfego.
- Em vez de permitir a saída para todos os endereços públicos de Internet, você pode especificar um intervalo de endereços IP (-DestinationPortRange) ou usar a marca de serviço storage.region.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>Permitir o acesso do firewall com marca de FQDN

Você pode configurar o Firewall do Azure para permitir o acesso de saída para o tráfego de rede para o Backup do Azure.

- [Saiba como](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) implantar o Firewall do Azure.
- [Leia sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) as marcas de FQDN.


## <a name="create-a-vault"></a>Criar um cofre

Um cofre dos Serviços de Recuperação de Backup armazena backups e pontos de recuperação criados ao longo do tempo e armazena as políticas de backup associadas às máquinas submetidas a backup. Crie um cofre da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu **Hub**, escolha **Procurar** e digite **Serviços de Recuperação**. Conforme você começa a digitar, sua entrada filtra a lista de recursos. Selecione **Cofres de Serviços de Recuperação**.

    ![Digitando na caixa e selecionando "Cofres de Serviços de Recuperação" nos resultados](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    A lista de cofres de Serviços de Recuperação aparecerá.
3. No menu **Cofres de Serviços de Recuperação**, selecione **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    O painel **Cofres de Serviços de Recuperação** é aberto. Ele solicita que você forneça informações para **Nome**, **Assinatura**, **Grupo de recursos** e **Localização**.

    ![Painel "Cofres de Serviços de Recuperação"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Em **Nome**, digite um nome amigável para identificar o cofre.
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


## <a name="configure-backup"></a>Configurar o backup

Descubra as VMs na assinatura e configure o backup.

1. No cofre > **Visão Geral**, clique em **+ Backup**

   ![Botão Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Os painéis **Backup** e **Meta de Backup** abrem.

2. Em **Meta de backup**> **Onde sua carga de trabalho é executada?**, escolha **Azure**. Em **Do que você deseja fazer backup?**, escolha **Máquina virtual** >  **OK**. Isso registra a extensão da VM no cofre.

   ![Painéis Backup e Meta de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Esta etapa registra a extensão da VM com o cofre. O painel **Meta de Backup** fecha e o painel **Política de backup** abre.

3. Na **Política de Backup**, escolha a política que você deseja associar ao cofre. Em seguida, clique em **OK**.
    - Os detalhes da política padrão estão listados no menu suspenso.
    - Clique em **Criar Nova** para criar uma política. [Saiba mais](backup-azure-vms-first-look-arm.md#defining-a-backup-policy) sobre como definir uma política.

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

- A política de backup é executada seguindo sua agenda de backup.
- O serviço de Backup instala a extensão de backup independentemente de a VM estar em execução.
    - Uma VM em execução oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo.
    -  No entanto, o backup da VM é feito mesmo se ela esteja desativada e a extensão não poderá ser instalada. Isso é conhecido como *VM offline*. Nesse caso, o ponto de recuperação será *consistente com a falha*.
- Se você quiser gerar um backup sob demanda para a VM imediatamente, em **Itens de Backup**, clique nas reticências (...) ao lado da VM > **Fazer backup agora**.


## <a name="next-steps"></a>Próximas etapas

- Solucionar problemas que ocorrem com os [agentes de VM do Azure](/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou o [backup de VM do Azure](backup-azure-vms-troubleshoot.md).
- [Fazer backup de VMs do Azure](backup-azure-vms-first-look-arm.md)
