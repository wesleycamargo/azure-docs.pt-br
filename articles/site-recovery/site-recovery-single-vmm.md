
<properties
	pageTitle="Azure Site Recovery: Replica Máquinas virtuais de Hyper-V (único servidor VMM)"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em nuvens VMM locais para o Azure ou para uma nuvem VMM secundária."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>

#  Azure Site Recovery: Replica Máquinas virtuais de Hyper-V (único servidor VMM)

O serviço do Azure Site Recovery contribui para uma solução de recuperação de desastre e continuidade de negócios (BCDR) robusta ao gerenciar e automatizar a replicação de servidores físicos locais e máquinas virtuais no Azure ou em um datacenter local secundário. Este artigo descreve como implantar a Recuperação de Site para proteger as máquinas virtuais Hyper-V que estão localizadas em uma nuvem VMM, quando você tem apenas um único servidor VMM em sua implantação. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

Você pode replicar máquinas virtuais Hyper-V de duas maneiras:

- Replicar máquinas virtuais do Hyper-V que estão localizadas em hosts Hyper-V que não estão localizados em uma nuvem VMM para o Azure
- Replicar máquinas virtuais do Hyper-V que estão localizadas em hosts Hyper-V em uma nuvem VMM para o Azure
- Replicar máquinas virtuais do Hyper-V que estão localizadas em hosts Hyper-V em uma nuvem VMM para o Azure

Mas o que acontece se você quiser usar o VMM, mas tiver apenas um único servidor VMM em sua infraestrutura?

Nesse caso, você tem duas opções:

- Replicar suas VMs Hyper-V em suas nuvens do VMM no Azure. [Saiba mais](site-recovery-vmm-to-azure.md) sobre esse cenário.
- Replicar entre nuvens em um único servidor VMM

Recomendamos a primeira opção, pois o failover e a recuperação não são perfeitos na segunda opção e são necessárias várias etapas manuais.


### Replicar entre sites com um único servidor VMM autônomo

Neste cenário, você implantará um único servidor VMM autônomo como uma máquina virtual em um site primário e replicará essa máquina virtual para um site secundário com a Recuperação de Site e a Réplica do Hyper-V. Para fazer isso:

1. Configure o VMM em uma VM Hyper-V. Considere a colocação da instância do SQL Server usada pelo VMM na mesma VM. Isso economiza tempo, pois apenas uma VM deverá ser instanciada. Se você deseja usar uma instância remota e ocorrer uma paralisação, você precisará recuperar essa instância antes de recuperar o VMM.
2. O servidor VMM único deve ter pelo menos duas nuvens configuradas. Uma nuvem conterá as máquinas virtuais que deseja replicar e outra nuvem servirá como o local secundário. A nuvem que contém as máquinas virtuais que você deseja proteger deve ter um ou mais grupos de hosts do VMM com um ou mais servidores de host do Hyper-V em cada grupo de hosts e pelo menos uma máquina virtual Hyper-V em cada servidor host.
2. Criar um cofre de Recuperação de Site, gerar e baixar uma chave de registro do cofre e registrar o servidor VMM no cofre.
2. Configurar uma ou mais nuvens na VM do VMM e adicionar os hosts Hyper-V que contêm máquinas virtuais que você deseja proteger a essas nuvens.
3. Em Azure Site Recovery, defina as configurações de proteção para nuvens. Em Local de Origem e de Destino, especifique o nome do servidor VMM único. Se configurar o mapeamento de rede, você mapeará a rede VM para a nuvem que contém máquinas virtuais que você deseja proteger para a rede VM para a nuvem para a qual você deseja replicar.
4. Habilite a replicação para máquinas virtuais que deseja proteger usando **Pela rede** como o método de replicação, pois ambas as nuvens estão localizadas no mesmo servidor.
4. No console do Gerenciador do Hyper-V, habilite a Réplica do Hyper-V no host Hyper-V que contém a VM do VMM e habilite a replicação na VM. Certifique-se de que você não adicione a máquina virtual do VMM às nuvens que são protegidas pela Recuperação de Site, para garantir que as configurações de réplica do Hyper-V não sejam substituídas pela Recuperação de Site.
5. Se você quiser criar planos de recuperação, especifique o mesmo servidor VMM de origem e destino. 

Quando ocorrem paralisações, recupere as cargas de trabalho em VMs Hyper-V da seguinte maneira:

1. Faça o failover manualmente da réplica da VM VMM para o site secundário usando o Gerenciador do Hyper-V com um failover planejado.
2. Depois que a VM VMM foi recuperada, você pode efetuar login no Gerenciador de Recuperação do Hyper-V do site secundário e fazer um failover não planejado das máquinas virtuais do site primário para o site secundário.
3. Após a conclusão do failover não planejado os usuários poderão acessar todos os recursos no site principal.

Observe que a VM do VMM precisa sofrer failover manualmente para o site secundário antes de as cargas de trabalho falharem.

![Servidor VMM virtual autônomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

### Replicar entre sites com um único servidor VMM em um cluster estendido

Em vez de implantar um servidor VMM autônomo como uma máquina virtual que é replicada para um site secundário, você pode tornar o VMM altamente disponível, implantando-o como uma VM em um cluster de failover do Windows, fornecendo resiliência de carga de trabalho e proteção contra falhas de hardware. Para implantar com Recuperação de Site, o VMM deve ser implantado em um cluster alongado em sites geograficamente separados. Para fazer isso:

1. Instale o VMM em uma máquina virtual em um cluster de failover do Windows e selecione a opção para executar o servidor como altamente disponível durante a instalação.
2. A instância do SQL Server usada pelo VMM deve ser replicada com grupos de disponibilidade do AlwaysOn do SQL Server para que haja uma réplica de banco de dados no local secundário. 

Quando ocorrem paralisações do servidor VMM e isso for correspondente ao banco de dados do SQL Server com falha e acessado no site secundário.

![Servidor VMM virtual clusterizado.](./media/site-recovery-single-vmm/single-vmm-cluster.png)




 

<!---HONumber=AcomDC_1203_2015-->