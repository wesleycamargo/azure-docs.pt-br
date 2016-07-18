
<properties
	pageTitle="Azure Site Recovery: replicar máquinas virtuais do Hyper-V em um único servidor VMM | Microsoft Azure"
	description="Este artigo descreve como replicar máquinas virtuais do Hyper-V quando você tem apenas um único servidor VMM."
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
	ms.date="07/06/2016"
	ms.author="raynew"/>

#  Replicar máquinas virtuais do Hyper-V em um único servidor de VMM

Leia este artigo para saber como replicar máquinas virtuais do Hyper-V localizadas em um servidor do host do Hyper-V em uma nuvem VMM quando você tem apenas um único servidor VMM na sua implantação.

Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

Você pode replicar VMs do Hyper-V localizadas em hosts do Hyper-V em nuvens do VMM de duas maneiras:

- Replicar para o Azure.
- Replicar para um site do VMM secundário

Porém, o que acontecerá se você desejar replicar para um local de VMM secundário, mas tiver apenas um único servidor VMM em sua implantação?

Nesse cenário, você tem duas opções:

- Replicar VMs do Hyper-V em nuvens VMM para o Azure. [Saiba mais](site-recovery-vmm-to-azure.md) sobre esse cenário.
- Replicar entre nuvens em um único servidor VMM.

Recomendamos a primeira opção, pois o failover e a recuperação não são perfeitos na segunda opção e várias etapas manuais são necessárias. Se você quiser replicar entre sites em vez do Azure, você terá duas opções.


## Replicar entre sites com um único servidor VMM autônomo

Para replicar entre sites neste cenário, você implantará um único servidor VMM como uma máquina virtual no site primário e replicará essa VM para um site secundário com a Recuperação de Site e a Réplica do Hyper-V. Para fazer isso:

1. Configure o VMM em uma VM Hyper-V. Quando você fizer isso, sugerimos colocar a instância do SQL Server usada pelo VMM na mesma VM. Isso economiza tempo, pois apenas uma VM deverá ser instanciada. Se você quiser usar uma instância remota do SQL Server e uma paralisação ocorrer, precisará recuperar essa instância antes de recuperar o VMM.
2. Verifique se o servidor VMM tem pelo menos duas nuvens configuradas. Uma nuvem conterá as máquinas virtuais que deseja replicar e outra nuvem servirá como o local secundário. A nuvem que contém as VMs que você deseja proteger deve ter:

	- Um ou mais grupos de host de VMM que contém um ou mais servidores de host do Hyper-V em cada grupo de hosts.
	- Pelo menos uma máquina virtual do Hyper-V em cada servidor de host do Hyper-V.
3. Criar um cofre de Recuperação de Site, gerar e baixar uma chave de registro do cofre e registrar o servidor VMM no cofre. Durante o registro, você instalará o Provedor do Azure Site Recovery nos servidores VMM.
4. Configurar uma ou mais nuvens na VM do VMM e adicionar os hosts Hyper-V que contêm máquinas virtuais que você deseja proteger a essas nuvens.
3. Em Azure Site Recovery, defina as configurações de proteção para nuvens. Em Local de Origem e de Destino, especifique o nome do servidor VMM único. Se configurar o mapeamento de rede, você mapeará a rede VM para a nuvem que contém máquinas virtuais que você deseja proteger para a rede VM para a nuvem para a qual você deseja replicar.
4. Habilite a replicação para máquinas virtuais que deseja proteger usando **Pela rede** como o método de replicação, pois ambas as nuvens estão localizadas no mesmo servidor.
4. No console do Gerenciador do Hyper-V, habilite a Réplica do Hyper-V no host Hyper-V que contém a VM do VMM e habilite a replicação na VM. Certifique-se de que você não adicione a máquina virtual do VMM às nuvens que são protegidas pela Recuperação de Site, para garantir que as configurações de réplica do Hyper-V não sejam substituídas pela Recuperação de Site.
5. Se você quiser criar planos de recuperação, especifique o mesmo servidor VMM de origem e destino.

Siga as instruções [neste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, obter uma chave, registrar o servidor e a configurar a proteção.

### Após uma paralisação

Quando paralisações ocorrem, você recuperará as cargas de trabalho em VMs do Hyper-V da seguinte maneira:

1. Faça o failover da réplica da VM do VMM manualmente para o site secundário usando o Gerenciador do Hyper-V com um failover planejado.
2. Depois que a VM do VMM for recuperada, você pode entrar no Gerenciador de Recuperação do Hyper-V do site secundário e fazer um failover não planejado das VMs do site secundário para o site primário. Observe que a VM do VMM precisa passar por um failover manualmente para o site secundário para que as VMs de carga de trabalho possam passar por failover.
3. Após a conclusão do failover não planejado, todos os recursos poderão ser acessados no site principal novamente.


![Servidor VMM virtual autônomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

## Replicar entre sites com um único servidor VMM em um cluster estendido

Em vez de implantar um servidor VMM autônomo como uma máquina virtual que é replicada para um site secundário, você pode tornar o VMM altamente disponível, implantando-o como uma VM em um cluster de failover do Windows. Isso fornece resiliência de carga de trabalho e proteção contra falhas de hardware. Para implantar com Recuperação de Site, a VM de VMM deve ser implantada em um cluster estendido em sites geograficamente separados. Para fazer isso:

1. Instale o VMM em uma máquina virtual em um cluster de failover do Windows e selecione a opção para executar o servidor como altamente disponível durante a instalação.
2. A instância do SQL Server usada pelo VMM deve ser replicada com grupos de disponibilidade do AlwaysOn do SQL Server para que haja uma réplica de banco de dados no site secundário.

Observe que, quando você configura a Recuperação de Site, será necessário registrar cada servidor VMM no cluster no cofre de Recuperação de Site. Para fazer isso, instale o Provedor em um nó ativo e conclua a instalação para registrar o servidor VMM no cofre. Em seguida, instale o Provedor nos outros nós.
 
### Após uma paralisação 

Quando paralisações ocorrem, o servidor de VMM e seu banco de dados do SQL Server correspondente passam por failover e são acessado do site secundário.

![Servidor VMM virtual clusterizado.](./media/site-recovery-single-vmm/single-vmm-cluster.png)

## Próximas etapas

[Saiba mais](site-recovery-vmm-to-vmm.md) sobre a implantação de Recuperação de Site detalhada do VMM para replicação do VMM.




 

<!---HONumber=AcomDC_0706_2016-->