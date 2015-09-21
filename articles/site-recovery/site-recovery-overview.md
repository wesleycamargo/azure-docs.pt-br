<properties
	pageTitle="Visão geral sobre a Recuperação de Site" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos locais para o Azure ou para um site local secundário." 
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
	ms.date="08/05/2015" 
	ms.author="raynew"/>

#  Visão geral sobre a Recuperação de Site

O serviço Recuperação de Site contribui para uma solução robusta de BCDR (continuidade dos negócios e recuperação de desastres) que protege servidores físicos locais e máquinas virtuais, administrando e automatizando a replicação e o failover no Azure ou em um datacenter local secundário.

- **Simplificar** - a Recuperação de Site ajuda a simplificar sua estratégia de BCDR, facilitando a configuração da replicação e executando o failover e a recuperação de seus aplicativos e cargas de trabalho locais.
- **Replicação** - você pode duplicar cargas de trabalho locais no armazenamento do Azure ou em um armazenamento de dados secundário. 
- **Cofre** - para gerenciar a replicação, configure um cofre de Recuperação de Site em uma região do Azure selecionada por você. Todos os metadados permanecem nessa região.
- **Metadados** - nenhum dado do aplicativo é enviado ao Azure. A Recuperação de Site precisa apenas de metadados como nomes de nuvem do VMM e de máquina virtual, a fim de coordenar a replicação e o failover. 
- **Conexão com o Azure** - os servidores de gerenciamento se comunicam com o Azure dependendo do cenário de implantação. Por exemplo, se você estiver replicando máquinas virtuais localizadas em uma nuvem do VMM local, o servidor VMM se comunicará com o Site Recovery em uma conexão HTTPS de saída criptografada. Nenhuma conexão é necessária nas máquinas virtuais ou hosts Hyper-V.
- **Réplica do Hyper-V** - o Azure Site Recovery aproveita a Réplica do Hyper-V para o processo de replicação, e também pode usar a replicação de SAN se você estiver replicando entre dois sites locais do VMM. A Recuperação de Site usa a replicação inteligente, replicando apenas os blocos de dados e não todo o VHD para a replicação inicial. Somente as alterações delta são replicadas durante a replicação contínua. A Recuperação de Site dá suporte à transferência de dados offline e funciona com otimizadores WAN.
- **Preços**-Você pode [Ler mais](pricing/details/site-recovery) sobre os preços da Recuperação de Site.


## Cenários de implantação

Esta tabela resume os cenários de replicação com suporte da Recuperação de Site.

**Replicar para** | **Replicar de (local)** | **Detalhes** | **Artigo**
---|---|---|---
As tabelas | Site do Hyper-V | Replique a máquina virtual em um ou mais servidores host Hyper-V locais definidos como um site do Hyper-V para o Azure. Nenhum servidor VMM é necessário. | [Leia mais](site-recovery-hyper-v-site-to-azure.md)
As tabelas| Servidor VMM | Replique as máquinas virtuais em um ou mais servidores host Hyper-V locais que estejam em uma nuvem VMM para o Azure. | [Leia mais](site-recovery-vmm-to-azure.md) 
As tabelas | Servidor físico do Windows | Replique um servidor físico do Windows ou do Linux para o Azure | [Leia mais](site-recovery-vmware-to-azure.md)
As tabelas | Máquina virtual VMware | Replique as máquinas virtuais VMware para o Azure | [Leia mais](site-recovery-vmware-to-azure.md)
Armazenamento de dados secundário | Servidor VMM | Replique as máquinas virtuais em servidores host Hyper-V locais que estejam em uma nuvem VMM para um servidor VMM secundário em outro armazenamento de dados. | [Leia mais](site-recovery-vmm-to-vmm.md)
Armazenamento de dados secundário | Servidor VMM com SAN | Replique as máquinas virtuais em servidores host Hyper-V locais que estejam em uma nuvem VMM para um servidor VMM secundário em outro armazenamento de dados usando a replicação SAN.| [Leia mais](site-recovery-vmm-san.md)
Armazenamento de dados secundário | Servidor VMM único | Replique as máquinas virtuais em servidores host Hyper-V locais que estejam em uma nuvem VMM para uma nuvem secundária no mesmo servidor VMM. | [Leia mais](site-recovery-single-vmm.md) 


## Diretrizes de carga de trabalho

As tecnologias de replicação de ASR são compatíveis com qualquer aplicativo em execução em uma máquina virtual. Conduzimos testes adicionais em parceria com as equipes de produto do aplicativo a fim de dar mais suporte a cada aplicativo.

**Carga de trabalho** | <p>**Replicar máquinas virtuais Hyper-V**</p> <p>**(para o site secundário)**</p> | <p>**Replicar máquinas virtuais Hyper-V**</p><p>**(para o Azure)**</p> | <p>**Replicar máquinas virtuais VMware**</p> <p>**(para o site secundário)**</p> | <p>**Replicar máquinas virtuais VMware**</p><p>**(para o Azure)**</p>
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y 
Aplicativos Web (IIS, SQL) | Y | Y | Y | Y
SCOM | Y | Y | Y | Y
Sharepoint | Y | Y | Y | Y
<p>SAP</p><p>Replicar site do SAP para o Azure para não cluster</p> | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Exchange (não DAG) | Y | Em breve | Y | Y
Área de Trabalho Remota/VDI | Y | Y | Y | N/D 
<p>Linux</p> <p>(sistema operacional e aplicativos)</p> | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) 
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Em breve | Y | Em breve
Oracle | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Windows File Server | Y | Y | Y | Y


## Recursos e requisitos 

Esta tabela resume os principais recursos da Recuperação de Site e como eles são manipulados durante a replicação no Azure, replicação para um site secundário usando a replicação da Réplica do Hyper-V padrão e usando SAN.

Recurso|Replicar no Azure|Replicar em um local secundário (Réplica do Hyper-V)|Replicar em um local secundário (SAN)
---|---|---|---
Replicação de dados|Os metadados sobre os servidores e as máquinas virtuais locais são armazenados no cofre da Recuperação de Site.</p> <p>Os dados replicados são armazenados no armazenamento do Azure.|Os metadados sobre os servidores e as máquinas virtuais locais são armazenados no cofre da Recuperação de Site.</p> <p>Os dados replicados são armazenados no local especificado pelo servidor Hyper-V de destino.|Os metadados sobre os servidores e as máquinas virtuais locais são armazenados no cofre da Recuperação de Site.</p> <p>Os dados replicados são armazenados no armazenamento de matriz de destino.
Requisitos de cofre|Conta do Azure com o serviço Recuperação de Site.|Conta do Azure com o serviço Recuperação de Site.|Conta do Azure com o serviço Recuperação de Site.
Replicação|Replique a máquina virtual do host Hyper-V de origem para o armazenamento do Azure. Failback para o local de origem.|Replique a máquina virtual do host Hyper-V de origem para o host Hyper-V de destino. Failback para o local de origem.|Replique as máquinas virtuais de um dispositivo de armazenamento SAN de origem para um dispositivo de armazenamento SAN de destino. Failback para o local de origem.
Máquina virtual|Disco rígido de máquina virtual armazenado no armazenamento do Azure|Disco rígido de máquina virtual armazenado no host Hyper-V|Disco rígido de máquina virtual armazenado na matriz de armazenamento SAN
Armazenamento do Azure|Necessário para armazenar os discos rígidos de máquina virtual replicada|Não aplicável|Não aplicável
Matriz de armazenamento SAN|Não aplicável|Não aplicável|A matriz de armazenamento SAN deve estar disponível nos sites de origem e de destino e ser gerenciada pelo VMM
Servidor VMM|Servidor VMM apenas no site de origem.|Recomenda-se servidores VMM nos sites de origem e destino. Você pode replicar entre nuvens em um único servidor VMM.|Servidor VMM em sites VMM de origem e de destino. As nuvens devem conter pelo menos um cluster do Hyper-V.
Versão do VMM|System Center 2012 R2<p>System Center 2012 com SP1|System Center 2012 R2|System Center 2012 R2 com Pacote Cumulativo de Atualizações do VMM 5.0
Configuração do VMM|Configurar nuvens nos sites de origem e de destino</p><p>Configurar redes VM no site de origem e de destino<p>Configurar classificações de armazenamento nos sites de origem e de destino<p>Instalar o Provedor nos servidores VMM de origem e de destino|Configurar nuvens no site de origem</p><p>Configurar o armazenamento SAN</p><p>Configurar redes VM no site de origem</p><p>Instalar o Provedor no servidor VMM de origem</p><p>Habilitar proteção de máquina virtual|Configurar nuvens nos sites de origem e de destino</p><p>Configurar redes VM nos sites de origem e de destino</p><p>Instalar o Provedor no servidor VMM de origem e de destino</p><p>Habilitar a proteção de máquina virtual
Provedor do Azure Site Recovery</p><p>Usado para conexão por HTTPS ao Site Recovery|Instalar em um servidor VMM de origem|Instalar em servidores VMM de origem e de destino|Instalar em servidores VMM de origem e de destino
Agente de Serviços de Recuperação do Azure</p><p>Usado para se conectar por HTTPS ao Site Recovery|Instalar em servidores host Hyper-V|Não requerido|Não requerido
Pontos de recuperação de máquina virtual|Definir pontos de recuperação por tempo.</p> <p>Especifica por quanto tempo um ponto de recuperação deve ser mantido (0 a 24 horas)|Definir pontos de recuperação por quantidade.</p> <p>Especifica quantos pontos de recuperação adicionais devem ser mantidos (0 a 15). Por padrão, um ponto de recuperação é criado por hora|Definido nas configurações de armazenamento de matriz
Mapeamento de rede|Mapear redes VM para redes do Azure.</p> <p>O mapeamento de rede assegura que todas as máquinas virtuais com failover na mesma rede VM de origem possam se conectar após o failover. Além disso, se houver um gateway de rede na rede Azure de destino, então, as máquinas virtuais poderão se conectar às máquinas virtuais presentes no local. </p><p>Se o mapeamento não estiver habilitado, somente máquinas virtuais que falharem no mesmo plano de recuperação poderão se conectar umas às outras após o failover para o Azure.|Mapeie as redes VM de origem para as redes VM de destino.</p> <p>O mapeamento de rede é utilizado para posicionar máquinas virtuais replicadas em servidores host Hyper-V ideais, garantindo que as máquinas virtuais associadas à rede VM de origem sejam associadas à rede de destino mapeada após o failover. </p><p>Se o mapeamento não estiver habilitado, as máquinas virtuais replicadas não serão conectadas a uma rede.|Mapeie as redes VM de origem para as redes VM de destino.</p> <p>O mapeamento de rede assegura que máquinas virtuais associadas à rede VM de origem sejam associadas à rede de destino mapeada após o failover. </p><p>Se o mapeamento não estiver habilitado, as máquinas virtuais replicadas não serão conectadas a uma rede.
Mapeamento de armazenamento|Não aplicável|Mapeia classificações de armazenamento em servidores VMM de origem para classificações de armazenamento em servidores VMM de destino.</p> <p>Com a habilitação do mapeamento, os discos rígidos de máquinas virtuais na classificação de armazenamento de origem estarão localizados na classificação de armazenamento de destino após o failover.</p><p>Se o mapeamento de armazenamento não estiver habilitado, os discos rígidos virtuais replicados serão armazenados em um local padrão no servidor host Hyper-V de destino.|Mapeia entre matrizes de armazenamento e pools nos sites primários e secundários.

## Próximas etapas

Depois de concluir esta visão geral, [leia as práticas recomendadas](site-recovery-best-practices.md) para começar a planejar a implantação.
 

<!---HONumber=Sept15_HO2-->