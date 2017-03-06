---
title: "Como funciona a Recuperação de Site? | Microsoft Docs"
description: "Este artigo fornece uma visão geral da arquitetura de Recuperação de Site"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 080dce21c2c803fc05c945cdadb1edd55bd7fe1c
ms.openlocfilehash: 4993a873742db5ca2bd8c31eaab098beb0a0a030
ms.lasthandoff: 02/22/2017


---
# <a name="how-does-azure-site-recovery-work"></a>Como funciona o Azure Site Recovery?

Leia este artigo para compreender a arquitetura subjacente do serviço Azure Site Recovery e os componentes que permitem o seu funcionamento.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md)

Este artigo descreve a implantação no [portal do Azure](https://portal.azure.com). O [portal clássico do Azure](https://manage.windowsazure.com/) pode ser usado para manter os cofres de Recuperação de Site existentes, mas não é possível criar novos cofres.

Poste comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="deployment-scenarios"></a>Cenários de implantação

A Recuperação de Site pode ser implantada para orquestrar a replicação em vários cenários:

- **Replicar máquinas virtuais VMware**: você pode replicar máquinas virtuais VMware locais no Azure ou em um datacenter secundário.
- **Replicar computadores físicos**: você pode replicar computadores físicos (Windows ou Linux) no Azure ou em um datacenter secundário. O processo de replicação de máquinas físicas é quase o mesmo que a replicação de VMs VMware.
- **Replicar VMs do Hyper-V**: você pode replicar VMs do Hyper-V para o Azure ou para um site secundário do VMM. Se você quiser replicá-las para um site secundário, elas deverão ser gerenciadas nas nuvens do VMM (Virtual Machine Manager) do System Center.
- **Migrar VMs**: além de replicar (replicação, failover e failback) VMs locais e servidores físicos no Azure, você também pode migrá-los para VMs do Azure (replicação, failover, nenhum failback). Aqui está o que você pode migrar:
    - Migre cargas de trabalho em execução em VMs do Hyper-V locais, VMs VMware e servidores físicos para execução em VMs do Azure.
    - Migre [VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões do Azure. Atualmente, apenas a migração tem suporte nesse cenário, o que significa que não há suporte para failback.
    - Migre [instâncias do Windows AWS](site-recovery-migrate-aws-to-azure.md) para VMs do IaaS do Azure. Atualmente, apenas a migração tem suporte nesse cenário, o que significa que não há suporte para failback.

A Recuperação de Site replica os aplicativos em execução em servidores físicos e VMs com suporte. Você pode obter um resumo completo dos aplicativos com suporte em [Quais cargas de trabalho o Azure Site Recovery pode proteger?](site-recovery-workload.md)

## <a name="replicate-vmware-vmsphysical-servers-to-azure"></a>Replicar VMs VMware/servidores físicos para o Azure

### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---
**As tabelas** | No Azure, você precisa de uma conta do Microsoft Azure, uma conta de armazenamento do Azure e uma rede do Azure.<br/><br/> O armazenamento e a rede podem ser contas do Gerenciador de Recursos ou contas clássicas.<br/><br/>  Os dados replicados são armazenados na conta de armazenamento, e VMs do Azure são criadas com os dados replicados quando ocorre failover do site local. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Você configura um servidor de configuração local para coordenar a comunicação entre o site local e o Azure e para gerenciar a replicação de dados.
**Servidor de processo** | Instalado por padrão no servidor de configuração local.<br/><br/> Atua como um gateway de replicação. Ele recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e envia os dados para o armazenamento do Azure.<br/><br/> Lida com a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de VMs VMware.<br/><br/> À medida que a implantação cresce, você pode adicionar outros servidores de processo dedicados separados para lidar com o aumento do volume de tráfego de replicação.
**Servidor de destino mestre** | Instalado por padrão no servidor de configuração local.<br/><br/> Lida com os dados de replicação durante o failback do Azure. Se os volumes de tráfego de failback forem altos, você poderá implantar um servidor de destino mestre separado para failback.
**Servidores VMware** | Você adiciona servidores vCenter e vSphere a seu cofre de Serviços de Recuperação para replicar VMs VMware.<br/><br/> Se replicar servidores físicos, você precisará de uma infraestrutura de VMware local para failback. Você não pode realizar o failback para um computador físico.
**Computadores replicados** | O serviço de mobilidade deve ser instalado em cada computador que você deseja replicar. Ele pode ser instalado manualmente em cada computador ou com uma instalação por push do servidor de processo.

**Figura 1: VMware para componentes do Azure**

![Componentes](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Processo de replicação

1. Você configura a implantação, incluindo os componentes do Azure e um cofre de Serviços de Recuperação. No cofre, você especifica a origem da replicação e o destino, configura o servidor de configuração, adiciona servidores VMware, cria uma política de replicação, implanta o serviço de mobilidade, habilita a replicação e executa um teste de failover.
2.  As máquinas iniciam a replicação de acordo com a política de replicação, e uma cópia inicial dos dados é replicada para o armazenamento do Azure.
4. A replicação de alterações delta para o Azure começa após a replicação inicial terminar. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
    - As máquinas que estão sendo replicadas se comunicam com o servidor de configuração na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - As máquinas que estão sendo replicadas enviam dados de replicação para o servidor de processo na porta 9443 HTTPS de entrada (pode ser configurada).
    - O servidor de configuração coordena o gerenciamento de replicação com o Azure pela porta HTTPS 443 de saída.
    - O servidor de processo recebe dados de máquinas de origem, otimiza-os e criptografa-os e os envia para o armazenamento do Azure pela porta 443 de saída.
    - Se você habilitar a consistência de várias VMS, as máquinas no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Isso é útil se os computadores estão executando a mesma carga de trabalho e precisam ser consistentes.
5. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering) do Azure ExpressRoute. Não há suporte para a replicação do tráfego através de uma VPN de site a site de um site local para o Azure.

**Figura 2: replicação do VMware para o Azure**

![Avançado](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você executa failovers não planejados de servidores físicos e VMs do VMware locais para o Azure. Não há suporte para failover planejado.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
3. Ao executar um failover, VMs de réplica são criadas no Azure. Você confirma um failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
4. Quando o site primário local estiver disponível novamente, você poderá executar o failback. Você configura uma infraestrutura de failback, inicia a replicação da máquina do site secundário para o primário e executa um failover não planejado do site secundário. Depois que você confirmar esse failover, os dados estarão novamente no local e será necessário habilitar a replicação no Azure de novo. [Saiba mais](site-recovery-failback-azure-to-vmware.md)

Há alguns requisitos de failback:

- **Não há suporte para failback físico para físico**: isso significa que, se você realizar o failover de servidores físicos para o Azure e, depois, quiser realizar o failback, deverá realizar o failback para uma VM VMware. Você não pode realizar o failback para um servidor físico. Você precisará de uma VM do Azure para fazer o failback, e se você não tiver implantado o servidor de configuração como uma VM VMware, será necessário configurar um servidor de destino mestre separado como uma VM VMware. Isso é necessário porque o servidor de destino mestre interage e anexa no armazenamento VMware para restaurar os discos para uma VM VMware.
- **Servidor de processo temporário no Azure**: se você deseja fazer failback do Azure após o failover, precisa criar uma VM do Azure configurada como um servidor de processo para lidar com a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
- **Conexão de VPN**: em caso de failback, você precisará de uma conexão de VPN (ou Rota Expressa do Azure) configurada da rede do Azure para o site local.
- **Servidor de destino mestre separado local**: o servidor de destino mestre local lida com o failback. O servidor de destino mestre está instalado por padrão no servidor de gerenciamento, mas se você estiver fazendo failback de volumes de tráfego maiores, deverá configurar um servidor de destino mestre separado local com essa finalidade.
- **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Isso é criado automaticamente quando você cria sua política de replicação.

**Figura 3: failback físico/de VMware**

![Failback](./media/site-recovery-components/enhanced-failback.png)


## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replicar VMs VMware/servidores físicos para um site secundário

### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---
**As tabelas** | Implante este cenário usando o InMage Scout. Para obtê-lo, você precisará de uma assinatura do Azure.<br/><br/> Depois de criar um cofre de Serviços de Recuperação, baixe o InMage Scout e instale as atualizações mais recentes para configurar a implantação.
**Servidor de processo** | Você implanta o componente do servidor de processo em seu site primário para lidar com o armazenamento em cache, a compactação e a otimização de dados.<br/><br/> Ele também trata da instalação por push do Agente Unificado nas máquinas que você deseja proteger.
**Servidor VMware ESX/ESXi e vCenter** |  Você precisa de uma infraestrutura do VMware para replicar VMs VMware.
**VMs/servidores físicos** |  Você instala o Agente Unificado em VMs VMware ou servidores físicos Windows/Linux que deseja replicar.<br/><br/> O agente atua como um provedor de comunicação entre todos os componentes.
**Servidor de configuração** | O servidor de configuração é instalado no site secundário a fim de gerenciar, configurar e monitorar sua implantação, usando o site de gerenciamento ou o console do vContinuum.
**Servidor vContinuum** | Instalado no mesmo local que o servidor de configuração.<br/><br/> Ele fornece um console para o gerenciamento e monitoramento de seu ambiente protegido.
**Servidor de destino mestre (site secundário)** | O servidor de destino mestre armazena os dados replicados. Ele recebe os dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção dos dados.<br/><br/> O número de servidores de destino mestre necessário depende do número de máquinas que você está protegendo.<br/><br/> Se você quiser realizar o failback no site primário, também precisará de um servidor de destino mestre nesse local. O Agente Unificado está instalado neste servidor.

### <a name="replication-process"></a>Processo de replicação

1. Configure os servidores de componente em cada site (configuração, processo, destino mestre) e instale o Agente Unificado nos computadores que você deseja replicar.
2. Após a replicação inicial, os agentes em cada computador enviam as alterações de replicação delta para o servidor de processo.
3. O servidor de processo otimiza os dados e os transfere para o servidor de destino mestre no site secundário. O servidor de configuração gerencia o processo de replicação.

**Figura 4: replicação do VMware para o VMware**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="replicate-hyper-v-vms-to-azure"></a>Replicar VMs do Hyper-V para o Azure


### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---

**Azure** | No Azure, você precisa de uma conta do Microsoft Azure, uma conta de armazenamento do Azure e uma rede do Azure.<br/><br/> O armazenamento e a rede podem ser contas baseadas no Gerenciador de Recursos ou clássicas.<br/><br/> Os dados replicados são armazenados na conta de armazenamento, e VMs do Azure são criadas com os dados replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor VMM** | Se seus hosts Hyper-V estiverem localizados nas nuvens do VMM, você precisará de redes lógicas e de VMs configuradas para definir o mapeamento de rede. Uma rede de VM deve ser vinculada a uma rede lógica associada à nuvem.
**Host do Hyper-V** | Você precisa de um ou mais servidores host do Hyper-V.
**VMs do Hyper-V** | Você precisa de uma ou mais VMs no servidor de host do Hyper-V. O provedor em execução no host Hyper-V coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. O agente manipula dados de replicação de dados através de HTTPS 443. As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.


## <a name="replication-process"></a>Processo de replicação

1. Você configura os componentes do Azure. Recomendamos que você configure as contas de armazenamento e rede antes de começar a implantação da Recuperação de Site.
2. Crie um cofre de Serviços de Replicação para Recuperação de Site e defina configurações de cofre, incluindo:
    - Se não estiver gerenciando hosts Hyper-V em uma nuvem VMM, você criará um contêiner de site do Hyper-V e adicionará hosts Hyper-V a ele.
    - A origem e o destino de replicação. Se seus hosts Hyper-V são gerenciados no VMM, a origem é a nuvem do VMM. Caso contrário, a origem será o site do Hyper-V.
    - Instalação do provedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. Se você tiver o VMM, o provedor será instalado nele, e o agente em cada host Hyper-V. Se você não tiver o VMM, o Provedor e o agente serão instalados em cada host.
    - Você cria uma política de replicação para o site do Hyper-V ou a nuvem do VMM. A política é aplicada a todas as VMs localizadas nos hosts na nuvem ou no site.
    - Você habilita a replicação para VMs do Hyper-V. A replicação inicial ocorre de acordo com as configurações de política de replicação.
4. As alterações de dados são acompanhadas, e a replicação de alterações delta para o Azure começa após a replicação inicial terminar. As alterações acompanhadas para um item são mantidas em um arquivo .hrl.
5. Você executa um failover de teste para verificar se tudo está funcionando.

### <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode executar um [failover](site-recovery-failover.md) planejado ou não planejado de VMs Hyper-V locais para o Azure. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
4. Após executar o failover, você deve ser capaz de ver as VMs de réplica criadas no Azure. Você pode atribuir um endereço IP público à VM, se necessário.
5. Você confirma então o failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
6. Quando o site primário local estiver disponível novamente, você poderá executar o failback. Você dispara um failover planejado do Azure para o site primário. Para um failover planejado, você pode selecionar failback para a mesma VM ou um local alternativo e sincronizar alterações entre o Azure e o local, para garantir que não haja perda de dados. Quando as VMs são criadas no local, você confirma o failover.

**Figura 5: replicação de site de Hyper-V para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figura 6: replicação de Hyper-V em nuvens do VMM para o Azure**

![Componentes](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)



## <a name="replicate-hyper-v-vms-to-a-secondary-site"></a>Replicar máquinas virtuais do Hyper-V para um site secundário

### <a name="components"></a>Componentes

**Componente** | **Detalhes**
--- | ---
**Conta do Azure** | Você precisa de uma conta do Microsoft Azure.
**Servidor VMM** | Recomendamos um servidor VMM no site primário e um no site secundário, conectado à Internet.<br/><br/> Cada servidor deve ter pelo menos uma nuvem privada de VMM, com o conjunto de perfis de funcionalidade do Hyper-V.<br/><br/> Instale o Provedor do Azure Site Recovery no servidor do VMM. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. As comunicações entre o Provedor e o Azure são protegidas e criptografadas.
**Servidor Hyper-V** |  Você precisa de um ou mais servidores de host do Hyper-V nas nuvens do VMM primárias e secundárias. Os servidores devem estar conectados à Internet.<br/><br/> Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou autenticação de certificado.  
**Computadores de origem** | O servidor host Hyper-V de origem deve ter pelo menos uma VM que você deseja replicar.

## <a name="replication-process"></a>Processo de replicação

1. Você configura a conta do Azure.
2. Crie um cofre de Serviços de Replicação para Recuperação de Site e defina configurações de cofre, incluindo:

    - Origem e destino de replicação (sites primários e secundários).
    - Instalação do provedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. O provedor está instalado nos servidores do VMM, e o agente em cada host Hyper-V.
    - Você cria uma política de replicação para a nuvem do VMM de origem. A política é aplicada a todas as VMs localizadas nos hosts na nuvem.
    - Você habilita a replicação para VMs do Hyper-V. A replicação inicial ocorre de acordo com as configurações de política de replicação.
4. As alterações de dados são acompanhadas, e a replicação de alterações delta começa após a replicação inicial terminar. As alterações acompanhadas para um item são mantidas em um arquivo .hrl.
5. Você executa um failover de teste para verificar se tudo está funcionando.

**Figura 7: replicação de VMM para VMM**

![Local para o próprio local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode executar um [failover](site-recovery-failover.md) planejado ou não planejado entre sites locais. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
4. Se você executar um failover não planejado para um site secundário, depois as máquinas de failover no local secundário não serão habilitadas para replicação ou proteção. Depois de executar um failover planejado, os computadores no local secundário são protegidos.
5. Em seguida, você confirma o failover para começar a acessar a carga de trabalho na VM de réplica.
6. Quando seu site primário estiver disponível novamente, você poderá iniciar a replicação inversa para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais em um estado protegido, mas o datacenter secundário permanece sendo o local ativo.
7. Para transformar o site primário em local ativo novamente, inicie um failover planejado do site secundário para o primário, seguido por outra replicação inversa.


### <a name="hyper-v-replication-workflow"></a>Fluxo de trabalho de replicação do Hyper-V

**Estágio de fluxo de trabalho** | **Ação**
--- | ---
1. **Habilitar proteção** | Depois que você habilita a proteção para uma VM Hyper-V, o trabalho **Habilitar Proteção** é iniciado, para verificar se o computador está em conformidade com os pré-requisitos. O trabalho invoca dois métodos:<br/><br/> [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) para configurar a replicação com as configurações que você definiu.<br/><br/> [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação completa de VM.
2. **Replicação inicial** |  É tirado um instantâneo da máquina virtual, e os discos rígidos virtuais são replicados individualmente até que sejam todos copiados para o local secundário.<br/><br/> O tempo de conclusão necessário depende do tamanho da VM, da largura de banda de rede e do método de replicação inicial.<br/><br/> Se houver alterações no disco durante a replicação, o Controlador de Replicação de Réplica do Hyper-V mostrará essas alterações em Logs de Replicação do Hyper-V (.hrl) localizados na mesma pasta que os discos.<br/><br/> Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário.<br/><br/> O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial. Após a conclusão da replicação inicial, o instantâneo da VM é excluído e as alterações de disco delta no log são sincronizadas e mescladas.
3. **Finalizar proteção** | Após a conclusão da replicação inicial, o trabalho **Finalizar proteção** definirá outras configurações de rede e pós-replicação para que a máquina virtual fique protegida.<br/><br/> Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover.<br/><br/> Aqui você já pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.
4. **Replicação** | Após a replicação inicial, a sincronização delta se inicia, de acordo com as configurações de replicação.<br/><br/> **Falha de replicação**: se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, ocorre a ressincronização. Por exemplo, se os arquivos .hrl alcançarem 50% do tamanho do disco, a VM será marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados, calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas o delta. Após a conclusão da ressincronização, a replicação delta será retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente.<br/><br/> **Erro de replicação**: se um erro de replicação ocorrer, haverá uma repetição interna. Se for um erro sem recuperação, por exemplo, um erro de autenticação ou de autorização, ou se uma máquina réplica estiver em um estado inválida, nenhuma repetição ocorrerá. Se for um erro recuperável, por exemplo, um erro de rede ou de espaço em disco/memória baixo(a), uma nova tentativa será realizada em intervalos crescentes (a cada 1, 2, 4, 8, 10 e 30 minutos).
5. **Failover planejado/não planejado** | Você pode executar failovers planejados ou não planejados, conforme necessário.<br/><br/> Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.<br/><br/> Após a criação das VMs de réplica, elas entram em um estado de confirmação pendente. Você precisa confirmar para concluir o failover.<br/><br/> Depois que o site primário estiver em execução, você poderá executar o failback para o site primário, quando disponível.


**Figura 8: fluxo de trabalho do Hyper-V**

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Próximas etapas

[Verificar pré-requisitos](site-recovery-prereq.md)

