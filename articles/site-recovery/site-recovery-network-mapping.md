<properties
	pageTitle="Preparar o mapeamento de rede para a proteção de uma máquina virtual do Hyper-V com VMM no Azure Site Recovery | Microsoft Azure"
	description="Configure o mapeamento de rede para a replicação de máquinas virtuais do Hyper-V de um datacenter local para o Azure, ou para um site secundário."
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
	ms.workload="storage-backup-recovery"
	ms.date="07/06/2016"
	ms.author="raynew"/>


# Preparar o mapeamento de rede para a proteção de uma máquina virtual do Hyper-V com VMM no Azure Site Recovery

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais e servidores físicos.

Este artigo descreve o mapeamento de rede, que ajuda a definir de forma ideal as configurações de rede quando você estiver usando o Site Recovery para replicar máquinas virtuais do Hyper-V localizadas em nuvens do VMM entre dois data centers locais ou entre um datacenter local e o Azure. Observe que, se você estiver replicando VMs do Hyper-V sem uma nuvem de VMM, ou se estiver replicando VMs ou servidores físicos da VMware, este artigo não será relevante.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Visão geral

O mapeamento de rede é usado quando o Azure Site Recovery é implantado para replicar máquinas virtuais do Hyper-V para o Azure ou para um datacenter secundário, usando a Réplica do Hyper-V ou a replicação de SAN.

- **Replicando máquinas virtuais do Hyper-V em nuvens de VMM entre dois datacenters locais** — o mapeamento de rede realiza o mapeamento entre redes de VM em um servidor VMM de origem e redes de VM em um servidor VMM de destino, a fim de fazer o seguinte:

	- **Conectar máquinas virtuais após o failover**. garante que as máquinas virtuais serão conectadas às redes apropriadas após o failover. A máquina virtual de réplica será conectada à rede de destino mapeada para a rede de origem.
	- **Colocar máquinas virtuais de réplica nos servidores host**: posiciona de forma ideal as máquinas virtuais de réplica em servidores host Hyper-V. As máquinas virtuais de réplica serão colocadas em hosts que podem acessar as redes VM mapeadas.
	- **Nenhum mapeamento de rede** — se você não configurar o mapeamento de rede, as máquinas virtuais replicadas não serão conectadas às redes VM após o failover.

- **Replicando máquinas virtuais do Hyper-V em uma nuvem de VMM local para o Azure** — o mapeamento de rede realiza o mapeamento entre redes de VM no servidor VMM de origem e redes do Azure destino, para fazer o seguinte:
	- **Conectar máquinas virtuais após o failover**: todos os computadores que forem submetidos a failover na mesma rede poderão se conectar entre si, independentemente do plano de recuperação em que estão.
	- **Gateway de rede** — se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão conectar-se a outras máquinas virtuais locais.
	- **Nenhum mapeamento de rede** — se você não configurar o mapeamento de rede, somente as máquinas virtuais com failover no mesmo plano de recuperação poderão conectar-se entre si após o failover no Azure.


## Exemplo de mapeamento de rede

O mapeamento de rede pode ser configurado entre redes VM em dois servidores do VMM, ou em um único servidor VMM, se dois locais forem gerenciados pelo mesmo servidor. Quando o mapeamento de rede é configurado corretamente e a replicação é habilitada, uma máquina virtual no local primário será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada.

Se as redes tiverem sido configuradas corretamente no VMM, quando você selecionar uma rede VM de destino durante o mapeamento de rede, as nuvens de origem do VMM que usam a rede VM de origem serão exibidas, junto com as redes VM de destino disponíveis nas nuvens de destino usadas para proteção.

A seguir, um exemplo para ilustrar esse mecanismo. Vamos usar uma organização com dois locais, Nova Iorque e Chicago.

**Localidade** | **Servidor VMM** | **Redes VM** | **Mapeado para**
---|---|---|---
Nova Iorque | VMM-NewYork| VMNetwork1-NewYork | Mapeado para VMNetwork1-Chicago
 | | VMNetwork2-NewYork | Não mapeado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mapeado para VMNetwork1-NewYork
 | | VMNetwork1-Chicago | Não mapeado

Neste exemplo:

- Quando uma máquina virtual de réplica é criada para qualquer máquina virtual conectada a VMNetwork1-NewYork, ela é conectada a VMNetwork1-Chicago.
- Quando uma máquina virtual de réplica é criada para VMNetwork2-NewYork ou VMNetwork2-Chicago, ele não é conectada a nenhuma rede.

Veja como as nuvens do VMM são configuradas em nosso exemplo de organização e como as redes lógicas são associadas às nuvens.

### Configurações de proteção de nuvem

**Nuvem protegida** | **Protegendo a nuvem** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### Configurações de rede lógica e de VM

**Localidade** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova Iorque | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### Redes de destino

Com base nessas configurações, ao selecionar a rede VM de destino, a tabela a seguir mostra as opções disponíveis.

**Seleção** | **Nuvem protegida** | **Protegendo a nuvem** | **Rede de destino disponível**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível



## Várias sub-redes

Se a rede de destino tiver várias sub-redes e uma delas tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada a essa sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


### Failback

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que a VMNetwork1-NewYork seja mapeada para VMNetwork1-Chicago, com as configurações a seguir.


**Máquina virtual** | **Conectado à rede VM**
---|---
VM1 | VMNetwork1-Rede
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com essas configurações, vamos analisar o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Nenhuma alteração nas propriedades de rede de VM-2 após o failover. | A VM-1 permanece conectada à rede de origem.
As propriedades de rede de VM-2 são alteradas após o failover e ela é desconectada. | A VM-1 é desconectada.
As propriedades de rede de VM-2 são alteradas após o failover e ela é conectada à VMNetwork2-Chicago. | Se a VMNetwork2-Chicago não estiver mapeada, a VM-1 será desconectada.
O mapeamento de rede da VMNetwork1-Chicago é alterado. | A VM-1 será conectada à rede, agora mapeada para VMNetwork1-Chicago.


## Próximas etapas

Agora que você compreende melhor o mapeamento de rede, [obtenha uma introdução à implantação do Site Recovery](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0706_2016-->