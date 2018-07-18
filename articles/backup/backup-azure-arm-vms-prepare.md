---
title: 'Backup do Azure: preparação para backup de máquinas virtuais | Microsoft Docs'
description: Assegure-se de que o ambiente esteja preparado para fazer backup de máquinas virtuais no Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: backups; fazendo backup;
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2018
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 489875e595c9f28a1e30cbb29cde078f1b716f7f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940563"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Preparar seu ambiente para fazer backup das máquinas virtuais implantadas com o Gerenciador de Recursos

Este artigo mostra como preparar seu ambiente para o backup de uma VM (máquina virtual) implantada com o Azure Resource Manager. As etapas mostradas nos procedimentos usam o Portal do Azure. Quando você faz backup de uma máquina virtual, os dados de backup ou os pontos de recuperação são armazenados em um cofre dos Serviços de Recuperação. Os cofres dos Serviços de Recuperação armazenam dados de backup para máquinas virtuais implantadas pelo Resource Manager e clássicas.

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md).

Antes de proteger (ou fazer backup) uma máquina virtual implementada com o Resource Manager, verifique se esses pré-requisitos existem:

* Crie ou identifique um cofre de Serviços de Recuperação *na mesma região que sua máquina virtual*.
* Selecione um cenário, defina a política de backup e os itens a serem protegidos.
* Verifique a instalação de um agente de VM (extensão) na máquina virtual.
* Verifique a conectividade de rede.
* Para VMs Linux, se você desejar personalizar o ambiente de backup para backups consistentes com o aplicativo, siga as [etapas para configurar scripts pré-instantâneo e pós-instantâneo](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Se essas condições já existem em seu ambiente, prossiga para o artigo [Fazer backup das suas VMs](backup-azure-arm-vms.md). Se você precisa configurar ou verificar qualquer um desses pré-requisitos, este artigo orienta você sobre etapas.

## <a name="supported-operating-systems-for-backup"></a>Versões de sistema operacional compatíveis para backup
 * **Linux**: o Backup do Azure é compatível [uma lista de distribuições endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) exceto o CoreOS Linux. 
 
    > [!NOTE] 
    > Outras distribuições personalizadas do Linux devem funcionar, contanto que o agente de VM esteja disponível na máquina virtual e exista suporte para Python. No entanto, não há suporte para essas distribuições.
 * **Windows Server**: não há suporte para versões anteriores ao Windows Server 2008 R2.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitações durante o backup e a restauração de uma VM
Antes de preparar seu ambiente, note as seguintes limitações:

* Não há suporte para o backup de máquinas virtuais com mais de 16 discos de dados.
* Não há suporte para o backup de máquinas virtuais com um endereço IP reservado e nenhum ponto de extremidade definido.
* O backup de máquinas virtuais de Linux criptografadas por meio da criptografia LUKS (Linux Unified Key Setup) não é compatível.
* Não é recomendável fazer backup de VMs que contêm a configuração CSV (Volume Compartilhado Clusterizado) ou Servidor de Arquivos de Escalabilidade Horizontal. Se tiver feito, será esperado que os gravadores CSV falhem. Elas exigem o envolvimento de todas as VMs incluídas na configuração do cluster durante a tarefa de instantâneo. O Backup do Azure não dá suporte à consistência de várias VMs. 
* Os dados de backup não incluem unidades de rede montadas anexadas à VM.
* Não há suporte para a substituição de uma máquina virtual existente durante a restauração. Se você tentar restaurar a VM quando ela existir, a operação de restauração falhará.
* Não há suporte para backup e restauração entre regiões.
* Ao configurar o backup, certifique-se de que as configurações de **Firewalls e redes virtuais** da conta de armazenamento permitem o acesso de Todas as redes.
* Para redes selecionadas, depois de configurar as definições de firewall e rede virtual para sua conta de armazenamento, selecione **Permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento** como uma exceção para habilitar o serviço de Backup do Microsoft Azure para acessar a conta de armazenamento restrita de rede. Não há suporte para a recuperação de nível de item para contas de armazenamento restritas de rede.
* Você pode fazer backup de máquinas virtuais em todas as regiões públicas do Azure. (Veja a [lista](https://azure.microsoft.com/regions/#services) das regiões com suporte.) Se o suporte ainda não estiver disponível para região que você procura, ela não aparecerá na lista suspensa durante a criação de cofre.
* A restauração de uma VM DC (controladora de domínio) que é parte de uma configuração multi-DC tem suporte somente usando o PowerShell. Para saber mais, confira [Restauração de um controlador de domínio com vários DCs](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Apenas há suporte para a restauração de máquinas virtuais que têm as seguintes configurações de rede especial por meio do PowerShell. As VMs criadas por meio do fluxo de trabalho de restauração na interface do usuário não terão essas configurações de rede depois que a operação de restauração for concluída. Para saber mais, confira [Restauração de VMs com configurações de rede especiais](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Máquinas virtuais sob configuração do balanceador de carga (interno e externo)
  * Máquinas virtuais com vários endereços IP reservados
  * Máquinas virtuais com vários adaptadores de rede

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Criar um cofre de Serviços de Recuperação para uma VM
Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre de Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre de Serviços de Recuperação:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu **Hub**, selecione **Procurar** e digite **Serviços de Recuperação**. Conforme você começa a digitar, sua entrada filtra a lista de recursos. Selecione **Cofres de Serviços de Recuperação**.

    ![Digitando na caixa e selecionando "Cofres de Serviços de Recuperação" nos resultados](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    A lista de cofres de Serviços de Recuperação aparecerá.
3. No menu **Cofres de Serviços de Recuperação**, selecione **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    O painel **Cofres de Serviços de Recuperação** é aberto. Ele solicita que você forneça informações para **Nome**, **Assinatura**, **Grupo de recursos** e **Localização**.

    ![Painel "Cofres de Serviços de Recuperação"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Em **Nome**, insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.
5. Selecione **Assinatura** para ver a lista de assinaturas disponíveis. Se você não tiver certeza sobre qual assinatura usar, utilize a assinatura padrão (ou a sugerida). Só haverá múltiplas opções se sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure.
6. Clique em **Grupo de recursos** para ver a lista disponível de grupos de recursos ou em **Novo** para criar um grupo de recursos. Para obter informações completas sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Clique em **Localização** para selecionar a região geográfica do cofre. O cofre *deve* estar na mesma região que as máquinas virtuais que você deseja proteger.

   > [!IMPORTANT]
   > Caso não saiba a localização da VM, feche a caixa de diálogo de criação do cofre e vá para a lista de máquinas virtuais no portal. Se você tem máquinas virtuais em várias regiões, é necessário criar um cofre de Serviços de Recuperação em cada região. Crie o cofre no primeiro local antes de ir para o próximo local. Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre de Serviços de Recuperação e o serviço de Backup do Azure lidam com isso automaticamente.
   >
   >

8. Selecione **Criar**. Talvez demore um pouco para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na área superior direita do portal. Depois que o cofre é criado, ele aparece na lista de cofres de Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

    ![Lista de cofres de backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Agora que você criou o cofre, saiba como configurar a replicação de armazenamento.

## <a name="set-storage-replication"></a>Definir replicação de armazenamento
A opção de replicação de armazenamento permite que você escolha entre o armazenamento com redundância geográfica e armazenamento com redundância local. Por padrão, seu cofre tem armazenamento com redundância geográfica. Deixe a configuração da opção como armazenamento com redundância geográfica para o backup primário. Se você quiser uma opção mais barata que não seja tão durável, escolha o armazenamento com redundância local.

Para editar a configuração de replicação de armazenamento:

1. No painel **Cofres de Serviços de Recuperação**, selecione seu cofre.
    Quando você seleciona o cofre, o painel **Configurações** (que tem o nome do cofre na parte superior) e o painel de detalhes do cofre abrem.

   ![Escolher seu cofre na lista de cofres de backup](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. No painel **Configurações**, use o controle deslizante vertical para rolar para baixo até a seção **Gerenciar** e selecione **Infraestrutura de Backup**. Na seção **Geral**, selecione **Configuração de Backup**. No painel **Configuração de Backup**, escolha a opção de replicação de armazenamento para o cofre. Por padrão, seu cofre tem armazenamento com redundância geográfica.

   ![Lista de cofres de backup](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup principal, continue usando o armazenamento com redundância geográfica. Se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup não primário, considere a escolha do armazenamento com redundância local. Leia mais sobre as opções de armazenamento na [Visão geral da replicação do Armazenamento do Azure](../storage/common/storage-redundancy.md).

3. Se você alterar o tipo de replicação de armazenamento, selecione **Salvar**.
    
Depois de escolher a opção de armazenamento para o cofre, você estará pronto para associar a VM ao cofre. Para iniciar a associação, você deverá descobrir e registrar as máquinas virtuais do Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selecionar a meta de backup, definir a política e os itens a serem protegidos
Antes de registrar uma máquina virtual com um cofre dos Serviços de Recuperação, execute o processo de descoberta para identificar novas máquinas virtuais adicionadas à assinatura. O processo de descoberta consulta o Azure para a lista de máquinas virtuais na assinatura. Se novas máquinas virtuais forem localizadas, o portal exibirá o nome do serviço de nuvem e a região associada. No Portal do Azure, o *cenário* é o que você insere no cofre dos Serviços de Recuperação. A *Política* define quando e com que frequência os pontos de recuperação são criados. A Política também inclui o período de retenção dos pontos de recuperação.

1. Se você já tiver um cofre de Serviços de Recuperação aberto, vá para a etapa 2. Se você não tiver um cofre de Serviços de Recuperação aberto, abra o [Portal do Azure](https://portal.azure.com/). No menu **Hub**, selecione **Mais serviços**.

   a. Na lista de recursos, digite **Serviços de Recuperação**. Conforme você começa a digitar, sua entrada filtra a lista. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

      ![Digitando na caixa e selecionando "Cofres de Serviços de Recuperação" nos resultados](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      A lista de cofres de Serviços de Recuperação aparecerá. Se não houver nenhum cofre em sua assinatura, essa lista estará vazia.

      ![Exibir a lista de cofres dos serviços de recuperação](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Na lista de cofres de Serviços de Recuperação, selecione um cofre.

      O painel **Configurações** e o painel do cofre para o cofre escolhido abrem.

      ![Painel Configurações e painel do cofre](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. No menu do painel do cofre, selecione **Backup**.

   ![Botão Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Os painéis **Backup** e **Meta de Backup** abrem.

3. No painel **Meta de Backup**, defina **Onde sua carga de trabalho é executada?** como **Azure** e **O que você quer fazer de backup?** como **Máquina virtual**. Depois, selecione **OK**.

   ![Painéis Backup e Meta de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Esta etapa registra a extensão da VM com o cofre. O painel **Meta de Backup** fecha e o painel **Política de backup** abre.

   ![Painéis "Backup" e "Política de backup"](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. No painel **Política de backup**, selecione a política de backup que deseja aplicar ao cofre.

   ![Selecionar a política de backup](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Os detalhes da política padrão estão listados no menu suspenso. Se você quiser criar uma nova política, selecione **Criar Nova** no menu suspenso. Para obter instruções sobre como definir uma política de backup, confira [Definindo uma política de backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Selecione **OK** para associar a política de backup ao cofre.

   O painel **Política de backup** fecha e o painel **Selecionar máquinas virtuais** abre.
5. No painel **Selecionar máquinas virtuais**, escolha as máquinas virtuais que serão associadas à política especificada e selecione **OK**.

   ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   A máquina virtual selecionada é validada. Se você não visualizar as máquinas virtuais esperadas, verifique se as máquinas virtuais estão na mesma região do Azure do cofre dos Serviços de Recuperação. Se ainda não for possível visualizar as máquinas virtuais, verifique se elas não estão protegidas com outro cofre. O painel do cofre mostra a região onde existe o cofre dos Serviços de Recuperação.

6. Agora que você definiu todas as configurações para o cofre, no painel **Backup**, selecione **Habilitar backup**. Esta etapa implanta a política no cofre e nas VMs. Essa etapa não cria o ponto de recuperação inicial para a máquina virtual.

   ![Botão "Habilitar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de habilitar o backup com êxito, sua política de backup será executada no prazo especificado. Se você quiser gerar um trabalho de backup sob demanda para fazer backup das máquinas virtuais agora, confira [Disparar o trabalho de backup](./backup-azure-vms-first-look-arm.md#initial-backup).

Se você tiver problemas ao registrar a máquina virtual, confira as seguintes informações sobre como instalar o agente de VM e conectividade de rede. Se as máquinas virtuais criadas no Azure estiverem protegidas, então é provável que as informações a seguir não serão necessárias. Mas se você migrou suas máquinas virtuais para o Azure, confira se o agente de VM foi instalado corretamente e se a sua máquina virtual pode se comunicar com a rede virtual.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalar o agente de VM na máquina virtual
Para a extensão de backup funcionar, o [agente de VM](../virtual-machines/extensions/agent-windows.md) do Azure deve ser instalado na máquina virtual do Azure. Se sua VM tiver sido criada por meio do Azure Marketplace, o agente de VM já estará presente na máquina virtual. 

As informações a seguir são fornecidas para situações em que você *não* está usando uma VM criada por meio do Azure Marketplace. Por exemplo, você migrou uma VM de um datacenter local. Nesse caso, o agente de VM precisa ser instalado para proteger a máquina virtual.

Se você tiver problemas para fazer backup da VM do Azure, use a tabela a seguir para verificar se o agente de VM do Azure está instalado corretamente na máquina virtual. A tabela fornece informações adicionais sobre o agente de VM para VMs Windows e Linux.

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalação do agente de VM |Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação. |<li> Instale o [agente Linux](../virtual-machines/extensions/agent-linux.md) mais recente. Você precisará de privilégios de Administrador para concluir a instalação. É recomendável instalar o agente do seu repositório de distribuição. **Não é recomendável** instalar o agente de VM Linux diretamente do GitHub.  |
| Atualizar o Agente de VM |Atualizar o agente de VM é tão simples quanto reinstalar os [Binários do Agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. |Siga as instruções em [atualizando o agente de VM Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É recomendável atualizar o agente do seu repositório de distribuição. **Não é recomendável** atualizar o agente de VM Linux diretamente do GitHub.<br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. |
| Validação da instalação do Agente de VM |<li>Navegue até a pasta *C:\WindowsAzure\Packages* na VM do Azure. <li>Você deve encontrar o arquivo WaAppAgent.exe presente.<li> Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do Produto deve ser 2.6.1198.718 ou mais recente. |N/D |

### <a name="backup-extension"></a>Extensão de backup
Depois que o Agente de VM for instalado na máquina virtual, o serviço de Backup do Azure instalará a extensão de backup no agente de VM. O serviço do Backup atualiza e corrige continuamente a extensão de backup.

O serviço de Backup instala a extensão de backup independentemente de a VM estar em execução. Uma VM em execução oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o serviço do Backup continua a realizar o backup da VM mesmo quando ela está desativada e a extensão não pode ser instalada. Isso é conhecido como *VM offline*. Nesse caso, o ponto de recuperação será *consistente com a falha*.

## <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede
Para gerenciar os instantâneos de VM, a extensão de backup precisa de conectividade com os endereços IP públicos do Azure. Sem a conexão correta com a Internet, as solicitações HTTP da máquina virtual atingirão o tempo limite e a operação de backup falhará. Se sua implantação tiver restrições de acesso em vigor, por meio de um NSG (grupo de segurança de rede), por exemplo, escolha uma destas opções para fornecer um caminho livre para o tráfego de backup:

* [Realizar a lista de permissões de intervalos de IP do datacenter do Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Implante um servidor de proxy HTTP para rotear o tráfego.

Ao decidir qual opção usar, as desvantagens são entre a capacidade de gerenciamento, o controle granular e o custo.

| Opção | Vantagens | Desvantagens |
| --- | --- | --- |
| Intervalos de IPs na lista de autorizados |Sem custo adicional.<br><br>Para habilitar o acesso em NSG, use o cmdlet **Set-AzureNetworkSecurityRule**. |É complexo para gerenciar, já que os intervalos de IP afetados mudam com o tempo.<br><br>Fornece acesso ao Azure por completo, não somente ao armazenamento. |
| Usar um proxy HTTP |É permitido o controle granular no proxy em relação às URLs de armazenamento.<br><br>Único ponto de acesso à Internet para VMs.<br><br>Não está sujeito a alterações do endereço IP do Azure |Custos adicionais para a execução de uma VM com o software do proxy |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>lista de autorizados de intervalos de IP de datacenter do Azure
Para colocar os intervalos IP do datacenter do Azure na lista de permissões, consulte o [site do Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) para obter detalhes sobre os intervalos de IP e as instruções.

Você pode permitir conexões ao armazenamento da região específica usando [marcas de serviço](../virtual-network/security-overview.md#service-tags). Certifique-se de que a regra que permite o acesso à conta de armazenamento tenha prioridade maior do que a regra que bloqueia o acesso à Internet. 

![NSG com marcas de armazenamento para uma região](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

O vídeo a seguir explica o procedimento passo a passo para configurar marcas de serviço: 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> Marcas de serviço de armazenamento estão disponíveis somente em regiões específicas e estão em versão prévia. Para obter a lista de regiões, consulte [Marcas de serviço para armazenamento](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Usar um proxy HTTP para backups de VM
Ao fazer backup de uma VM, a extensão de backup na VM envia os comandos de gerenciamento de instantâneo para o Armazenamento do Azure usando a API de HTTPS. Roteie o tráfego da extensão de backup por meio do proxy HTTP, pois ele é o único componente configurado para acesso à Internet pública.

> [!NOTE]
> Não recomendamos um software de proxy específico que deve ser usado. Certifique-se de escolher um proxy que seja compatível com as etapas de configuração a seguir.
>
>

A imagem de exemplo a seguir mostra as três etapas de configuração necessárias para usar um proxy HTTP:

* A VM de aplicativo roteia todo o tráfego HTTP voltado para a Internet pública por meio da VM do proxy.
* A VM do proxy permite a passagem do tráfego de entrada de VMs na rede virtual.
* O grupo de segurança de rede chamado Bloqueio de NSF precisa de uma regra de segurança que permita a saída do tráfego de Internet da VM do proxy.

Para usar um proxy HTTP para se comunicar com a Internet pública, conclua as etapas a seguir.

> [!NOTE]
> Essas etapas usam valores e nomes específicos para esse exemplo. Quando estiver inserindo (ou colando) detalhes no código, use os nomes e valores para sua implantação.

#### <a name="step-1-configure-outgoing-network-connections"></a>Etapa 1: Configurar conexões de rede de saída
###### <a name="for-windows-machines"></a>Para computadores Windows
Este procedimento define a configuração do servidor proxy para a conta do sistema local.

1. Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Abra o Internet Explorer executando o seguinte comando em um prompt com privilégios elevados:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. No Internet Explorer, acesse **Ferramentas** > **Opções de Internet** > **Conexões** > **Configurações de LAN**.
4. Verifique as configurações de proxy para a conta do sistema. Defina o IP e a porta do proxy.
5. Feche o Internet Explorer.

O script a seguir define uma configuração de proxy de todo o computador e a usa para todo o tráfego HTTP ou HTTPS de saída. Se você configurou um servidor proxy em uma conta de usuário atual (não uma conta do sistema local), use este script para aplicá-las à SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Se você receber "(407) Autenticação de Proxy Necessária" no log do servidor proxy, verifique se a configuração da sua autenticação está correta.
>
>

###### <a name="for-linux-machines"></a>Para computadores Linux
Adicione a seguinte linha ao arquivo ```/etc/environment``` :

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adicione as linhas abaixo ao arquivo ```/etc/waagent.conf``` :

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Etapa 2: Permitir conexões de entrada no servidor proxy
1. No servidor proxy, abra o Firewall do Windows. A maneira mais fácil de acessar o firewall é pesquisar o **Firewall do Windows com Segurança Avançada**.
2. Na caixa de diálogo **Firewall do Windows com Segurança Avançada**, clique com o botão direito do mouse em **Regras de Entrada** e selecione **Nova Regra**.
3. No Assistente de Nova Regra de Entrada, na página **Tipo de Regra**, selecione a opção **Personalizado** e selecione **Avançar**.
4. Na página **Programa**, selecione **Todos os Programas** e selecione **Avançar**.
5. Na página **Protocolo e Portas**, insira as seguintes informações e selecione **Avançar**:
   * Para **Tipo de protocolo**, selecione **TCP**.
   * Para **Porta local**, selecione **Portas Específicas**. Na caixa a seguir, especifique o número da porta de proxy foi configurada.
   * Para **Porta remota**, selecione **Todas as Portas**.

Para o restante do assistente, aceite as configurações padrão até chegar ao fim. Em seguida, atribua um nome a essa regra. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Etapa 3: Adicionar uma regra de exceção ao NSG
O comando a seguir adiciona uma exceção ao NSG. Essa exceção permite o tráfego TCP de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS). Se você exigir uma porta específica na Internet pública, certifique-se de adicioná-la a ```-DestinationPortRange```.

Em um prompt de comando do Azure PowerShell, digite o seguinte comando:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Próximas etapas
Agora que você já preparou seu ambiente para fazer backup de sua VM, a próxima etapa lógica será criar um backup. O artigo de planejamento oferece informações mais detalhadas sobre o backup de máquinas virtuais.

* [Backup de máquinas virtuais](backup-azure-arm-vms.md)
* [Planeje sua infraestrutura de backup da VM](backup-azure-vms-introduction.md)
* [Gerenciar backups de máquinas virtuais](backup-azure-manage-vms.md)
