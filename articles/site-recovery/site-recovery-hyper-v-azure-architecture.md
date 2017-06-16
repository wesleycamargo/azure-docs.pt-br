---
title: "Como funciona a replicação do Hyper-V para o Azure no Site Recovery? | Microsoft Docs"
description: "Este artigo fornece uma visão geral do funcionamento da replicação do Hyper-V no Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-architecture-hyper-v-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.contentlocale: pt-br
ms.lasthandoff: 04/18/2017

---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Como funciona a replicação do Hyper-V para o Azure?

Leia este artigo para compreender a arquitetura e os fluxos de trabalho de replicação do Hyper-V para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Poste comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Replique o seguinte no Azure:
- **Hyper-V com o VMM**: as máquinas virtuais em hosts do Hyper-V local gerenciadas nas nuvens do System Center Virtual MAchine Manager (VMM). Os hosts podem estar executando qualquer [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Replique VMs Hyper-V executando qualquer sistema operacional convidado [com suporte do Hyper-V e do Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- **Hyper-V sem o VMM**: VMs locais localizadas nos hosts Hyper-V que não são gerenciadas em nuvens do VMM. Os hosts podem executar qualquer um dos [sistemas operacionais com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Replique VMs Hyper-V executando qualquer sistema operacional convidado [com suporte do Hyper-V e do Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="architectural-components"></a>Componentes de arquitetura

**Área** | **Componente** | **Detalhes**
--- | --- | ---
**As tabelas** | No Azure, você precisa de uma conta do Microsoft Azure, uma conta de armazenamento do Azure e uma rede do Azure. | O armazenamento e a rede podem ser contas baseadas no Gerenciador de Recursos ou clássicas.<br/><br/> Os dados replicados são armazenados na conta de armazenamento, e VMs do Azure são criadas com os dados replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor VMM** | Hosts Hyper-V localizados em nuvens do VMM | Se os hosts Hyper-V forem gerenciados em nuvens do VMM, registre o servidor VMM no cofre dos Serviços de Recuperação.<br/><br/> No servidor do VMM, instale o Provedor de Site Recovery para orquestrar a replicação com o Azure.<br/><br/> Você precisa de redes lógicas e de VM configuradas para definir o mapeamento de rede. Uma rede de VM deve ser vinculada a uma rede lógica associada à nuvem.
**Host Hyper-V** | Os servidores Hyper-V podem ser implantados com ou sem o servidor do VMM. | Se não houver um servidor VMM, o Provedor de Site Recovery será instalado no host para orquestrar a replicação com o Site Recovery pela internet. Se houver um servidor VMM, o Provedor será instalado nele, e não no host.<br/><br/> O agente dos Serviços de Recuperação é instalado no host para lidar com a replicação de dados.<br/><br/> As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.
**VMs Hyper-V** | Você precisa de uma ou mais VMs no servidor de host Hyper-V. | Nada precisa ser explicitamente instalado em VMs

## <a name="deployment-steps"></a>Etapas de implantação.

1. **Azure**: configure os componentes do Azure. Recomendamos que você configure as contas de armazenamento e rede antes de começar a implantação da Recuperação de Site.
2. **Cofre**: crie um cofre de Serviços de Recuperação para o Site Recovery e defina configurações de cofre, inclusive as configurações de origem e destino, configuração de uma política de replicação e habilitando a replicação.
3. **Origem e destino**:
    - **Hosts do Hyper-V em nuvens do VMM**: como parte da especificação de configurações de origem, você pode baixar e instalar o Provedor do Azure Site Recovery no servidor do VMM e o agente de Serviços de Recuperação do Azure em cada host Hyper-V. A origem será o servidor do VMM. O destino é o Azure.
    - Hosts do Hyper-V sem o VMM: ao especificar as configurações de origem, você baixa e instala o Provedor e o agente em cada host Hyper-V. Durante a implantação você reune os hosts em um site do Hyper-V e especifica este site como a origem. O destino é o Azure.

    ![Replicação do Hyper-V/VMM para o Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Replicação de site do Hyper-V para o Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Política de replicação**: crie uma política de replicação para o site do Hyper-V ou a nuvem do VMM. A política é aplicada a todas as VMs localizadas nos hosts na nuvem ou no site.
5. **Habilitar replicação**: habilite a replicação para máquinas virtuais do Hyper-V. A replicação inicial ocorre de acordo com as configurações de política de replicação. As alterações de dados são acompanhadas, e a replicação de alterações delta para o Azure começa após a replicação inicial terminar. As alterações acompanhadas para um item são mantidas em um arquivo .hrl.
6. **Failover de teste**: execute um failover de teste para verificar se tudo está funcionando conforme o esperado.

Saiba mais sobre a implantação:
- [Introdução à replicação de máquinas virtuais do Hyper-V para o Azure - com VMM](site-recovery-vmm-to-azure.md)
- [Introdução à replicação de máquinas virtuais do Hyper-V para o Azure - sem VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Fluxo de trabalho de replicação do Hyper-V

### <a name="enable-protection"></a>Habilitar proteção

1. Depois de habilitar a proteção para uma máquina virtual do Hyper-V, no portal do Azure ou no local, a opção **Habilitar proteção** é iniciada.
2. O trabalho verifica se o computador está em conformidade com os pré-requisitos antes de invocar [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) para configurar a replicação com as configurações definidas por você.
3. O trabalho é iniciado com a replicação inicial, invocando o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação completa de VM e enviar os discos virtuais da VM no Azure.
4. Você pode monitorar o trabalho na guia **Trabalhos** .
        ![Lista de trabalhos](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Habilitar busca detalhada da proteção](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Replicação inicial

1. Um [Instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada.
2. Discos rígidos virtuais são replicadas individualmente até que eles sejam copiados para o Azure. Ele pode demorar um pouco, dependendo do tamanho da VM e largura de banda de rede. Para otimizar o uso da rede, veja [Como gerenciar o uso de largura de banda de rede da proteção local do Azure](https://support.microsoft.com/kb/3056159).
3. Se houver alterações no disco durante a replicação inicial, o Controlador de Replicação de Réplica do Hyper-V mostrará essas alterações em Logs de Replicação do Hyper-V (.hrl). Esses arquivos estão localizados na mesma pasta dos discos. Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário.
4. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
5. Quando a replicação inicial termina, o instantâneo de VM é excluído. As alterações de disco delta no log são sincronizadas e mescladas para o disco pai.


### <a name="finalize-protection"></a>Finalizar proteção

1. Após a conclusão da replicação inicial, o trabalho **Finalizar proteção na máquina virtual** definirá outras configurações de rede e pós-replicação para que a máquina virtual fique protegida.
    ![Finalizar trabalho de proteção](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover. Aqui você já pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.

### <a name="delta-replication"></a>Replicação delta

1. Após a replicação inicial, a sincronização delta se inicia, de acordo com as configurações de replicação.
2. O Controlador de Replicação de Réplica do Hyper-V rastreia as alterações em um disco rígido virtual como arquivos. hrl. Cada disco configurado para a replicação tem um arquivo .hrl associado. Esses logs são enviados para a conta de armazenamento do cliente após a conclusão da replicação inicial. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log no mesmo diretório.
3. Durante a replicação inicial e delta, você pode monitorar a VM na exibição da VM. [Saiba mais](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Sincronização de replicação

1. Se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, a VM fica marcada para ressincronização. Por exemplo, se os arquivos .hrl alcançarem 50% do tamanho do disco, a VM será marcada para ressincronização.
2.  A ressincronização minimiza a quantidade de dados enviados, calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas os dados de delta. A ressincronização usa um algoritmo de agrupamento de bloco fixo em que os arquivos de origem e destino são divididos em partes fixas. As somas de verificação para cada bloco são geradas e comparadas para determinar quais blocos da origem precisam ser aplicados ao destino.
3. Após a conclusão da ressincronização, a replicação delta normal deverá ser retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente. Por exemplo, se ocorrer uma interrupção da rede ou outra interrupção qualquer, você poderá retomar a ressincronização. Para fazer isso, selecione a máquina virtual no portal > **Ressincronizar**.

    ![Ressincronização manual](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Novas tentativas

Se um erro de replicação ocorrer, haverá uma repetição interna. Ela pode ser classificada em duas categorias:

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Nenhuma nova tentativa é feita. O status da VM será **Crítico** e há necessidade de intervenção do administrador. Exemplos desses erros incluem: cadeia VHD quebrada; Estado inválido para a VM de réplica; Erros de autenticação de rede: erros de autorização; A VM não encontrou erros (para servidores do Hyper-V autônomo)
**Erros recuperáveis** | Novas tentativas ocorrem a cada intervalo da replicação usando de modo exponencial a retirada, o que aumenta o intervalo de repetição desde o início da primeira tentativa por 1, 2, 4, 8 e 10 minutos. Se o erro persistir, repita a operação a cada 30 minutos. Os exemplos incluem: erros da rede; erros de pouco espaço em disco; condições de memória insuficiente |

## <a name="protection-and-recovery-lifecycle"></a>Proteção e ciclo de vida de recuperação

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Próximas etapas

- [Verificar pré-requisitos da implantação](site-recovery-prereq.md)
- Solucionar problemas com:
    - [Monitorar e solucionar problemas de proteção](site-recovery-monitoring-and-troubleshooting.md)
    - [Ajuda do suporte da Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Erros comuns e resoluções](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

