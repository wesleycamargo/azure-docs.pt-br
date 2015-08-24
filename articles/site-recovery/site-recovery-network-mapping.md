<properties
	pageTitle="Mapeamento de rede de Recuperação de Site"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos locais para o Azure ou para um site local secundário."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="08/05/2015"
	ms.author="raynew"/>


# Mapeamento de rede de Recuperação de Site


O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Leia sobre possíveis cenários de implantação na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).


## Sobre este artigo

O mapeamento de rede é um elemento importante da implantação do VMM e da Recuperação de Site. Ele coloca da melhor maneira possível as máquinas virtuais replicadas nos servidores host Hyper-V de destino e garante que suas máquinas virtuais replicadas estejam conectadas às redes apropriadas após o failover. Este artigo descreve o mapeamento de rede e fornece alguns exemplos para ajudar você a entender como funciona o mapeamento de rede.


Publique qualquer pergunta no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

A maneira na qual você configura o mapeamento de rede depende de seu cenário de implantação da Recuperação de Site.



- **Local para servidores VMM locais**: o mapeamento de rede realiza o mapeamento entre redes VM em um servidor VMM de origem e redes VM em um servidor VMM de destino, a fim de fazer o seguinte:

	- **Conectar máquinas virtuais após o failover**. garante que as máquinas virtuais serão conectadas às redes apropriadas após o failover. A máquina virtual de réplica será conectada à rede de destino mapeada para a rede de origem.
	- **Colocar máquinas virtuais de réplica nos servidores host**: posiciona de forma ideal as máquinas virtuais de réplica em servidores host Hyper-V. As máquinas virtuais de réplica serão colocadas em hosts que podem acessar as redes VM mapeadas.
	- **Nenhum mapeamento de rede**: se você não configurar o mapeamento de rede, as máquinas virtuais de réplica não serão conectadas às redes VM após o failover.

- **Servidor VMM local para o Azure**: o mapeamento de rede realiza o mapeamento entre redes VM no servidor VMM de origem e redes do Azure destino, a fim de fazer o seguinte:
	- **Conectar máquinas virtuais após o failover**: todos os computadores que forem submetidos a failover na mesma rede poderão se conectar entre si, independentemente do plano de recuperação em que estão.
	- **Gateway de rede**. se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão se conectar a outras máquinas virtuais locais.
	- **Nenhum mapeamento de rede**: se você não configurar o mapeamento de rede, somente as máquinas virtuais com failover no mesmo plano de recuperação poderão se conectar entre si após o failover no Azure.

## Redes VM

A rede lógica VMM fornece uma exibição abstrata da infraestrutura de rede física. As redes VM fornecem uma interface de rede para que as máquinas virtuais possam se conectar a redes lógicas. Uma rede lógica precisa de pelo menos uma rede VM. Quando você coloca uma máquina virtual em uma nuvem para proteção, ela deve estar conectada a uma rede VM vinculada a uma rede lógica associada à nuvem. Saiba mais em:

- [Redes lógicas (Parte 1)](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)
- [Rede virtual no VMM 2012 SP1](http://blogs.technet.com/b/scvmm/archive/2013/01/08/virtual-networking-in-vmm-2012-sp1.aspx)

## Exemplo

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

Veja como as nuvens do VMM são configuradas em nosso exemplo de organização, e como as redes lógicas são associadas às nuvens.

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

Se a rede de destino tiver várias sub-redes, e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.


### Failback

Para ver o que acontece no caso de failback (replicação inversa), vamos supor que a VMNetwork1-NewYork seja mapeada para VMNetwork1-Chicago, com as configurações a seguir.


**Máquina virtual** | **Conectado à rede VM**
---|---
VM1 | VMNetwork1-Rede
VM2 (réplica de VM1) | VMNetwork1-Chicago

Com essas configurações, vamos analisar o que acontece em alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Nenhuma alteração nas propriedades de rede de VM-2 após o failover | A VM-1 permanece conectada à rede de origem.
As propriedades de rede de VM-2 são alteradas após o failover, e é desconectada | VM-1 é desconectada
As propriedades de rede de VM-2 são alteradas após o failover, e é conectada à VMNetwork2-Chicago | Se VMNetwork2-Chicago não estiver mapeada, a VM-1 será desconectada
O mapeamento de rede de VMNetwork1-Chicago é alterado | A VM-1 será conectada à rede, agora mapeada para VMNetwork1-Chicago


## Próximas etapas

Agora que você compreende melhor o mapeamento de rede, comece a ler as [práticas recomendadas](site-recovery-best-practices.md) para se preparar para a implantação.
 

<!---HONumber=August15_HO7-->