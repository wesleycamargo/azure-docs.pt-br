---
title: Arquitetura de replicação do VMware para o Azure no Azure Site Recovery | Microsoft Docs
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizados durante a replicação de VMs de VMware locais para o Azure com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 61c283c178936c98a9a18509c1b46035e48f8f24
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285263"
---
# <a name="vmware-to-azure-replication-architecture"></a>Arquitetura de replicação de VMware para o Azure

Este artigo descreve a arquitetura e os processos utilizados ao fazer a replicação, o failover e a recuperação de VMs (máquinas virtuais) do VMware entre um site de VMware local e o Azure, utilizando o [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do VMware para o Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Uma assinatura do Azure, uma conta de Armazenamento do Microsoft Azure e uma rede do Azure. | Os dados replicados de VMs locais são colocados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando você faz um failover do local para o Azure. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Máquina do servidor de configuração** | Uma única máquina local. É recomendável executar como uma VM de VMware que possa ser implantada desde um modelo de OVF baixado.<br/><br/> A máquina executa todos os componentes do Site Recovery locais, que incluem o servidor de configuração, servidor de processo e o servidor de destino mestre. | **Servidor de configuração**: coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.<br/><br/> **Servidor de processo**: instalado por padrão no servidor de configuração. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Microsoft Azure. O servidor de processo também instala o Serviço de Mobilidade do Azure Site Recovery nas VMs que você deseja replicar e executa a descoberta automática de máquinas locais. À medida que a implantação cresce, você pode adicionar outros servidores de processo separados para lidar com volumes maiores de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: instalado por padrão no servidor de configuração. Lida com os dados de replicação durante o failback do Azure. Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.
**Servidores VMware** | VMs de VMware são hospedadas em servidores ESXi do vSphere locais. Recomendamos um servidor vCenter para gerenciar os hosts. | Durante a implantação do Site Recovery, você pode adicionar servidores VMware no cofre dos Serviços de Recuperação.
**Computadores replicados** | O Serviço de Mobilidade é instalado em cada VM de VMware que você replicar. | É recomendável permitir a instalação automática do servidor em processo. Como alternativa, você pode instalar o serviço manualmente ou usar um método de implantação automatizado como o System Center Configuration Manager.

**Arquitetura do VMware para o Azure**

![Componentes](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Etapas da configuração

As etapas amplas para configurar o VMware para a recuperação de desastres ou migração do Azure são as seguintes:

1. **Configurar os componentes do Azure**. Você precisa de uma conta do Azure com as permissões corretas, uma conta de armazenamento do Azure, uma rede virtual do Azure e um cofre de Serviços de Recuperação. [Saiba mais](tutorial-prepare-azure.md).
2. **Configurar no local**. Isso inclui configurar uma conta no servidor do VMware para que o Site Recovery possa descobrir automaticamente VMs para replicar, configurar uma conta que possa ser usada para instalar o componente de serviço de Mobilidade nas VMs que você deseja replicar e verificar se os servidores VMware e as VMs estão em conformidade com os pré-requisitos. Você também pode se preparar para se conectar a essas VMs do Azure após o failover. O Site Recovery replica os dados da VM para uma conta de armazenamento do Azure e cria VMs do Azure usando os dados quando você executa um failover para o Azure. [Saiba mais](vmware-azure-tutorial-prepare-on-premises.md).
3. **Configurar a replicação**. Escolha para onde você deseja replicar. Configure o ambiente de replicação de origem pela configuração de uma única VM local do VMware (o servidor de configuração) que executa todos os componentes do Site Recovery locais que você precisa. Após a configuração, registre o computador de servidor de configuração no cofre dos Serviços de Recuperação. Em seguida, selecione as configurações de destino. [Saiba mais](vmware-azure-tutorial.md).
4. **Criar uma política de replicação**. Crie uma política de replicação que especifique como a replicação deve ocorrer. 
    - **Limite de RPO**: essa configuração de monitoramento define que se a replicação não ocorrer dentro do tempo especificado, um alerta (e opcionalmente um email) é emitido. Por exemplo, se você definir o limite RPO para 30 minutos e um problema impedir que a replicação aconteça por 30 minutos, um evento será gerado. Essa configuração não afeta a replicação. A replicação é contínua e os pontos de recuperação são criados a cada poucos minutos
    - **Retenção**: a retenção de ponto de recuperação especifica por quanto tempo os pontos de recuperação devem ser mantidos no Azure. Você pode especificar um valor entre 0 e 24 horas para o armazenamento premium ou até 72 horas para o armazenamento padrão. É possível fazer failover para o ponto de recuperação mais recente ou para um ponto armazenado, se você definir o valor acima de zero. Após a janela de retenção, os pontos de recuperação são limpos.
    - **Instantâneos consistentes em relação a falhas**: por padrão, o Site Recovery usa instantâneos consistentes em relação a falhas e cria pontos de recuperação com eles com uma frequência de poucos minutos. Um ponto de recuperação será consistente em relação a falhas se todos os componentes de dados inter-relacionados forem consistentes em relação à ordem de gravação, como estavam no momento em que o ponto de recuperação foi criado. Para entender melhor, imagine o status dos dados no disco rígido do computador após uma queda de energia ou um evento semelhante. Um ponto de recuperação consistente em relação a falhas geralmente é suficiente se o seu aplicativo for projetado para recuperar de uma falha sem nenhuma inconsistência de dados.
    - **Instantâneos consistentes com o aplicativo**: se esse valor não for zero, o serviço de mobilidade em execução na VM tentará gerar instantâneos consistentes com o sistema de arquivo e pontos de recuperação. O primeiro instantâneo é feito após a conclusão da replicação inicial. Em seguida, os instantâneos são criados com a frequência que você especificar. Um ponto de recuperação será consistente com o aplicativo se, além de ser consistente em relação à ordem de gravação, os aplicativos em execução concluírem todas as operações e liberarem seus buffers para o disco (fechando o aplicativo). Pontos de recuperação consistentes com o aplicativo são recomendados para aplicativos de banco de dados, como SQL, Oracle e Exchange. Se um instantâneo consistente em relação a falhas for suficiente, esse valor pode ser definido como 0.  
    - **Consistência de várias VMs**: opcionalmente, você pode criar um grupo de replicação. Em seguida, quando você habilitar a replicação, você pode reunir as VMs no grupo. As VMs em um grupo de replicação são replicados em conjunto e têm pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando passam por failover. Você deve usar essa opção com cuidado, pois ela pode afetar o desempenho da carga de trabalho, já que instantâneos precisam ser coletados de várias máquinas. Faça isso apenas se as VMs executam a mesma carga de trabalho e precisam ser consistentes e se as VMs têm churns semelhantes. Você pode adicionar até 8 VMs a um grupo. 
5. **Habilitar a replicação de VM**. Por fim, habilite a replicação para as VMs do VMware no local. Se você criou uma conta para instalar o serviço de Mobilidade e especificou que o Site Recovery deve fazer uma instalação por push, o serviço de Mobilidade será instalado em cada VM para a qual você habilitar a replicação. [Saiba mais](vmware-azure-tutorial.md#enable-replication). Se você tiver criado um grupo de replicação para consistência de várias VMs, você pode adicionar VMs a esse grupo.
6. **Failover de teste**. Depois que tudo estiver configurado, você poderá fazer um failover de teste para verificar se as VMs realizam failover para o Azure conforme o esperado. [Saiba mais](tutorial-dr-drill-azure.md).
7. **Failover**. Se você estiver migrando apenas as VMs para o Azure, execute um failover completo para fazer isso. Se você estiver configurando a recuperação de desastres, você poderá executar um failover completo conforme necessário. Para recuperação de desastres completa, após o failover ao Azure, você pode fazer failback para seu site local à medida que e quando ele estiver disponível. [Saiba mais](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Processo de replicação

1. Quando você habilitar a replicação para uma VM, ela começará a replicar de acordo com a política de replicação. 
2. O tráfego é replicado para pontos de extremidade públicos do Armazenamento do Microsoft Azure pela Internet. Como alternativa, você pode usar o Azure ExpressRoute com [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Não há suporte para replicação de tráfego através de uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. Uma cópia inicial dos dados da VM é replicada para o armazenamento do Azure.
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
5. A comunicação ocorre da seguinte maneira:

    - As VMs se comunicam com o servidor de configuração local na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - O servidor de configuração coordena a replicação com o Azure pela porta HTTPS 443 de saída.
    - As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e os envia para o armazenamento do Microsoft Azure pela porta 443 de saída.


**Processo de replicação do VMware para o Azure**

![Processo de replicação](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois que a replicação é configurada e você executou uma simulação de recuperação de desastre (failover de teste) para verificar se tudo está funcionando conforme o esperado, você poderá executar failover e failback conforme necessário.

1. É possível fazer failover de uma única máquina ou criar planos de recuperação para fazer failover de várias VMs ao mesmo tempo. As vantagens de um plano de recuperação em vez de failover de única máquina incluem:
    - Você pode modelar dependências do aplicativo, incluindo todas as VMs entre o aplicativo em um único plano de recuperação.
    - Você pode adicionar scripts, runbooks do Azure e pausar para ações manuais.
2. Depois de disparar o failover inicial, você confirma-o para começar a acessar a carga de trabalho da VM do Azure.
3. Quando o site primário local estiver disponível novamente, você poderá se preparar executar o failback. Para executar o failback, você precisa configurar uma infraestrutura de failback, incluindo:

    * **Servidor em processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor em processo, a fim de manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
    * **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou ExpressRoute) da rede do Azure para o site local.
    * **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local manipula o failback. Se for necessário fazer failback de grandes volumes de tráfego, configure um servidor de destino mestre local separado para essa finalidade.
    * **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Essa política foi criada automaticamente quando você criou sua política de replicação do local para o Azure.
4. Depois que os componentes estão em vigor, o failback ocorre em três ações:

    - Estágio 1: proteja novamente as VMs do Azure para que elas comecem a replicação para as VMs de VMware locais.
    -  Estágio 2: execute um failover para o site local.
    - Etapa 3: depois que as cargas de trabalho fizeram failback, você reabilitará a replicação para as VMs locais.
    
 
**Failback de VMware do Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Próximas etapas

Siga [este tutorial](vmware-azure-tutorial.md) para habilitar a replicação de VMware para o Azure.
