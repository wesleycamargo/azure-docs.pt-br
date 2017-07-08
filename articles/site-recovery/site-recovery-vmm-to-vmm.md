---
title: "Replicar VMs do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como replicar as VMs do Hyper-V em nuvens VMM para um site do VMM secundário usando o portal do Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a2bc32193ad539053984794a411ae7307b8d8532
ms.contentlocale: pt-br
ms.lasthandoff: 03/15/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Replicar máquinas virtuais do Hyper-V em nuvens de VMM para um site de VMM secundário usando o portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-vmm.md)
> * [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Este artigo descreve como replicar máquinas virtuais do Hyper-V locais gerenciadas em nuvens do System Center VMM (Virtual Machine Manager) para um site secundário usando o [Azure Site Recovery](site-recovery-overview.md) no portal do Azure. Saiba mais sobre isso [arquitetura de cenário](site-recovery-components.md#hyper-v-to-a-secondary-site).

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Pré-requisitos

**Pré-requisito** | **Detalhes**
--- | ---
**As tabelas** | Você precisa de uma conta do [Microsoft Azure](http://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.
**VMM local** | Recomendamos que você tenha dois servidores do VMM, uma no site primário e um secundário.<br/><br/> Você pode replicar entre nuvens em um único servidor VMM.<br/><br/> Os servidores VMM devem estar executando pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Servidores VMM precisam de acesso à Internet.
**Nuvens do VMM** | Cada servidor VMM deve ter uma ou mais nuvens, e todas as nuvens devem ter o perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem conter um ou mais grupos de hosts do VMM.<br/><br/> Se você tiver apenas um servidor VMM, ele precisa de pelo menos duas nuvens, para atuar como primário e secundário.
**Hyper-V** | Os servidores Hyper-V devem executar, no mínimo, o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>  Os servidores host Hyper-V devem estar localizados em grupos de hosts nas nuvens VMM primárias e secundárias.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, instale a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver executando o Hyper-V em um cluster no Windows Server 2012, o agente de cluster não será criado automaticamente se você tiver um cluster baseado em endereço IP estático. Configure o agente de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Os servidores do Hyper-V precisam de acesso à Internet.
**URLs** | Servidores do VMM e hosts Hyper-V devem ser capazes de acessar essas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

## <a name="deployment-steps"></a>Etapas de implantação.

Veja o que fazer:

1. Verifique se os pré-requisitos.
2. Prepare o servidor do VMM e os hosts do Hyper-V.
3. Crie um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e orquestra a replicação.
4. Especifique as configurações de origem, destino e replicação.
5. Implantando o serviço de mobilidade em máquinas virtuais que você deseja replicar.
6. Preparar para replicação e habilitar a replicação para máquinas virtuais do Hyper-V.
7. Execute um failover de teste para verificar se tudo está funcionando como esperado.

## <a name="prepare-vmm-servers-and-hyper-v-hosts"></a>Preparar servidores VMM e hosts Hyper-V

Para se preparar para a implantação:

1. Verifique se o servidor do VMM e hosts Hyper-V cumprir os pré-requisitos descritos acima e podem alcançar os URLs necessários.
2. Configure as redes de VMM de modo que você possa configurar o [mapeamento de rede](#network-mapping-overview).

    - Verifique se as VMs do servidor host de origem do Hyper-V estão conectadas a uma rede de VMs do VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
    Verifique se a nuvem secundária que você usará para recuperação tem uma rede VM correspondente configurada. Essa rede de VM deverá ser vinculada a uma rede lógica associada à nuvem secundária.

3. Preparar para uma [implantação de servidor única](#single-vmm-server-deployment), se você quiser replicar máquinas virtuais entre nuvens no mesmo servidor VMM.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gerenciamento** > **Serviços de Recuperação**.
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. [Crie um grupo de recursos](../azure-resource-manager/resource-group-template-deploy-portal.md)ou selecione um existente. Especifique uma região do Azure. Os computadores serão replicados para essa região. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Se você deseja acessar rapidamente o cofre pelo Painel, clique em **Fixar no painel** > **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

O novo cofre é exibido no **Painel**, em **Todos os recursos** e na folha principal **Cofres dos Serviços de Recuperação**.


## <a name="choose-a-protection-goal"></a>Escolher um objetivo de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

2. Clique em **Site Recovery** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.
3. Selecione **Para site de recuperação** e selecione **Sim, com o Hyper-V**.
4. Selecione **Sim** para indicar que você está usando o VMM para gerenciar os hosts do Hyper-V.
5. Selecione **Sim** se você tiver um servidor VMM secundário. Se você estiver implantando a replicação entre nuvens em um único servidor VMM, clique em **Não**. Em seguida, clique em **OK**.

    ![Escolher metas](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Instale o Provedor do Azure Site Recovery nos servidores VMM e descubra e registre os servidores no cofre.

1. Clique em **Etapa 1: Preparar a Infraestrutura** > **Origem**.

    ![Configurar origem](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. Em **Preparar origem**, clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar origem](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. Em **Adicionar Servidor**, verifique se **Servidor System Center VMM** é exibido em **Tipo de servidor** e se o servidor VMM atende aos [pré-requisitos](#prerequisites).
4. Baixe o arquivo de instalação do Provedor do Azure Site Recovery.
5. Baixe a chave do registro. Você precisará dela quando executar a instalação. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. Instale o Provedor do Azure Site Recovery no servidor do VMM de origem. Você não precisa instalar nada explicitamente nos servidores de host do Hyper-V.


### <a name="install-the-azure-site-recovery-provider"></a>Instalar o Provedor de Recuperação do Site do Azure

1. Execute o arquivo de configuração do Provedor em cada servidor VMM. Se o VMM é implantado em um cluster, faça o seguinte na primeira vez que você instala:
    -  Instale o provedor em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre.
    - Em seguida, instale o Provedor nos outros nós. Nós de cluster devem todos executar a mesma versão do provedor.
2. A instalação executa algumas verificações de pré-requisito e solicita permissão para interromper o serviço do VMM. O serviço VMM será reiniciado automaticamente quando a instalação for finalizada. Se estiver instalando em um cluster do VMM, será solicitado que você pare a função de Cluster.
3. No **Microsoft Update**, você pode aceitar que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
4. Em **Instalação**, aceite ou modifique o local de instalação padrão e clique em **Instalar**.

    ![Local de instalação](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. Quando a instalação for concluída, clique em **Registrar** para registrar o servidor no cofre.

    ![Local de instalação](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. Em **Nome do cofre**, verifique o nome do cofre para o qual o servidor será registrado. Clique em *Próximo*.

    ![Registros do servidor](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. Em **Conexão com a Internet**, especifique como o provedor em execução no servidor VMM se conecta ao Azure.

    ![Configurações da Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   - Você pode especificar que o provedor deve se conectar diretamente à internet ou por meio de um proxy.
   - Especifique as configurações de proxy, se necessário.
   - Se você usar um proxy, uma conta RunAs do VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito. As configurações de conta executar como podem ser modificadas no console do VMM > **configurações** > **segurança** > **contas executar como**. Reinicie o serviço VMM para atualizar as alterações.
8. Em **Chave de Registro**, selecione a chave que você baixou no Azure Site Recovery e copiou para o servidor VMM.
9. A configuração de criptografia é usada somente quando você estiver replicando VMs do Hyper-V em nuvens do VMM no Azure. Se estiver replicando para um site secundário, ela não é usada.
10. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Em uma configuração de cluster, especifique o nome de função de cluster do VMM.
11. Em **Sincronizar metadados de nuvem**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não desejar sincronizar todas as nuvens, deixe essa configuração desmarcada e sincronize cada nuvem individualmente nas propriedades da nuvem no console do VMM.
12. Clique em **Avançar** para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido na guia **Servidores VMM** da página **Servidores** no cofre.

    ![Servidor](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. Depois que o servidor estiver disponível no console de Recuperação de Site, em **Origem** > **Preparar origem** selecione o servidor VMM e selecione a nuvem na qual o host Hyper-V está localizado. Em seguida, clique em **OK**.

Você também pode instalar o provedor usando a linha de comando:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione o servidor VMM e a nuvem de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione o servidor VMM de destino que você deseja usar.
2. Nuvens no servidor sincronizadas com a Recuperação de Site serão exibidas. Selecione a nuvem de destino.

   ![Destino](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="set-up-replication-settings"></a>Definir as configurações de replicação

- Quando você cria uma política de replicação, todos os hosts usando a política devem ter o mesmo sistema operacional. Nuvem do VMM pode conter hosts Hyper-V executando versões diferentes do Windows Server, mas nesse caso, você precisa de várias políticas de replicação.
- Você pode executar a replicação inicial offline. [Saiba mais](#prepare-for-initial-offline-replication)

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. Em **Criar e associar política**, especifique um nome de política. Os tipos de origem e de destino devem ser **Hyper-V**.
3. Em **Versão do host do Hyper-V**, selecione qual sistema operacional está em execução no host.
4. Em **Tipo de autenticação** e **Porta de autenticação**, especifique como o tráfego é autenticado entre os servidores host Hyper-V primário e de recuperação. Selecione **Certificado** , a menos que você tenha um ambiente Kerberos em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Você não precisa fazer nada manualmente. Por padrão, as portas 8083 e 8084 (para certificados) serão abertas no Firewall do Windows nos servidores host Hyper-V. Se você selecionar **Kerberos**, um tíquete Kerberos será usado para autenticação mútua dos servidores host. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
5. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
6. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
7. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que contêm instantâneos consistentes com o aplicativo. O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual. Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Se você habilitar instantâneos consistentes com o aplicativo, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.
8. Em **Compactação da transferência de dados**, especifique se os dados replicados transferidos devem ser compactados.
9. Selecione **Excluir VM de réplica** para especificar que a máquina virtual de réplica deverá ser excluída se você desabilitar a proteção para a VM de origem. Se você habilitar essa configuração, quando você desabilitar a proteção para a VM de origem, ela será removida do console de Recuperação de Site, as configurações de Recuperação de Site para o VMM serão removidas do console do VMM e a réplica será excluída.
10. Em **Método de replicação inicial**, se você estiver replicando na rede, especifique se deseja iniciar a replicação inicial ou agendá-la. Para economizar largura de banda de rede, talvez você deseje agendá-la fora de seus horários mais ocupados. Em seguida, clique em **OK**.

     ![Política de replicação](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM. Em **Política de replicação**, clique em **OK**. É possível associar Nuvens do VMM adicionais (e as VMs nelas) a essa política de replicação em **Replicação** > nome da política > **Associar Nuvem do VMM**.

     ![Política de replicação](./media/site-recovery-vmm-to-vmm/policy-associate.png)


### <a name="configure-network-mapping"></a>Configurar o mapeamento de rede

- Saiba mais sobre [mapeamento de rede](#prepare-for-network-mapping) antes de iniciar.
- Verifique se as máquinas virtuais nos servidores VMM estão conectadas a uma rede de VM.


1. Em **Infraestrutura do Site Recovery** > **Mapeamento de Rede** > **Mapeamentos de rede**, clique em **+Mapeamento de Rede**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. Na guia **Adicionar mapeamento de rede**, selecione os servidores VMM de origem e destino. As redes da VM associadas aos servidores VMM são recuperadas.
3. Em **Rede de origem**, selecione a rede que você deseja usar na lista de redes VM associadas ao servidor primário do VMM.
4. Em **Rede de destino**, selecione a rede que você deseja usar no servidor VMM secundário. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Veja o que acontece quando começa o mapeamento de rede:

* As máquinas virtuais de réplica existentes que corresponderem à rede VM de origem serão conectadas às redes da VM de destino.
* As máquinas virtuais que estiverem conectadas à rede VM de origem serão conectadas à rede mapeada de destino após a replicação.
* Se você modificar um mapeamento existente com uma nova rede, as máquinas virtuais de réplica serão conectadas usando as novas configurações.
* Se a rede de destino tiver várias sub-redes, e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

### <a name="configure-storage-mapping"></a>Configure o mapeamento de armazenamento.

Não há suporte para o [mapeamento de armazenamento](#prepare-for-storage-mapping) no portal do Azure. No entanto, ele pode ser habilitado com o PowerShell. [Saiba mais](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Etapa 5: Planejamento de capacidade

Agora que você tem a infraestrutura básica configurada, pense no planejamento de capacidade e confira se precisa de recursos adicionais.

- Baixe e execute o [Planejador de Capacidade do Azure Site Recovery](site-recovery-capacity-planner.md), para coletar informações sobre o ambiente de replicação, inclusive as VMs, discos por VM e armazenamento por disco.
- Depois que você coletou informações de replicação em tempo real, você pode modificar a política de NetQos para controlar a largura de banda de replicação para máquinas virtuais. Leia mais sobre [limitação tráfego de réplica do Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/), no blog de Thomas Maurer. Saiba mais sobre o [cmdlet New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx.).

## <a name="enable-replication"></a>Habilitar a replicação

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. Em **Origem**, selecione o servidor VMM e a nuvem na qual os hosts Hyper-V que você deseja replicar estão localizados. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. Em **Destino**, verifique o servidor VMM secundário e a nuvem.
4. Em **Máquinas virtuais**, selecione as VMs que você deseja proteger na lista.

    ![Habilitar Proteção da Máquina Virtual](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após a conclusão do trabalho **Finalizar Proteção**, a máquina virtual estará pronta para failover.

Observe que:

- Também é possível habilitar a proteção para máquinas virtuais no console do VMM. Clique em **Habilitar Proteção** na barra de ferramentas na guia propriedades > **Azure Site Recovery** da máquina virtual.
- Depois de habilitar a replicação, é possível exibir as propriedades da VM em **Itens Replicados**. No painel **Informações Gerais**, você pode ver informações sobre a política de replicação da VM e seu status. Clique em **Propriedades** para obter mais detalhes.

### <a name="onboard-existing-virtual-machines"></a>Integrar máquinas virtuais existentes
Se você tiver máquinas virtuais existentes no VMM replicadas com a Réplica do Hyper-V, poderá carregá-las na replicação do Azure Site Recovery da seguinte maneira:

1. Verifique se o servidor Hyper-V que hospeda a VM existente está localizado na nuvem primária e se o servidor Hyper-V que hospeda a máquina virtual de réplica está localizado na nuvem secundária.
2. Certifique-se de ter uma política de replicação configurada para a nuvem primária do VMM.
3. Habilite a replicação para a máquina virtual primária. O Azure Site Recovery e o VMM garantem que o mesmo host de réplica e a mesma máquina virtual são detectados, e o Azure Site Recovery reutilizará e restabelecerá a replicação usando as configurações especificadas.

## <a name="test-your-deployment"></a>Testar a implantação

Para testar a implantação, você pode executar um [failover de teste](site-recovery-test-failover-vmm-to-vmm.md) para uma única máquina virtual ou [criar um plano de recuperação](site-recovery-create-recovery-plans.md) que contenha uma ou mais máquinas virtuais.



## <a name="prepare-for-offline-initial-replication"></a>Preparar a replicação inicial offline

Você pode fazer a replicação offline para a cópia inicial de dados. Você pode preparar isso da seguinte maneira:

* No servidor de origem, você especifica um local do caminho do qual será feita a exportação de dados. Atribua Controle Total de permissões de NTFS e de Compartilhamento ao serviço VMM no caminho de exportação. No servidor de destino, você especifica um local do caminho do qual será feita a importação de dados. Atribua as mesmas permissões nesse caminho de importação.
* Se o caminho de importação ou de exportação for compartilhado, atribua a associação de grupo de Administrador, Usuário Avançado, Operador de Impressão ou Operador de Servidor à conta de serviço VMM no computador remoto no qual o caminho compartilhado está localizado.
* Se você estiver usando contas Executar Como para adicionar hosts, nos caminhos de importação e exportação, atribua permissões de leitura e gravação às contas Executar Como no VMM.
* Os compartilhamentos de importação e de exportação não devem estar localizados em um computador usado como um servidor host Hyper-V porque o Hyper-V não dá suporte à configuração de loopback.
* No Active Directory, em cada servidor host Hyper-V que contém máquinas virtuais que deseja proteger, habilite e configure a delegação restrita para confiar nos computadores remotos nos quais os caminhos de importação e de exportação estão localizados, da seguinte maneira:
  1. No controlador de domínio, abra **Usuários e Computadores do Active Directory**.
  2. Na árvore de console, clique em **DomainName** > **Computadores**.
  3. Clique com o botão direito do mouse no nome do servidor host Hyper-V > **Propriedades**.
  4. Na guia **Delegação**, clique em **Confiar no computador para delegação apenas a serviços especificados**.
  5. Clique em **Usar qualquer protocolo de autenticação**.
  6. Clique em **Adicionar** > **Usuários e Computadores**.
  7. Digite o nome do computador que hospeda o caminho de exportação > **OK**. Na lista de serviços disponíveis, mantenha pressionada a tecla CTRL e clique em **cifs** > **OK**. Repita o procedimento para o nome do computador que hospeda o caminho de importação. Repita conforme necessário para servidores host Hyper-V adicionais.



## <a name="prepare-for-network-mapping"></a>Prepare-se para o mapeamento de rede
Mapeamento de rede mapeiam entre redes de VM do VMM nos servidores primário e secundário do VMM para:

* Colocar de maneira ideal VMs de réplica em hosts secundários do Hyper-V após o failover.
* Conecte VMs de réplica às redes de VM apropriadas após o failover.

Observe que:
- O mapeamento de rede pode ser configurado entre redes VM em dois servidores do VMM, ou em um único servidor VMM, se dois locais forem gerenciados pelo mesmo servidor.
- Quando o mapeamento de rede é configurado corretamente e a replicação é habilitada, uma VM no local primário será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada.
- Se as redes tiverem sido configuradas corretamente no VMM, quando você selecionar uma rede VM de destino durante o mapeamento de rede, as nuvens de origem do VMM que usam a rede VM de origem serão exibidas, junto com as redes VM de destino disponíveis nas nuvens de destino usadas para proteção.
- Se a rede de destino tiver várias sub-redes e uma delas tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


A seguir, um exemplo para ilustrar esse mecanismo. Vamos usar uma organização com dois locais, Nova Iorque e Chicago.

| **Localidade** | **Servidor VMM** | **Redes VM** | **Mapeado para** |
| --- | --- | --- | --- |
| Nova Iorque |VMM-NewYork |VMNetwork1-NewYork |Mapeado para VMNetwork1-Chicago |
| VMNetwork2-NewYork |Não mapeado | | |
| Chicago |VMM-Chicago |VMNetwork1-Chicago |Mapeado para VMNetwork1-NewYork |
| VMNetwork2-Chicago |Não mapeado | | |

Neste exemplo:

* Quando uma máquina virtual de réplica é criada para qualquer máquina virtual conectada a VMNetwork1-NewYork, ela é conectada a VMNetwork1-Chicago.
* Quando uma máquina virtual de réplica é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, ele não é conectada a nenhuma rede.

Veja como as nuvens do VMM são configuradas em nosso exemplo de organização e como as redes lógicas são associadas às nuvens.

### <a name="cloud-protection-settings"></a>Configurações de proteção de nuvem
| **Nuvem protegida** | **Protegendo a nuvem** | **Rede lógica (Nova Iorque)** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>ND</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |
| SilverCloud2 |<p>ND</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |

### <a name="logical-and-vm-network-settings"></a>Configurações de rede lógica e de VM
| **Localidade** | **Rede lógica** | **Rede VM associada** |
| --- | --- | --- |
| Nova Iorque |LogicalNetwork1-NewYork |VMNetwork1-NewYork |
| Chicago |LogicalNetwork1-Chicago |VMNetwork1-Chicago |
| LogicalNetwork2Chicago |VMNetwork2-Chicago | |

### <a name="target-networks"></a>Redes de destino
Com base nessas configurações, ao selecionar a rede VM de destino, a tabela a seguir mostra as opções disponíveis.

| **Seleção** | **Nuvem protegida** | **Protegendo a nuvem** | **Rede de destino disponível** |
| --- | --- | --- | --- |
| VMNetwork1-Chicago |SilverCloud1 |SilverCloud2 |Disponível |
| GoldCloud1 |GoldCloud2 |Disponível | |
| VMNetwork2-Chicago |SilverCloud1 |SilverCloud2 |Não disponível |
| GoldCloud1 |GoldCloud2 |Disponível | |


### <a name="failback"></a>Failback
Para ver o que acontece no caso de failback (replicação inversa), vamos supor que a VMNetwork1-NewYork seja mapeada para VMNetwork1-Chicago, com as configurações a seguir.

| **Máquina virtual** | **Conectado à rede VM** |
| --- | --- |
| VM1 |VMNetwork1-Rede |
| VM2 (réplica de VM1) |VMNetwork1-Chicago |

Com essas configurações, vamos analisar o que acontece em alguns cenários possíveis.

| **Cenário** | **Resultado** |
| --- | --- |
| Nenhuma alteração nas propriedades de rede de VM-2 após o failover. |A VM-1 permanece conectada à rede de origem. |
| As propriedades de rede de VM-2 são alteradas após o failover e ela é desconectada. |A VM-1 é desconectada. |
| As propriedades de rede de VM-2 são alteradas após o failover e ela é conectada à VMNetwork2-Chicago. |Se a VMNetwork2-Chicago não estiver mapeada, a VM-1 será desconectada. |
| O mapeamento de rede da VMNetwork1-Chicago é alterado. |A VM-1 será conectada à rede, agora mapeada para VMNetwork1-Chicago. |


## <a name="prepare-for-single-server-deployment"></a>Preparar para a implantação de servidor único


Se você tiver apenas um único servidor VMM, será possível replicar VMs em hosts Hyper-V na nuvem do VMM para o [Azure](site-recovery-vmm-to-azure.md) ou para uma nuvem do VMM secundária. Recomendamos a primeira opção porque replicar entre nuvens não é perfeita. Se você quiser replicar entre nuvens, você pode replicar com um único servidor VMM autônomo ou com um único servidor VMM implantado em um cluster estendido do Windows

### <a name="standalone-vmm-server"></a>Servidor VMM Autônomo

Neste cenário, você implanta um único servidor VMM como uma máquina virtual no site primário e replica essa VM para um site secundário com o Site Recovery e a Réplica do Hyper-V.

1. **Configure o VMM em uma VM Hyper-V**. Sugerimos que você coloque a instância do SQL Server usada pelo VMM na mesma VM. Isso economiza tempo, pois apenas uma VM deverá ser criada. Se você quiser usar uma instância remota do SQL Server e uma paralisação ocorrer, precisará recuperar essa instância antes de recuperar o VMM.
2. **Garanta que o servidor VMM tenha pelo menos duas nuvens configuradas**. Uma nuvem conterá as máquinas virtuais que deseja replicar e outra nuvem servirá como o local secundário. A nuvem que contém as VMs que você deseja proteger deve estar de acordo com os [pré-requisitos](#prerequisites).
3. Configure a Recuperação de Site conforme descrito neste artigo. Crie e registre o servidor VMM em um cofre, configure uma política de replicação e habilite a replicação. Os nomes VMM de origem e de destino será o mesmo. Especifique que a replicação inicial ocorrerá pela rede.
4. Ao configurar o mapeamento de rede, você vai mapear a rede da VM para a nuvem primária para a rede da VM para a nuvem secundária.
5. No console do Gerenciador do Hyper-V, habilite a Réplica do Hyper-V no host Hyper-V que contém a VM do VMM e habilite a replicação na VM. Certifique-se de que você não adicione a máquina virtual do VMM às nuvens que são protegidas pela Recuperação de Site, para garantir que as configurações de réplica do Hyper-V não sejam substituídas pela Recuperação de Site.
6. Se você criar planos de recuperação para failover, usará o mesmo servidor VMM para origem e destino.
7. Uma falha completa, failover e recuperação da seguinte maneira:

   1. Execute um failover não planejado no console do Gerenciador do Hyper-V no site secundário para fazer o failover da VM do VMM primário para o site secundário.
   2. Verifique se que a VM do VMM está funcionando e em execução e no cofre, execute um failover não planejado failover as VMs de nuvens primários para o secundário. Confirmar o failover e selecione um ponto de recuperação alternativo, se necessário.
   3. Após a conclusão do failover não planejado, todos os recursos poderão ser acessados no site principal novamente.
   4. Quando o site primário estiver disponível novamente, no console do Gerenciador do Hyper-V no site secundário, habilite a replicação inversa para a VM do VMM. Isso inicia a replicação para a VM de secundário para primário.
   5. Execute um failover planejado no console do Gerenciador do Hyper-V no site secundário, failover VM VMM para o site primário. Confirme o failover. Em seguida, habilite a replicação inversa, para que a VM VMM está replicando novamente do principal para o secundário.
   6. No cofre de Serviços de Recuperação, habilite a replicação inversa para as máquinas virtuais da carga de trabalho, para iniciar a replicação de secundário para primário.
   7. No cofre de Serviços de Recuperação, execute um failover planejado para realizar failback das máquinas virtuais da carga de trabalho para o site primário. Confirme o failover para concluí-lo. Em seguida, habilite a replicação inversa para iniciar a replicação das máquinas virtuais da carga de trabalho do primário para o secundário.

### <a name="stretched-vmm-cluster"></a>Cluster do VMM ampliado

Em vez de implantar um servidor VMM autônomo como uma máquina virtual que é replicada para um site secundário, você pode tornar o VMM altamente disponível, implantando-o como uma VM em um cluster de failover do Windows. Isso fornece resiliência de carga de trabalho e proteção contra falhas de hardware. Para implantar com Recuperação de Site, a VM de VMM deve ser implantada em um cluster estendido em sites geograficamente separados. Para fazer isso:

1. Instale o VMM em uma máquina virtual em um cluster de failover do Windows e selecione a opção para executar o servidor como altamente disponível durante a instalação.
2. A instância do SQL Server usada pelo VMM deve ser replicada com grupos de disponibilidade AlwaysOn do SQL Server para que haja uma réplica do banco de dados no site secundário.
3. Siga as instruções deste artigo para criar um cofre, registrar o servidor e configurar a proteção. Você precisa registrar cada servidor VMM no cluster no cofre de Serviços de Recuperação. Para fazer isso, você deve instala o provedor em um nó ativo e registrar o servidor VMM. Em seguida, instale o Provedor nos outros nós.
4. Quando ocorre uma interrupção, o servidor VMM e seu banco de dados SQL Server correspondente passam por failover e são acessados por meio do site secundário.



## <a name="prepare-for-storage-mapping"></a>Preparar para realizar mapeamento de armazenamento


Configurar o armazenamento de mapeamento mapeando as classificações de armazenamento em uma fonte e destino servidores VMM para fazer o seguinte:

  * **Identificar o armazenamento de destino para máquinas virtuais de réplica**— um disco VM de origem serão replicadas para o armazenamento especificado por você (compartilhamento SMB ou cluster volumes compartilhados (CSVs)) no local de destino.
  * **Posicionamento das máquinas virtuais de réplica**— o Mapeamento de armazenamento é usado para posicionar de forma ideal as máquinas virtuais de réplica em servidores host Hyper-V. As máquinas virtuais de réplica serão colocadas em hosts que possam acessar as redes VM mapeadas.
  * **Nenhum mapeamento de armazenamento**— se você não configurar o mapeamento de armazenamento, as máquinas virtuais serão replicadas para o local de armazenamento padrão especificado no servidor host Hyper-V associado à máquina virtual de réplica.

Observe que:
- Você pode configurar o mapeamento entre duas nuvens do VMM em um único servidor.
- As classificações de armazenamento devem estar disponíveis para os grupos de host localizados nas nuvens de origem e de destino.
- As classificações não precisam ter o mesmo tipo de armazenamento. Por exemplo, você pode mapear uma classificação de origem que contenha compartilhamentos SMB para uma classificação de destino que contenha CSVs.

### <a name="example"></a>Exemplo
Se as classificações estiverem configuradas corretamente no VMM quando você selecionar os servidores VMM de origem e de destino durante o mapeamento de armazenamento, as classificações de origem e de destino serão exibidas. Veja um exemplo de compartilhamentos de arquivos de armazenamento e classificações para uma organização com duas localizações, Nova Iorque e Chicago.

| **Localidade** | **Servidor VMM** | **Compartilhamento de arquivos (origem)** | **Classificação (origem)** | **Mapeado para** | **Compartilhamento de arquivos (destino)** |
| --- | --- | --- | --- | --- | --- |
| Nova Iorque |VMM_Source |SourceShare1 |GOLD |GOLD_TARGET |TargetShare1 |
| SourceShare2 |SILVER |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONZE |BRONZE_TARGET |TargetShare3 | | |
| Chicago |VMM_Target | |GOLD_TARGET |Não mapeado | |
|  |SILVER_TARGET |Não mapeado | | | |
|  |BRONZE_TARGET |Não mapeado | | | |

Neste exemplo:

* Quando uma máquina virtual de réplica for criada para qualquer máquina virtual no armazenamento GOLD (SourceShare1), ela será replicada para um armazenamento GOLD_TARGET (TargetShare1).
* Quando uma máquina virtual de réplica for criada para qualquer máquina virtual no armazenamento SILVER (SourceShare2), ela será replicada para um armazenamento SILVER_TARGET (TargetShare2) etc.

### <a name="multiple-storage-locations"></a>Vários locais de armazenamento
Se a classificação de destino for atribuída a vários compartilhamentos SMB ou CSV, o local de armazenamento ideal será selecionado automaticamente quando a máquina virtual estiver protegida. Se nenhum armazenamento de destino adequado estiver disponível com a classificação especificada, o local de armazenamento padrão especificado no host Hyper-V será usado para posicionar os discos rígidos virtuais de réplica.

A tabela a seguir mostram como a classificação de armazenamento e os volumes compartilhados do cluster são configurados em nosso exemplo.

| **Localidade** | **Classificação** | **Armazenamento associado** |
| --- | --- | --- |
| Nova Iorque |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| Chicago |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

Esta tabela resume o comportamento ao ativar a proteção para máquinas virtuais (VM1 - VM5) neste ambiente de exemplo.

| **Máquina virtual** | **Armazenamento de origem** | **Classificação de origem** | **Armazenamento de destino mapeado** |
| --- | --- | --- | --- |
| VM1 |C:\ClusterStorage\SourceVolume1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Ambos GOLD_TARGET</p> |
| VM2 |\\FileServer\SourceShare1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Ambos GOLD_TARGET</p> |
| VM3 |C:\ClusterStorage\SourceVolume2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| VM4 |\FileServer\SourceShare2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Ambos SILVER_TARGET</p> |
| VM5 |C:\ClusterStorage\SourceVolume3 |N/D |Nenhum mapeamento; sendo assim, o local de armazenamento padrão do host Hyper-V é usado |



### <a name="data-privacy-overview"></a>Visão geral de privacidade de dados

Esta tabela resume como os dados são armazenados neste cenário:

- - -
| Ação | **Detalhes** | **Dados coletados** | **Uso** | **Obrigatório** |
| --- | --- | --- | --- | --- |
| **Registro** | Você registra um servidor VMM em um cofre de Serviços de Recuperação. Se posteriormente você desejar cancelar o registro de um servidor, poderá fazer isso excluindo as informações do servidor do portal do Azure. | Depois que um servidor VMM é registrado, a Recuperação de Site coleta, processa e transfere os metadados sobre o servidor VMM e os nomes das nuvens do VMM detectados pela Recuperação de Site. | Os dados são usados para identificar e se comunicar com o servidor VMM apropriado e definir as configurações para as nuvens do VMM apropriadas. | Este recurso é necessário. Se você não quiser enviar essas informações à Recuperação de Site, não deverá usar o serviço de Recuperação de Site. |
| **Habilitar a replicação** | O provedor do Azure Site Recovery está instalado no servidor VMM e é o canal de comunicação com o serviço de Recuperação de Site. O Provedor é uma DLL (biblioteca de vínculo dinâmico) hospedada no processo do VMM. Depois que o Provedor é instalado, o recurso "Recuperação do Data Center" é habilitado no console do administrador do VMM. VMs novas e existentes podem habilitar esse recurso habilitar a proteção de uma VM. |Com essa propriedade definida, o Provedor envia o nome e a ID da VM para Recuperação de Site.  A replicação é habilitada pelo Windows Server 2012 ou Réplica do Hyper-V do Windows Server 2012 R2. Os dados da máquina virtual são replicados de um host Hyper-V para outro (normalmente localizado em um data center de "recuperação" diferente). |A Recuperação de Site usa os metadados para preencher as informações da VM no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações, não habilite a proteção de Recuperação de Site para VMs. Observe que todos os dados enviados pelo Provedor à Recuperação de Site são enviados por HTTPS. |
| **Plano de recuperação** | Planos de recuperação ajudam a compilar um plano de orquestração para o data center de recuperação. Você pode definir a ordem na qual as VMs ou um grupo de máquinas virtuais devem ser iniciados no site de recuperação. Você também pode especificar scripts automatizados para execução ou qualquer ação manual a ser executada no momento da recuperação para cada VM. O failover costuma ser disparado no nível do plano de recuperação para a recuperação coordenada. | A Recuperação de Site coleta, processa e transmite metadados para o plano de recuperação, incluindo metadados de máquina virtual e metadados de scripts de automação e anotações de ações manuais. |Os metadados são usados para compilar o plano de recuperação no portal do Azure. |Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à Recuperação de Site, não crie planos de recuperação. |
| **Mapeamento de rede** | Mapeia as informações de rede do data center principal para o data center de recuperação. Quando as VMs são recuperadas no site de recuperação, o mapeamento de rede ajuda a estabelecer a conectividade de rede. |A Recuperação de Site coleta, processa e transmite os metadados das redes lógicas para cada site (primário e datacenter). |Os metadados são usados para preencher as configurações de rede para que você possa mapear as informações de rede. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à Recuperação de Site, não use o mapeamento de rede. |
| **Failover (planejado/não planejado/teste)** | O failover faz o failover de VMs de um datacenter gerenciado por VMM para outro. A ação de failover é disparada manualmente no portal do Azure. |O Provedor no servidor VMM é notificado sobre o evento de failover pela Recuperação de Site e executa uma ação de failover no host Hyper-V por meio de interfaces do VMM. O failover real de uma VM é de um host do Hyper-V para outro e é tratado pelo Windows Server 2012 ou pela Réplica do Hyper-V do Windows Server 2012 R2. O Site Recovery usa as informações enviadas para popular o status das informações de ação de failover no portal do Azure. | Esse recurso é uma parte essencial do serviço e não pode ser desativado. Se você não quiser enviar essas informações à Recuperação de Site, não use o failover. |

## <a name="next-steps"></a>Próximas etapas

Depois de testar a implantação, saiba mais sobre outros tipos de [failover](site-recovery-failover.md)

