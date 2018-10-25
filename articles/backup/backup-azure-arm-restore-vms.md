---
title: 'Backup do Azure: restaurar máquinas virtuais usando o portal do Azure'
description: Restaure uma máquina virtual do Azure de um ponto de recuperação usando o portal do Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: restaurar o backup; como restaurar; ponto de recuperação;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: 450314dddd49825bae689701b694f9a26758835e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377632"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Usar o portal do Azure para restaurar máquinas virtuais
Proteja seus dados criando instantâneos de dados em intervalos definidos. Esses instantâneos são conhecidos como pontos de recuperação e são armazenados nos cofres dos Serviços de Recuperação. Se for necessário reparar ou recriar uma VM (máquina virtual), você poderá restaurá-la de qualquer um dos pontos de recuperação salvos. Quando restaura um ponto de recuperação, você pode:

* Criar uma nova VM, que é uma representação pontual da VM copiada em backup.
* Restaurar discos e usar o modelo que acompanha o processo para personalizar a VM restaurada ou fazer uma recuperação de arquivos individuais.

Este artigo explica como restaurar uma VM em uma nova VM, ou restaurar todos os discos de backup. Para a recuperação de arquivos individuais, consulte [Recuperar arquivos do backup de VM do Azure](backup-azure-restore-files-from-vm.md).

![Três maneiras de restaurar do backup de VM](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [o Azure Resource Manager e o clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece as informações e os procedimentos usados para restaurar VMs implantadas usando o modelo do Resource Manager.
>
>

A restauração de uma VM ou de todos os discos do backup de VM envolve duas etapas:

* Selecionar um ponto para restauração.
* Selecionar o tipo de restauração, criar uma nova VM ou restaurar discos e especificar os parâmetros necessários.

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
Restauração pode ser realizada de várias maneiras nessa folha. Observe que Esta folha lista apenas os últimos 30 dias de pontos de recuperação.

    a) Clique com o botão direito do mouse no ponto de recuperação neste blade (menos de 30 dias) e inicie **Restore VM**.

    b) para restaurar a recuperação de pontos de maior que 30 dias, clique aqui fornecida na folha pode ser usado.

    c) **A restauração da VM** no cabeçalho do menu fornece uma opção para listar e filtrar as VMs em datas personalizadas, conforme preferir.

    Usar o filtro para alterar o intervalo de tempo dos pontos de restauração é exibido. Por padrão, são exibidos pontos de restauração de todas as consistências. Modifique o filtro Todos os pontos de restauração para selecionar uma consistência de ponto de restauração específica. Para saber mais sobre cada tipo de ponto de restauração, consulte [Consistência de dados](backup-azure-vms-introduction.md#data-consistency).

    Opções de consistência de ponto de restauração:
    - Pontos de restauração com controle de falhas
    - Pontos de restauração consistentes do aplicativo
    - Pontos de restauração consistentes de sistema de arquivos
    - Todos os pontos de restauração

  ![Pontos de restauração](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > O tipo de recuperação representa se está na conta de armazenamento do cliente, no cofre ou em ambos. Saiba mais sobre [ponto de recuperação instantânea](https://azure.microsoft.com/blog/large-disk-support/).

8. Na folha **Restaurar**, selecione **Ponto de restauração**.

    ![Folha Selecionar ponto de restauração](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    A folha **restaurar** mostra que o ponto de restauração é definido ao clicar em **Ok**.
9. Se ainda não estiver nela, vá até a folha **Restaurar**. Verifique se um [ponto de restauração está selecionado](#select-a-restore-point-for-restore) e selecione **Configuração de restauração**. A folha **Configuração de restauração** é aberta.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauração de VM
Após selecionar o ponto de restauração, escolha uma configuração de restauração de VM. Para configurar a VM restaurada, você pode usar o portal do Azure ou o PowerShell.

1. Se ainda não estiver nela, vá até a folha **Restaurar**. Verifique se um [ponto de restauração está selecionado](#select-a-restore-point-for-restore) e selecione **Configuração de restauração**. A folha **Configuração de restauração** é aberta.
2. Atualmente, este blade tem duas opções, uma sendo **Create New**, que é o padrão e a outra é **Replace existing**, que é uma restauração no local para substituir o (s) disco (s) apenas mantendo as configurações existentes. extensões.

> [!NOTE]
> Estamos trabalhando para substituir a VM inteira com o (s) disco (s), configurações de rede, configurações e extensões nos próximos meses.
>
>

 Na opção **Criar Novo**, que restaura os dados para a nova VM ou novo (s) disco (s), você tem duas opções:

 ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **Criar máquina virtual**
 - **Restaurar discos**

![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

O portal fornece uma opção de **Criação Rápida** para a VM restaurada. Para personalizar a configuração da VM ou os nomes dos recursos criados como parte da criação de uma nova opção de VM, use o PowerShell ou o portal para restaurar os discos de backup. Use comandos do PowerShell para anexá-los à sua opção de configuração da VM. Ou você pode usar o modelo fornecido com os discos restaurados para personalizar a VM restaurada. Para obter informações sobre como restaurar uma VM que tem várias NICs ou que está sob um balanceador de carga, consulte [Restaurar uma VM com configurações de rede especiais](#restore-vms-with-special-network-configurations). Se sua VM do Windows usar o [licenciamento de HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaure os discos e use o PowerShell/modelo conforme especificado neste artigo para criar a VM. Não deixe de especificar o **Tipo de Licença** como "Windows_Server" ao criar a VM para tirar proveito dos benefícios do HUB na VM restaurada. Observe que isso pode ser feito posteriormente após a criação da VM.

## <a name="create-a-new-vm-from-a-restore-point"></a>Criar uma nova VM de um ponto de restauração
1. Sobre o **restaurar configuração** folha mencionado na antes da seção, insira ou selecione valores para cada um dos seguintes campos:

    a. **Tipo de Restauração**. Crie uma máquina virtual.

    b. **Nome da máquina virtual**. Insira o nome da VM que não existe na assinatura.

    c. **Grupo de recursos**. Use um grupo de recursos existente ou crie um novo. Se estiver restaurando uma VM clássica, use este campo para especificar o nome de um novo serviço de nuvem. Se estiver criando um novo grupo de recursos/serviço de nuvem, o nome deverá ser exclusivo globalmente. Normalmente, o nome do serviço de nuvem está associado a uma URL voltada para o público, por exemplo: [cloudservice].cloudapp.net. Se você tentar usar um nome de serviço de nuvem/grupo de recursos de nuvem que já esteja sendo usado, o Azure atribuirá ao serviço de nuvem/grupo de recursos o mesmo nome que o da VM. O Azure exibe serviços de nuvem/grupos de recursos e VMs não associadas a nenhum grupo de afinidades. Para obter mais informações, consulte [Como migrar de grupos de afinidades para uma rede virtual regional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Rede virtual**. Quando você cria a VM, selecione a rede virtual. O campo fornece todas as redes virtuais associadas à assinatura. O grupo de recursos da VM é exibido entre parênteses.

    e. **Sub-rede**. Se a rede virtual tiver sub-redes, a primeira sub-rede será selecionada por padrão. Se houver sub-redes adicionais, selecione a sub-rede desejada.

    f. **Local de armazenamento**. Contas de armazenamento são o local de preparo. Este menu lista as contas de armazenamento no mesmo local que o cofre dos Serviços de Recuperação. Não há suporte para contas de armazenamento com redundância de zona. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre dos Serviços de Recuperação, você deverá criar uma antes de iniciar a operação de restauração. O tipo de replicação da conta de armazenamento é exibido entre parênteses.

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * Uma rede virtual é opcional para uma VM clássica e obrigatória para a VM implementada pelo Resource Manager.

    > * O tipo de armazenamento fornecido na conta de armazenamento (premium ou padrão) no local temporário decide o tipo de armazenamento em disco de restauração. Atualmente, não há suporte para um modo misto de discos durante a restauração.
    >
    >

2. Na folha **Configuração de restauração**, clique em **OK** para finalizar a configuração da restauração. Na folha **Restaurar**, selecione **Restaurar** para disparar a operação de restauração.

## <a name="restore-backed-up-disks"></a>Restaurar discos com backup
Valor do tipo de restauração **Disco de restauração** em **A lâmina da configuração de restauração** permite criar uma VM com configurações personalizadas. Durante a restauração de discos, a conta de armazenamento a ser selecionada deve estar no mesmo local que a segurança de serviços de recuperação. É obrigatório criar uma conta de armazenamento, se não houver contas de armazenamento com o mesmo local que o cofre do Recovery Services. Não há suporte para contas de armazenamento do ZRS. Tipo de replicação da conta de armazenamento é exibido entre parênteses.

Após a operação de restauração, use abaixo:
* [Usar o modelo para personalizar a VM restaurada](#use-templates-to-customize-restore-vm)
* [Usar os discos restaurados para anexar a uma VM existente](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Criar uma nova VM usando o PowerShell de discos restaurados](./backup-azure-vms-automation.md#restore-an-azure-vm)

Na folha **Configuração de restauração**, clique em **OK** para finalizar a configuração da restauração. Na folha **Restaurar**, selecione **Restaurar** para disparar a operação de restauração.

![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

Em **Restauração do Local** está sendo feita através da guia **Replace Existing**.

## <a name="replace-existing-disks-from-a-restore-point"></a>Substituir discos existentes de um ponto de restauração
A opção **Substituir existente** ajuda a substituir os discos existentes na VM atual pelo ponto de restauração selecionado. Esta operação só pode ser executada se a VM atual existir. Se ele foi excluído por qualquer motivo, essa operação não poderá ser executada; Como alternativa, recomendamos que você faça **Criar nova** VM ou discos para continuar com as operações de restauração. Durante a substituição das operações de disco (s) existentes, como medida de precaução, fazemos backup dos dados antes de iniciar as operações de substituição de discos. Se o ponto de restauração tiver discos mais / menos que a VM atual, o número de discos no ponto de restauração refletirá apenas na VM. Opção de substituir existente é atualmente tem suporte para somente os discos gerenciados e não criptografada VMs.  

 No blade **Restore Configuration**, a única entrada que precisa ser selecionada é **Staging Location**.

   ![Restaurar o Assistente de configuração de substituir existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **Tipo de Restauração**. Substitua o (s) disco (s) que representam o ponto de restauração selecionado e substituirá o (s) disco (s) na VM existente.

 b. **Local de preparo**. Contas de armazenamento são o local de preparo dos discos gerenciados. Este menu lista as contas de armazenamento no mesmo local que o cofre dos Serviços de Recuperação. Não há suporte para contas de armazenamento com redundância de zona. Se não houver nenhuma conta de armazenamento no mesmo local que o cofre dos Serviços de Recuperação, você deverá criar uma antes de iniciar a operação de restauração. O tipo de replicação da conta de armazenamento é exibido entre parênteses.

## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração
Após você disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhar a operação de restauração. O serviço de backup também cria e exibe temporariamente a notificação na área de **Notificações** do portal. Se não vir a notificação, selecione o símbolo **Notificações** para exibir suas notificações.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Clique no hiperlink das notificações para acessar a lista **BackupJobs**. Todos os detalhes das operações de um determinado trabalho estão disponíveis nas listas **BackupJobs**. Você pode ir para **BackupJobs** do painel do cofre clicando-se os trabalhos de Backup em bloco, selecione **máquina virtual do Azure** para exibir os trabalhos associados ao cofre.

A folha **Trabalhos de backup** é aberta e exibe a lista de trabalhos.

![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-completed.png)

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
