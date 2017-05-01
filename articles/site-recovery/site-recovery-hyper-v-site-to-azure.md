---
title: Replicar VMs Hyper-V para o Azure| Microsoft Docs
description: "Descreve como implantar orquestrar a replicação, o failover e a recuperação de VMs do Hyper-V locais para o Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 2254b06d37b9090e1ca5e4e7db83e35e732e01a3
ms.lasthandoff: 04/06/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Replicar máquinas virtuais Hyper-V (sem VMM) no Azure usando o Azure Site Recovery com o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure clássico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Este artigo descreve como replicar máquinas virtuais Hyper-V locais no Azure usando o [Azure Site Recovery](site-recovery-overview.md) no portal do Azure. Nessa implantação, VMs de Hyper-V não são gerenciadas pelo System Center Virtual Machine Manager (VMM).

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Se você quiser migrar computadores para o Azure (sem failback), saiba mais [neste artigo](site-recovery-migrate-to-azure.md).



## <a name="deployment-steps"></a>Etapas de implantação.

Siga o artigo para concluir estas etapas de implantação:

1. [Saiba mais](site-recovery-components.md#hyper-v-to-azure) sobre a arquitetura dessa implantação. Além disso, [saiba mais sobre](site-recovery-hyper-v-azure-architecture.md) como funciona a replicação do Hyper-V no Site Recovery.
2. Verifique os pré-requisitos e as limitações.
3. Configure contas de rede e armazenamento do Azure.
4. Prepare os hosts do Hyper-V.
5. Crie um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e orquestra a replicação.
6. Especifique as configurações de origem. Crie um site de Hyper-V contendo os hosts Hyper-V e registre o site no cofre. Instale o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação da Microsoft nos hosts do Hyper-V.
7. Defina as configurações de destino e de replicação.
8. Habilite a replicação para as VMs.
9. Execute um failover de teste para verificar se tudo está funcionando como esperado.



## <a name="prerequisites"></a>Pré-requisitos


**Requisito** | **Detalhes** |
--- | --- |
**As tabelas** | Saiba mais sobre os [requisitos do Azure](site-recovery-prereq.md#azure-requirements).
**Servidores locais** | [Saiba mais](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager) sobre os requisitos dos hosts Hyper-V locais.
**VMs do Hyper-V locais** | As VMs que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar de acordo com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs do Azure** | Os hosts Hyper-V precisam de acesso a essas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).



## <a name="prepare-for-deployment"></a>Preparar para a implantação

Para se preparar para a implantação, você precisará:

1. [Configure uma rede do Azure](#set-up-an-azure-network) na qual as VMs do Azure estarão localizadas quando criadas após o failover.
2. [Configure uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para os dados replicados.
3. [Prepare os hosts do Hyper-V](#prepare-the-hyper-v-hosts) para garantir que eles possam acessar as URLs necessárias.

### <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Configure uma rede do Azure. Você precisará dela para que as VMs do Azure criadas após o failover sejam conectadas à rede.

* A rede deve estar na mesma região que o cofre dos Serviços de Recuperação.
* Dependendo do modelo de recurso que deseja usar para as VMs do Azure com failover, você configura uma rede do Azure no [modo Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* É recomendável configurar uma rede antes de começar. Caso você não faça isso, será necessário fazê-lo durante a implantação da Recuperação de Site.
- As contas de armazenamento usadas pelo Site Recovery não podem ser [movidas](../azure-resource-manager/resource-group-move-resources.md) dentro de uma mesma assinatura ou entre assinaturas diferentes.


### <a name="set-up-an-azure-storage-account"></a>Configure uma conta de armazenamento do Azure

- Você precisa de uma conta de armazenamento do Azure standard/premium para conter dados replicados para o Azure. O [Armazenamento Premium](../storage/storage-premium-storage.md) normalmente é usado para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho com uso intensivo de E/S.
- Se você desejar usar uma conta premium para armazenar dados replicados, também precisará de uma conta de armazenamento standard para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.
- Dependendo do modelo de recurso que você deseja usar para as VMs do Azure com failover, você configurará uma conta no [modo Resource Manager](../storage/storage-create-storage-account.md) ou no [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- É recomendável que você configure uma conta de armazenamento antes de começar. Caso você não faça isso, será necessário fazê-lo durante a implantação do Site Recovery. As contas devem estar na mesma região que o cofre dos Serviços de Recuperação.
- Não é possível mover contas de armazenamento usadas pelo Site Recovery entre grupos de recursos na mesma assinatura ou entre assinaturas diferentes.


### <a name="prepare-the-hyper-v-hosts"></a>Prepare os hosts do Hyper-V

* Verifique se os hosts do Hyper-V satisfazem os [pré-requisitos](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager).
- Certifique-se de que os hosts podem acessar as URLs necessárias.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Monitoramento + Gerenciamento** > **Backup e Site Recovery (OMS)**.  

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.

4. [Crie um novo grupo de recursos](../azure-resource-manager/resource-group-template-deploy-portal.md) ou selecione um existente e especifique uma região do Azure. Os computadores serão replicados para essa região. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

5. Se você deseja acessar rapidamente o cofre no Painel, clique em **Fixar no painel** e em **Criar**.

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

O novo cofre é exibido na lista **Painel** > **Todos os recursos** e na folha **Cofres dos Serviços de Recuperação** principal.



## <a name="select-the-protection-goal"></a>Selecionar o objetivo de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Nos **Cofres dos Serviços de Recuperação**, selecione o cofre.
2. Em **Introdução**, clique em **Site Recovery** > **Preparar a infraestrutura** > **Meta de proteção**.

    ![Escolher metas](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. Em **Meta de proteção**, selecione **Para o Azure** e selecione **Sim, com o Hyper-V**. Selecione **Não** para confirmar que você não está usando o VMM. Em seguida, clique em **OK**.

    ![Escolher metas](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configure o site do Hyper-V, instale o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure em hosts do Hyper-V e registre o site no cofre.

1. Em **Preparar a infraestrutura**, clique em **Origem**. Para adicionar um novo site do Hyper-V como um contêiner para seus hosts ou clusters do Hyper-V, clique em **+ Site Hyper-V**.

    ![Configurar origem](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. Em **Criar site do Hyper-V**, especifique um nome para o site. Em seguida, clique em **OK**. Agora, selecione o site que você criou e clique em **+Servidor Hyper-V** para adicionar um servidor ao site.

    ![Configurar origem](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Em **Adicionar Servidor** > **Tipo de servidor**, verifique se **Servidor Hyper-V** é exibido.

    - Certifique-se de que o servidor Hyper-V que você deseja adicionar seja compatível com os [pré-requisitos](#on-premises-prerequisites) e seja capaz de acessar as URLs especificadas.
    - Baixe o arquivo de instalação do Provedor do Azure Site Recovery. Você executa esse arquivo para instalar o Provedor e o agente dos Serviços de Recuperação em cada host do Hyper-V.

    ![Configurar origem](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Instalar o Provedor e o agente

1. Execute o arquivo de configuração do Provedor em cada host que você adicionou ao site do Hyper-V. Se você estiver instalando em um cluster do Hyper-V, execute a instalação em cada nó de cluster. Instalar e registrar cada nó de cluster do Hyper-V garante que as VMs estarão protegidas, mesmo se migrarem entre nós.
2. No **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**.
4. Em **Configurações do Cofre**, clique em **Procurar** para selecionar o arquivo da chave do cofre baixado. Especifique a assinatura do Azure Site Recovery, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.

    ![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. Em **Configurações de Proxy**, especifique como o Provedor em execução nos hosts Hyper-V se conecta ao Azure Site Recovery pela Internet.

    * Se você quiser que o Provedor conecte diretamente, selecione **Conectar diretamente o Azure Site Recovery sem um proxy**.
    * Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão ao Provedor, selecione **Conectar o Azure Site Recovery com um servidor proxy**.
    * Se você usar um proxy:
        - Especifique o endereço, a porta e as credenciais
        - Certifique-se de que as URLs descritas nos [pré-requisitos](#prerequisites) sejam permitidas através do proxy.

    ![internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. Quando a instalação terminar, clique em **Registrar** para registrar o servidor no cofre.

    ![Local de instalação](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. Após a conclusão do registro, os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido em **Infraestrutura do Site Recovery** > **Hosts Hyper-V**.


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Especifique a conta de armazenamento do Azure para a replicação e a rede do Azure à qual as VMs do Azure se conectarão após o failover.

1. Clique em **Preparar a Infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos em que você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs.

3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

    - Se você não tiver uma conta de armazenamento, clique em **+Armazenamento** para criar um conta embutida baseada no Resource Manager. Leia sobre [requisitos de armazenamento](site-recovery-prereq.md#azure-requirements).
    - Se você não tiver uma rede do Azure, clique em **+Rede** para criar um rede embutida baseada no Resource Manager.

    ![Armazenamento](./media/site-recovery-vmware-to-azure/enable-rep3.png)

    


## <a name="configure-replication-settings"></a>Definir configurações de replicação

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

    ![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).

    > [!NOTE]
    > Não há suporte para uma frequência de 30 segundos ao replicar para armazenamento premium. A limitação é determinada pelo número de instantâneos por blob (100) com suporte pelo armazenamento premium. [Saiba mais](../storage/storage-premium-storage.md#snapshots-and-copy-blob).

4. Em **Retenção do ponto de recuperação**, especifique (em horas) qual será a duração da janela de retenção para cada ponto de recuperação. VMs podem ser recuperadas para qualquer ponto em uma janela.
5. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que contêm instantâneos consistentes com o aplicativo.

    - O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual.
    - Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido.
    - Se você habilitar instantâneos consistentes com o aplicativo, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.

6. Em **Hora de início para replicação inicial**, especifique quando a replicação inicial deve começar. A replicação ocorre pela largura de banda da Internet, pois talvez você queira agendá-la fora dos horários de pico. Em seguida, clique em **OK**.

    ![Política de replicação](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando você cria uma nova política, ela é automaticamente associada ao site do Hyper-V. É possível associar um site do Hyper-V (e as VMs nele) a várias políticas de replicação em **Replicação** > nome da política > **Associar Site do Hyper-V**.

## <a name="capacity-planning"></a>Planejamento da capacidade

Agora que você tem a infraestrutura básica configurada, pode pensar sobre o planejamento de capacidade e descobrir se precisa de recursos adicionais.

O Site Recovery fornece um planejador de capacidade para ajudar você a alocar os recursos certos para computação, rede e armazenamento. É possível executar o planejador no modo rápido para estimativas baseadas em um número médio de VMs, de discos e de armazenamento ou no modo detalhado, com números personalizados no nível de carga de trabalho. Antes de começar, é necessário:

* Reunir informações sobre seu ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
* Estimar a taxa de alteração (variação) diária de dados replicados. Você pode usar o [Planejador de Capacidade para Réplica do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para ajudá-lo a fazer isso.

1. Clique em **Baixar** para baixar a ferramenta e execute-a. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2. Quando terminar, selecione **Sim** em **Você executou o Planejador de Capacidade**?

   ![Planejamento da capacidade](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

Saiba mais sobre [controle de largura de banda de rede](#network-bandwidth-considerations)



## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação para VMs conforme demonstrado a seguir:          

1. Clique em **Replicar aplicativo** > **Origem**. Depois de configurar a replicação pela primeira vez, você pode clicar em **+Replicar** para habilitar a replicação para outros computadores.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. Em **Origem**, selecione o site do Hyper-V. Em seguida, clique em **OK**.
3. Em **Destino**, selecione a assinatura de cofre e o modelo de failover que você deseja usar no Azure (gerenciamento de recursos ou clássico) após o failover.
4. Selecione a conta de armazenamento que você deseja usar. Se não tiver uma conta que deseje usar, você poderá [criar uma](#set-up-an-azure-storage-account). Em seguida, clique em **OK**.
5. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover.

    - Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores habilitados para replicação.
    - Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
    - Se não tiver uma rede que deseje usar, você poderá [criar uma](#set-up-an-azure-network). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.

   ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. Em **Propriedades** > **Configurar propriedades**, selecione o sistema operacional para as VMs selecionadas e o disco do sistema operacional.
8. Verifique se o nome da VM do Azure (nome de destino) está em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Por padrão, todos os discos da VM são selecionados para replicação, mas você pode limpar os discos para excluí-los.
    - Talvez você queira excluir discos para reduzir a largura de banda de replicação. Por exemplo, talvez você não queira replicar discos com os dados temporários ou dados que são atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou Microsoft SQL Server tempdb). Para excluir o disco da replicação, você deverá desmarcá-lo.
    - Apenas discos básicos podem ser excluídos. Você não pode excluir discos do sistema operacional.
    - É recomendável que você não exclua discos dinâmicos. O Site Recovery não pode identificar se um disco rígido virtual dentro de uma VM convidada é básico ou dinâmico. Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco protegido dinâmico aparecerá como um disco com falha quando ocorrer o failover da VM e os dados nesse disco não poderão ser acessados.
        - Depois que a replicação estiver habilitada, você não poderá adicionar ou remover discos para replicação. Se desejar adicionar ou excluir um disco, você precisará desabilitar a proteção da VM e habilitá-la novamente.
        - Discos que você criar manualmente no Azure não sofrerão failback. Por exemplo, se você realizar failover de três discos e criar dois discos diretamente na VM do Azure, apenas três discos que sofreram failover sofrerão failback do Azure para o Hyper-V. Você não pode incluir discos criados manualmente em failback ou em replicação inversa do Hyper-V para o Azure.
        - Se você excluir um disco necessário para um aplicativo operar, após o failover no Azure você precisará criá-lo manualmente no Azure para que possa executar o aplicativo replicado. Como alternativa, integre a automação do Azure em um plano de recuperação para criar o disco durante o failover do computador.

10. Clique em **OK** para salvar as alterações. Você pode definir propriedades adicionais posteriormente.

     ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. Em **Configurações de replicação** > **Definir configurações de replicação**, selecione a política de replicação que você deseja aplicar para as VMs protegidas. Em seguida, clique em **OK**. É possível modificar a política de replicação em **Políticas de replicação** > nome da política > **Editar Configurações**. As alterações aplicadas serão usadas para computadores que já estejam replicando e para novas máquinas.


   ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

É possível acompanhar o progresso do trabalho **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

### <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

É recomendável que você verifique as propriedades do computador de origem.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e selecione o computador.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.

    ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. Em **Computação e Rede** > **Propriedades de computação**, você pode especificar o nome da VM do Azure e o tamanho de destino. Modifique o nome para que ele fique em conformidade com os requisitos do Azure, se for necessário. Você também pode exibir e modificar as informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure. Observe o seguinte:

   * Você pode definir o endereço IP de destino. Se você não fornecer um endereço, o computador com failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover falhará. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.
   * O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:

     * Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
     * Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
     * Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.     
     * Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
     * Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede *Padrão* na máquina virtual do Azure.

     ![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. Em **Discos**, é possível ver o sistema operacional e os discos de dados na VM que serão replicados.




## <a name="test-the-deployment"></a>Teste a implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.

### <a name="before-you-start"></a>Antes de começar

 - Se você quiser se conectar às VMs do Azure usando o RDP após o failover, aprenda sobre [preparação para conexão](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para um teste completo, você precisa copiar o Active Directory e o DNS para seu ambiente de teste. [Saiba mais](site-recovery-active-directory.md#test-failover-considerations).

### <a name="run-a-test-failover"></a>Execute um teste de failover

1. Para executar failover de um único computador, em **Itens Replicados**, clique na VM > ícone **+Failover de Teste**.
2. Para executar failover de um plano de recuperação, em **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).
3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.
4. Clique em **OK** para iniciar o failover. É possível acompanhar o progresso clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** no nome do cofre > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.
6. Se você tiver se preparado para conexões após o failover, você deverá poder se conectar à VM do Azure.
7. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em **Observações** , registre e salve todas as observações associadas ao failover de teste. Isso excluirá as máquinas virtuais que foram criadas durante o failover de teste.

Para obter mais detalhes, leia o artigo [Failover de teste para o Azure](site-recovery-test-failover-to-azure.md).



## <a name="monitor-the-deployment"></a>Monitorar a implantação

Monitore as definições de configuração, o status e a integridade para a implantação do Site Recovery:

1. Clique no nome do cofre para acessar o painel **Conceitos básicos** . Neste painel, você pode acompanhar os trabalhos do Site Recovery, o status da replicação, os planos de recuperação, a integridade do servidor e os eventos.  

    ![Conceitos básicos](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. No bloco **Integridade** , você pode monitorar os servidores de site que estejam enfrentando o problema, além dos eventos gerados pelo Site Recovery nas últimas 24 horas. Você pode personalizar os Conceitos básicos para mostrar os blocos e os layouts mais úteis, incluindo o status de outros cofres da Recuperação de Site e do Backup.
3. Você pode gerenciar e monitorar a replicação nos blocos **Itens Replicados**, **Planos de Recuperação** e **Trabalhos de Recuperação de Site**. É possível fazer uma busca detalhada nos trabalhos para obter mais detalhes em **Trabalhos** > **Trabalhos do Site Recovery**.

## <a name="command-line-provider-and-agent-installation"></a>Instalação do provedor e do agente pela linha de comando

O Provedor e o agente do Azure Site Recovery também podem ser instalados usando a linha de comando a seguir. Esse método pode ser usado para instalar o provedor em um Núcleo de Servidor para o Windows Server 2012 R2.

1. Baixar o arquivo de instalação do provedor e a chave de registro em uma pasta. Por exemplo, C:\ASR.
2. Em um prompt de comando com privilégios elevados, execute estes comandos para extrair o instalador do Provedor:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i
4. Em seguida, execute estes comandos para registrar o servidor no cofre:

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
Em que:

* **/Credentials**: parâmetro obrigatório que especifica o local no qual o arquivo da chave de registro está localizado  
* **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no Portal do Azure Site Recovery.
* **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
* **/proxyport** : parâmetro opcional que especifica a porta do servidor proxy.
* **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exige autenticação).
* **/proxyPassword**: parâmetro opcional que especifica a Senha para autenticação com o servidor proxy (se o proxy exige autenticação).


## <a name="network-bandwidth-considerations"></a>Considerações sobre largura de banda de rede
Você pode usar a [ferramenta de planejador de capacidade do Hyper-V](site-recovery-capacity-planner.md) para calcular a largura de banda necessária para a replicação (replicação inicial e depois a delta). Para controlar a quantidade de uso de largura de banda para a replicação, você tem algumas opções:

* **Restringir a largura de banda**: o tráfego do Hyper-V replicado para o Azure passa por um host específico do Hyper-V. Você pode limitar a largura de banda no servidor host.
* **Ajustar largura de banda**: você pode influenciar a largura de banda usada para a replicação usando algumas chaves do Registro.

### <a name="throttle-bandwidth"></a>Limitar largura de banda
1. Abra o snap-in MMC do Backup do Microsoft Azure no servidor host do Hyper-V. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. Na guia **Limitação**, selecione **Habilitar limitação de uso de largura de banda da Internet para operações de backup** e defina os limites do horário comercial e não comercial. Os intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Restringir a largura de banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a limitação. Veja um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.

### <a name="influence-network-bandwidth"></a>Influência da largura de banda de rede
1. No Registro, navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influenciar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM**ou crie a chave caso ela não exista.
   * Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. O máximo é 32. Monitore o tráfego para otimizar o valor.

## <a name="next-steps"></a>Próximas etapas

Depois que a replicação inicial for concluída e você tiver testado a implantação, você poderá invocar failovers conforme a necessidade. [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.

