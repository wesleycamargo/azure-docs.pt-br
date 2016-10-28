<properties
	pageTitle="Mapear o armazenamento no Azure Site Recovery para a replicação de uma máquina virtual do Hyper-V entre dois data centers locais | Microsoft Azure"
	description="Prepare o mapeamento de armazenamento para a replicação de máquina virtual do Hyper-V entre dois data centers locais com o Azure Site Recovery."
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


# Preparar o mapeamento de armazenamento para replicação de máquina virtual do Hyper-V entre dois data centers locais com o Azure Site Recovery


O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais e servidores físicos. Este artigo descreve o mapeamento de armazenamento, que ajuda a usar o armazenamento de forma ideal quando você está usando o Site Recovery para replicar máquinas virtuais do Hyper-V entre dois datacenters VMM locais.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

O mapeamento de armazenamento só será relevante quando você estiver replicando máquinas virtuais do Hyper-V localizadas nas nuvens do VMM de um data center principal para um datacenter secundário usando a replicação SAN ou a Réplica do Hyper-V, da seguinte maneira:


- **De local para local (replicação com a Réplica do Hyper-V)** — você configura o mapeamento de armazenamento ao mapear as classificações de armazenamento em servidores VMM de origem e de destino para fazer o seguinte:

	- **Identificar o armazenamento de destino para máquinas virtuais de réplica** — as máquinas virtuais serão replicadas para um destino de armazenamento escolhido por você (compartilhamento SMB ou volumes compartilhados de cluster (CSVs)).
	- **Posicionamento das máquinas virtuais de réplica** — o Mapeamento de armazenamento é usado para posicionar de forma ideal as máquinas virtuais de réplica em servidores host Hyper-V. As máquinas virtuais de réplica serão colocadas em hosts que possam acessar as redes VM mapeadas.
	- **Nenhum mapeamento de armazenamento** — se você não configurar o mapeamento de armazenamento, as máquinas virtuais serão replicadas para o local de armazenamento padrão especificado no servidor host Hyper-V associado à máquina virtual de réplica.

- **Replicação de local para local com SAN** — você configura o mapeamento de armazenamento ao mapear os pools de matrizes de armazenamento em servidores VMM de origem e de destino.
	- **Especificar pool** — especifica qual pool de armazenamento secundário receberá os dados de replicação do pool principal.
	- **Identificar pools de armazenamento de destino** — garante que os LUNs em um grupo de replicação serão replicados no pool de armazenamento de destino mapeado.

## Configurar classificações de armazenamento para a replicação do Hyper-V

Quando você estiver usando a Réplica do Hyper-V para replicar com o Site Recovery, mapeará entre classificações de armazenamento nos servidores VMM de origem e de destino ou em um único servidor VMM se dois locais forem gerenciados pelo mesmo servidor VMM. Observe que:

- Quando o mapeamento estiver configurado corretamente e a replicação estiver habilitada, disco rígido virtual de uma máquina virtual no local primário será replicado no armazenamento no local de destino mapeado.
- As classificações de armazenamento devem estar disponíveis para os grupos de host localizados nas nuvens de origem e de destino.
- As classificações não precisam ter o mesmo tipo de armazenamento. Por exemplo, você pode mapear uma classificação de origem que contenha compartilhamentos SMB para uma classificação de destino que contenha CSVs.
- Leia mais em [Como criar classificações de armazenamento np VMM](https://technet.microsoft.com/library/gg610685.aspx).

## Exemplo

Se as classificações estiverem configuradas corretamente no VMM quando você selecionar os servidores VMM de origem e de destino durante o mapeamento de armazenamento, as classificações de origem e de destino serão exibidas. Veja um exemplo de compartilhamentos de arquivos de armazenamento e classificações para uma organização com duas localizações, Nova Iorque e Chicago.

**Localidade** | **Servidor VMM** | **Compartilhamento de arquivos (origem)** | **Classificação (origem)** | **Mapeado para** | **Compartilhamento de arquivos (destino)**
---|---|--- |---|---|---
Nova Iorque | VMM\_Source| SourceShare1 | GOLD | GOLD\_TARGET | TargetShare1
 | | SourceShare2 | SILVER | SILVER\_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE\_TARGET | TargetShare3
Chicago | VMM\_Target | | GOLD\_TARGET | Não mapeado |
| | | SILVER\_TARGET | Não mapeado |
 | | | BRONZE\_TARGET | Não mapeado

Você deve configurá-los na guia **Armazenamento do Servidor**, na página **Recursos**, do portal de Recuperação de Site.

![Configurar o mapeamento de armazenamento](./media/site-recovery-storage-mapping/storage-mapping1.png)

Neste exemplo:
- Quando uma máquina virtual de réplica for criada para qualquer máquina virtual no armazenamento GOLD (SourceShare1), ela será replicada para um armazenamento GOLD\_TARGET (TargetShare1).
- Quando uma máquina virtual de réplica for criada para qualquer máquina virtual no armazenamento SILVER (SourceShare2), ela será replicada para um armazenamento SILVER\_TARGET (TargetShare2) etc.

Os compartilhamentos de arquivo reais e suas classificações atribuídas no VMM aparecem na captura de tela a seguir.

![Classificações de armazenamento no VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## Vários locais de armazenamento

Se a classificação de destino for atribuída a vários compartilhamentos SMB ou CSV, o local de armazenamento ideal será selecionado automaticamente quando a máquina virtual estiver protegida. Se nenhum armazenamento de destino adequado estiver disponível com a classificação especificada, o local de armazenamento padrão especificado no host Hyper-V será usado para posicionar os discos rígidos virtuais de réplica.

A tabela a seguir mostram como a classificação de armazenamento e os volumes compartilhados do cluster são configurados em nosso exemplo.

**Localidade** | **Classificação** | **Armazenamento associado**
---|---|---
Nova Iorque | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p>
 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
Chicago | GOLD\_TARGET | <p>C:\\ClusterStorage\\TargetVolume1</p><p>\\FileServer\\TargetShare1</p>
 | SILVER\_TARGET| <p>C:\\ClusterStorage\\TargetVolume2</p><p>\\FileServer\\TargetShare2</p>

Esta tabela resume o comportamento ao ativar a proteção para máquinas virtuais (VM1 - VM5) neste ambiente de exemplo.

**Máquina virtual** | **Armazenamento de origem** | **Classificação de origem** | **Armazenamento de destino mapeado**
---|---|---|---
VM1 | C:\\ClusterStorage\\SourceVolume1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\\FileServer\\SourceShare1</p><p>Ambos GOLD\_TARGET</p>
VM2 | \\FileServer\\SourceShare1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p> <p>Ambos GOLD\_TARGET</p>
VM3 | C:\\ClusterStorage\\SourceVolume2 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
VM4 | \\FileServer\\SourceShare2 | SILVER |<p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p><p>Ambos SILVER\_TARGET</p>
VM5 | C:\\ClusterStorage\\SourceVolume3 | N/D | Nenhum mapeamento; sendo assim, o local de armazenamento padrão do host Hyper-V é usado

## Próximas etapas

Agora que você compreende melhor o mapeamento de armazenamento, [prepare-se para implantar o Azure Site Recovery](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0706_2016-->