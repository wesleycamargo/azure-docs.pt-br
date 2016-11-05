
---
title: 'Azure Site Recovery: replicar máquinas virtuais do Hyper-V em um único servidor VMM | Microsoft Docs'
description: Este artigo descreve como replicar máquinas virtuais do Hyper-V quando você tem apenas um único servidor VMM.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 08/24/2016
ms.author: raynew

---
# Replicar máquinas virtuais do Hyper-V em um único servidor de VMM
Leia este artigo para saber como replicar máquinas virtuais do Hyper-V localizadas em um servidor do host do Hyper-V em uma nuvem VMM quando você tem apenas um único servidor VMM na sua implantação.

O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação. Este artigo contém instruções para configurar a replicação no portal do Azure.

Se você tiver dúvidas após a leitura deste artigo, publique-as na seção de comentários do Disqus no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral
Se você quiser replicar máquinas virtuais do Hyper-V localizadas nos hosts do Hyper-V no VMM e tem apenas um único servidor VMM, você pode [replicar para o Azure](site-recovery-vmm-to-azure.md), ou entre nuvens no servidor VMM único.

Recomendamos replicar para o Azure porque o failover e recuperação não são perfeitas ao replicar entre nuvens e é necessário uma série de etapas manuais. Se você deseja replicar usando apenas o servidor do VMM, você pode fazer o seguinte:

* Replicar com um único servidor VMM autônomo
* Replicar com um único servidor VMM implantado em um cluster estendido do Windows

## Replicar entre sites com um único servidor VMM autônomo
![Servidor VMM virtual autônomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

Neste cenário, você implanta um único servidor VMM como uma máquina virtual no site primário e replica essa VM para um site secundário com o Site Recovery e a Réplica do Hyper-V.

1. Configure o VMM em uma VM Hyper-V. Sugerimos que você instale a instância do SQL Server usada pelo VMM na mesma VM para economizar tempo. Se você quiser usar uma instância remota do SQL Server e uma paralisação ocorrer, você precisa recuperar essa instância antes de recuperar o VMM.
2. Verifique se o servidor VMM tem pelo menos duas nuvens configuradas. Uma nuvem contém as máquinas virtuais que deseja replicar e a outra nuvem serve como o local secundário. A nuvem que contém as VMs que você deseja proteger deve ter:
   
   * Um ou mais grupos de host de VMM que contém um ou mais servidores de host do Hyper-V em cada grupo de hosts.
   * Pelo menos uma máquina virtual do Hyper-V em cada servidor de host do Hyper-V.
3. Criar um cofre de Serviços de Recuperação, gerar e baixar uma chave de registro do cofre e registrar o servidor VMM no cofre. Durante o registro, você instala o Provedor do Azure Site Recovery nos servidores VMM.
4. Configurar uma ou mais nuvens na VM do VMM e adicionar os hosts Hyper-V a essas nuvens.
5. Configure a proteção para as nuvens. Especifique o nome do servidor VMM único como os locais de origem e destino. Para configurar o mapeamento de rede, você mapeia a rede VM para a nuvem com as máquinas virtuais que você deseja proteger para a rede VM para a nuvem para a nuvem de replicação.
6. Habilite a replicação inicial para máquinas virtuais que deseja proteger usando a rede, pois ambas as nuvens estão localizadas no mesmo servidor.
7. No console do Gerenciador do Hyper-V, habilite a Réplica do Hyper-V no host Hyper-V que contém a VM do VMM e habilite a replicação na VM. Verifique se que você não adicionou a VM do VMM para todas as nuvens que estão protegidas pelo Site Recovery. Isso garante que as configurações de réplica do Hyper-V não são substituídas pela Site Recovery.
8. Se você quiser criar planos de recuperação, especifique o mesmo servidor VMM de origem e destino.

Siga as instruções [neste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, registrar o servidor e configurar a proteção.

### O que fazer em uma interrupção
Se ocorrer uma falha completa e você precisar operar do site secundário, faça o seguinte:

1. No console do Gerenciador do Hyper-V no site secundário, execute um failover não planejado para fazer o failover da VM do VMM do site primário para o site secundário.
2. Verifique se a VM do VMM está em execução no site secundário.
3. No cofre de Serviços de Recuperação, execute um failover não planejado para o failover das máquinas virtuais da carga de trabalho das nuvens primárias para as secundárias. Para concluir o failover não planejado das máquinas virtuais, confirmar o failover ou selecionar um ponto de recuperação diferente conforme necessário.
4. Após o failover não planejado ser concluído, os usuários podem acessar recursos de carga de trabalho no site secundário.

Quando o site primário estiver funcionando normalmente novamente, faça o seguinte:

1. No console do Gerenciador do Hyper-V, habilite a replicação inversa para a VM do VMM, para iniciar a replicação do secundário para o primário.
2. No console do Gerenciador do Hyper-V, execute um failover planejado para realizar o failback da VM do VMM para o site primário. Confirme o failover para concluí-lo. Em seguida, habilite a replicação inversa para iniciar a replicação do VMM do primário para o secundário.
3. No cofre de Serviços de Recuperação, habilite a replicação inversa para as máquinas virtuais da carga de trabalho, para iniciar a replicação de secundário para primário.
4. No cofre de Serviços de Recuperação, execute um failover planejado para realizar failback das máquinas virtuais da carga de trabalho para o site primário. Confirme o failover para concluí-lo. Em seguida, habilite a replicação inversa para iniciar a replicação das máquinas virtuais da carga de trabalho do primário para o secundário.

## Replicar entre sites com um único servidor VMM em um cluster estendido
![Servidor VMM virtual clusterizado.](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Em vez de implantar um servidor VMM autônomo como uma máquina virtual que é replicada para um site secundário, você pode tornar o VMM altamente disponível, implantando-o como uma VM em um cluster de failover do Windows. Isso fornece resiliência de carga de trabalho e proteção contra falhas de hardware. Para implantar com Recuperação de Site, a VM de VMM deve ser implantada em um cluster estendido em sites geograficamente separados. Para fazer isso:

1. Instale o VMM em uma máquina virtual em um cluster de failover do Windows e selecione a opção para executar o servidor como altamente disponível durante a instalação.
2. A instância do SQL Server usada pelo VMM deve ser replicada com grupos de disponibilidade do AlwaysOn do SQL Server para que haja uma réplica de banco de dados no site secundário.
3. Siga as instruções [neste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, registrar o servidor e configurar a proteção. Você precisa registrar cada servidor VMM no cluster no cofre de Serviços de Recuperação. Para fazer isso, você deve instala o provedor em um nó ativo e registrar o servidor VMM. Em seguida, instale o Provedor nos outros nós.

### O que fazer em uma interrupção
Quando ocorre uma paralisação, o servidor de VMM e seu banco de dados do SQL Server correspondente passam por failover e são acessado do site secundário.

## Próximas etapas
[Saiba mais](site-recovery-vmm-to-vmm.md) sobre a implantação do Site Recovery detalhada do VMM para replicação do VMM.

<!---HONumber=AcomDC_0824_2016-->