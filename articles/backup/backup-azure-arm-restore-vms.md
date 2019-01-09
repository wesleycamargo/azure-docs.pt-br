---
title: 'Serviço de Backup do Azure: restaurar máquinas virtuais usando o portal do Azure'
description: Restaure uma máquina virtual do Azure de um ponto de recuperação usando o portal do Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: restaurar o backup; como restaurar; ponto de recuperação;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793369"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Usar o portal do Azure para restaurar máquinas virtuais
Proteja seus dados criando instantâneos de dados em intervalos definidos. Esses instantâneos são conhecidos como pontos de recuperação e são armazenados nos cofres dos Serviços de Recuperação. Se for necessário reparar ou recriar uma VM (máquina virtual), você poderá restaurá-la de qualquer um dos pontos de recuperação salvos. Quando restaura um ponto de recuperação, você pode:

* Criar uma nova VM: De um ponto de recuperação pontual da sua VM de backup.
* Restaurar discos: usar o modelo que acompanha o processo para personalizar a VM restaurada ou fazer uma recuperação de arquivos individuais.

Este artigo explica como restaurar uma VM em uma nova VM, ou restaurar todos os discos de backup. Para a recuperação de arquivos individuais, consulte [Recuperar arquivos do backup de VM do Azure](backup-azure-restore-files-from-vm.md).

![Três maneiras de restaurar do backup de VM](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


A restauração de uma VM ou de todos os discos do backup de VM envolve duas etapas:

* Selecionar um ponto para restauração.
* Selecionar o tipo de restauração
    - Opção 1: Criar uma nova VM
    - Opção 2: Restaurar discos.

## <a name="select-a-restore-point-for-restore"></a>Selecionar um ponto de restauração para restauração
1. Entre no [Portal do Azure](http://portal.azure.com/).

2. No menu Azure, selecione **Todos os serviços**. Na lista de serviços, digite **serviços de recuperação** ou vá para **armazenamento** em que os **cofres dos serviços de recuperação** são listado, selecione-o.

    ![Cofre dos Serviços de Recuperação](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. A lista de cofres na assinatura é exibida.

    ![Listar cofres de Serviços de Recuperação](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Na lista de cofres do Serviço de Recuperação, selecione o cofre associado à VM que você deseja restaurar. Ao selecionar o cofre, o painel é aberto.

    ![Cofre dos Serviços de Recuperação selecionado](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. No painel do cofre, sobre o **itens de Backup** lado a lado, selecione **Máquina Virtual do Azure**.

    ![Painel do cofre](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. O **itens de Backup** folha com a lista de VMs do Azure é aberta.

    ![Lista de VMs no cofre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. Na lista, selecione uma VM para abrir o painel. O painel da VM é aberto na área de monitoramento, que contém os **pontos de recuperação**. Todas as operações no nível da VM, como **Backup agora**, **Recuperação de arquivos**, **Parar backup** podem ser realizadas a partir deste blade.
Restauração pode ser realizada de várias maneiras nessa folha. Observe que esta folha lista apenas os últimos 30 dias de pontos de recuperação.

    - Para restaurar usando um ponto de restauração dos últimos 30 dias, clique com o botão direito na VM > **Restaurar VM**.
    - Para restaurar usando um ponto de restauração com mais de 30 dias, clique no link em **Pontos de restauração**.
    - Para listar e filtrar as VMs com datas personalizadas, clique em **Restaurar VM** no menu. Use o filtro para modificar o intervalo de tempo para os pontos de restauração exibidos. Você também pode filtrar os tipos diferentes de consistência de dados.
8. Examine as configurações do ponto de restauração:
    - A consistência de dados mostra o [tipo de consistência](backup-azure-vms-introduction.md#consistency-types) no ponto de recuperação.
    - O **Tipo de Recuperação** mostra onde o ponto é armazenado (em uma conta de armazenamento, no cofre ou ambos. [Saiba mais](https://azure.microsoft.com/blog/large-disk-support/) sobre ponto de recuperação instantânea.

  ![Pontos de restauração](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Selecione um ponto de restauração.

10. Selecione **Restaurar Configuração**. A folha **Configuração de restauração** é aberta.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauração de VM
Após selecionar o ponto de restauração, escolha uma configuração de restauração de VM. Para configurar a VM restaurada, você pode usar o portal do Azure ou o PowerShell.

### <a name="restore-options"></a>Restaurar opções

**Opção** | **Detalhes**
--- | ---
**Criar nova VM** | Equivalente à criação rápida de uma VM. Coloca uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> As configurações da VM podem ser modificadas como parte do processo de restauração.
**Criar novo disco de restauração** | Cria uma VM que você pode personalizar como parte do processo de restauração.<br/><br/> Esta opção copia VHDs à conta de armazenamento que você especificar.<br/><br/> - A conta de armazenamento deve estar na mesma localização que o cofre.<br/><br/> Se você não tiver uma conta de armazenamento apropriada, precisará criar uma.<br/><br/> O tipo de replicação da conta de armazenamento é mostrado entre parênteses. Não há suporte para ZRS (Armazenamento com redundância de zona).<br/><br/> Na conta de armazenamento, você pode anexar os discos restaurados a uma VM existente ou criar uma nova VM a partir de discos restaurados usando o PowerShell.
**Substituir existente** | Com essa opção, você não precisa criar uma VM.<br/><br/> A VM atual deve existir. Se ela tiver sido excluída, essa opção não poderá ser usada.<br/><br/> O Backup do Azure tira um instantâneo da VM existente e o armazena no local de preparo especificado. Os discos existentes conectados à VM são então substituídos pelo ponto de restauração selecionado. O instantâneo criado anteriormente é copiado para o cofre e armazenado como um ponto de recuperação de acordo com a política de retenção de backup.<br/><br/> Substituir existente tem suporte para VMs gerenciadas não criptografadas. Não há suporte para discos não gerenciados, [VMs generalizadas](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), ou para VMs [criadas usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauração tiver mais ou menos discos que a VM atual, o número de discos no ponto de restauração refletirá apenas na VM.

> [!NOTE]
> Se você deseja restaurar uma VM com configurações de rede avançadas, por exemplo, se ela for gerenciada pelo balanceador de carga interno ou externo, ou ter várias NICs ou vários endereços IP reservados, restaure-a com o PowerShell. [Saiba mais](#restore-vms-with-special-network-configurations).
> Se uma VM do Windows usa [licenciamento de HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), use a opção **Criar um novo disco de restauração** e, em seguida, use o PowerShell ou o modelo de restauração para criar a VM com o **Tipo de licença** definido como **Windows_Server**. Essa configuração também pode ser aplicada após a criação da VM.


Especifique as configurações de restauração da seguinte maneira:

1. Em **Restaurar**, selecione um [ponto de restauração](#select-a-restore-point-for-restore) > **Restaurar configuração**.
2. Em **Restaurar configuração**, selecione como você deseja restaurar a VM de acordo com as configurações resumidas na tabela acima.

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Criar uma nova VM

1. Em **Restaurar configuração** > **Criar nova** > **Tipo de restauração**, selecione **Criar uma máquina virtual**.
2. Em **Nome da máquina virtual**, especifique uma máquina virtual que não exista na assinatura.
3. No **Grupo de recursos**, selecione um grupo de recursos existente para a nova máquina virtual ou crie um novo com um nome exclusivo globalmente. Se você atribuir um nome que já exista, o Azure atribuirá ao grupo o mesmo nome que a VM.
4. Em **Rede virtual**, selecione a rede virtual na qual a VM será colocada. Todas as redes virtuais associadas à assinatura são exibidas. Escolher a sub-rede. A primeira sub-rede será selecionada por padrão.
5. Em **Local de Armazenamento**, especifique o tipo de armazenamento usado para a VM.

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Em **Restaurar configuração**, selecione **OK**. Em **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.



## <a name="create-new-restore-disks"></a>Criar novos discos de restauração

1. Em **Restaurar configuração** > **Criar nova** > **Tipo de restauração**, selecione **Restaurar discos**.
2. No **Grupo de recursos**, selecione um grupo de recursos existente para os discos recuperados ou crie um novo com um nome exclusivo globalmente.
3. Em **Conta de armazenamento**, especifique a conta para a qual copiar os VHDs. Verifique se a conta está na mesma região que o cofre.

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Em **Restaurar configuração**, selecione **OK**. Em **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.
5. Depois que o disco for restaurado, você poderá fazer qualquer uma das opções a seguir para concluir a operação de restauração de VM.

    - Use o modelo que foi gerado como parte da operação de restauração para personalizar as configurações e disparar a implantação da VM. Você edita as configurações de modelo padrão e envia o modelo para implantação da VM.
    - Você pode [anexar os discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) a uma VM existente.
    - Você pode [criar uma nova VM](backup-azure-vms-automation.md#restore-an-azure-vm) de discos restaurados usando o PowerShell.


## <a name="replace-existing-disks"></a>Substituir discos existentes

Use essa opção para substituir discos existentes na VM atual pelo ponto de restauração selecionado.

1. Em **Restaurar configuração**, clique em **Substituir existente**.
2. Em **Tipo de restauração**, selecione **Substituir discos** (o ponto de restauração que substituirá o disco ou os discos de VM existentes).
3. Em **local de preparo**, especifique onde os instantâneos dos discos gerenciados atuais devem ser salvos.

   ![Restaurar o Assistente de configuração de substituir existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração
Após você disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhar a operação de restauração. O serviço de backup também cria e exibe temporariamente a notificação na área de **Notificações** do portal. Se não vir a notificação, selecione o símbolo **Notificações** para exibir suas notificações.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Clique no hiperlink das notificações para acessar a lista **BackupJobs**. Todos os detalhes das operações de um determinado trabalho estão disponíveis nas listas **BackupJobs**. Você pode ir para **BackupJobs** do painel do cofre clicando-se os trabalhos de Backup em bloco, selecione **máquina virtual do Azure** para exibir os trabalhos associados ao cofre.

A folha **Trabalhos de backup** é aberta e exibe a lista de trabalhos.

![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

A **Barra de progresso** agora está disponível na folha **Detalhes de restauração**. A folha **Detalhes de Restauração** pode ser aberta clicando em qualquer trabalho de restauração que tenha o status **em andamento**. A **barra de progresso** está disponível em todas as variantes de restaurações, como **Criar nova**, **Restaurar disco** e **Substituir existente**. Os detalhes transportados pela barra de progresso de restauração são **tempo de restauração estimado**, **percentual da restauração** e **número de bytes transferidos**.

Os detalhes da barra de progresso de restauração são fornecidos abaixo:

- **Tempo estimado de restauração** inicialmente fornece o tempo necessário para concluir a operação de restauração. À medida que a operação progride, o tempo gasto reduz e chega a 0 quando a operação de restauração é concluída.
- **Porcentagem de restauração** fornece o percentual de dados que a operação de restauração concluiu.
- **Número de bytes transferidos** está disponível na subtarefa quando a restauração ocorre por meio de criar nova VM. Isso fornece os detalhes de quantos números de bytes foram transferidos em relação ao número total de bytes a serem transferidos.


## <a name="use-templates-to-customize-a-restored-vm"></a>Usar modelos para personalizar uma VM restaurada
Após a [conclusão da operação de restauração de discos](#Track-the-restore-operation), use o modelo que foi gerado como parte da operação de restauração para criar uma nova VM com uma configuração diferente da configuração de backup. Você também pode usar isso para personalizar os nomes dos recursos que foram criados durante o processo de criação de uma nova VM de um ponto de restauração.

![Detalhamento do trabalho de restauração](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

Para obter o modelo que foi gerado como parte da opção de discos de restauração:

1. Vá para **Restaurar detalhes do trabalho** correspondente ao trabalho.

2. Na tela **Detalhes do Trabalho de Restauração**, selecione **Implantar Modelo** para iniciar a implantação de modelo.

3. Na folha **Implantar modelo** para a implantação personalizada, use a implantação de modelo para [editar e implantar o modelo](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) ou acrescentar mais personalizações [criando um modelo](../azure-resource-manager/resource-group-authoring-templates.md) antes de implantar.

  ![Carregar a implantação de modelo](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Depois de inserir os valores necessários, aceite os **Termos e Condições** e selecione **Comprar**.

  ![Enviar a implantação de modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>Etapas pós-restauração
* Se você usar uma distribuição Linux baseada em inicialização da nuvem, como o Ubuntu, a senha será bloqueada após a restauração por motivos de segurança. Use uma extensão VMAccess na VM restaurada para [redefinir a senha](../virtual-machines/linux/reset-password.md). Recomendamos o uso de chaves SSH nessas distribuições para evitar a redefinição da senha após a restauração.
* Extensões presentes durante a configuração do backup serão instaladas, mas não serão habilitadas. Se encontrar um problema, reinstale as extensões.
* Se, após a restauração, a VM de backup tiver IP estático, a VM restaurada terá um IP dinâmico para evitar conflitos ao criar uma VM restaurada. Saiba mais sobre como você pode [adicionar um IP estático a uma VM restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Uma VM restaurada não tem um valor de disponibilidade definido. Recomendamos o uso da opção de discos de restauração para [adicionar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) ao criar uma VM do PowerShell ou de modelos usando discos restaurados.


## <a name="backup-for-restored-vms"></a>Backup de VMs restauradas
Se você tiver restaurado uma VM no mesmo grupo de recursos e com o mesmo nome que a VM do backup original, o backup continuará na VM após a restauração. Se você tiver restaurado a VM para outro grupo de recursos ou tiver especificado um nome diferente para a VM restaurada, a VM será tratada como se fosse uma nova VM. Você precisa configurar o backup para a VM restaurada.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Restaurar uma VM durante um desastre de datacenter do Azure
O Backup do Azure permite restaurar backups de VMs para o data center emparelhado caso ocorra um desastre no datacenter principal em que as VMs estão sendo executadas e você tenha configurado o cofre de backup para ser redundante geograficamente. Durante esses cenários, selecione uma conta de armazenamento que esteja presente em um datacenter emparelhado. O restante do processo de restauração permanece o mesmo. O Backup usa o serviço de computação da área geográfica emparelhada para criar a VM restaurada. Para obter mais informações, consulte [Resiliência de datacenter do Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Restaurar VMs do controlador de domínio
O backup de VMs do DC (controlador de domínio) é um cenário com suporte no Backup. No entanto, é necessário ter cuidado durante o processo de restauração. O processo de restauração correto depende da estrutura do domínio. No caso mais simples, você tem um único DC em um único domínio. Mais comumente para cargas de produção, você terá um único domínio com vários DCs, talvez com alguns DCs locais. Por fim, você pode ter uma floresta com vários domínios.

Da perspectiva do Active Directory, a VM do Azure é como qualquer outra VM em um hipervisor moderno com suporte. A principal diferença com hipervisores locais é que nenhum console da VM está disponível no Azure. Um console é necessário para determinados cenários, como a recuperação usando um backup do tipo BMR (recuperação bare-metal). No entanto, a restauração de VM do cofre de backup é uma substituição completa para a BMR. O DSRM (Modo de Restauração dos Serviços de Diretório) também está disponível, portanto, todos os cenários de recuperação do Active Directory são viáveis. Para obter mais informações, consulte as [Considerações de backup e restauração para controladores de domínio virtualizados](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) e [Planejamento para recuperação de floresta do Active Directory](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Controlador de domínio único em um único domínio
A VM pode ser restaurada (como qualquer outra VM) do portal do Azure ou usando o PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Vários controladores de domínio em um único domínio
Quando outros controladores de domínio do mesmo domínio podem ser acessados pela rede, o controlador de domínio pode ser restaurado como uma VM. Se for o último DC restante no domínio ou se uma recuperação em uma rede isolada for executada, um procedimento de recuperação de floresta deverá ser seguido.

### <a name="multiple-domains-in-one-forest"></a>Vários domínios em uma floresta
Quando outros controladores de domínio do mesmo domínio podem ser acessados pela rede, o controlador de domínio pode ser restaurado como uma VM. Em todos os outros casos, é recomendável uma recuperação de floresta.

## <a name="restore-vms-with-special-network-configurations"></a>Restaurar VMs com configurações de rede especiais
É possível fazer backup e restaurar VMs com as seguintes configurações de rede especiais. No entanto, essas configurações exigem algumas considerações especiais ao percorrer o processo de restauração:

* VMs sob balanceadores de carga (internos e externos)
* VMs com vários IPs reservados
* VMs com vários NICs

> [!IMPORTANT]
> Ao criar a configuração de rede especial para VMs, você precisa usar o PowerShell para criar VMs dos discos restaurados.
>
>

Para recriar completamente as VMs após restaurar para o disco, siga estas etapas:

1. Restaure os discos de um cofre dos Serviços de Recuperação usando o [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

1. Crie a configuração da VM necessária para o balanceador de carga/múltiplos NICs/ múltiplos IPs reservados usando os cmdlets do PowerShell. Use-a para criar a VM com a configuração desejada:

    a. Criar uma VM no serviço de nuvem com um [balanceador de carga interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Criar uma VM para se conectar a um [balanceador de carga voltado para a Internet](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Criar uma VM com [diversas NICs](../virtual-machines/windows/multiple-nics.md).

   d. Criar uma VM com [vários IPs reservados](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você pode restaurar suas VMs, consulte o artigo de solução de problemas para obter informações sobre erros comuns com VMs. Além disso, confira o artigo sobre como gerenciar tarefas com suas VMs.

* [Solucionar erros](backup-azure-vms-troubleshoot.md#restore)
* [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)
