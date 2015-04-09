<properties 
	pageTitle="Visão Geral do Azure Site Recovery" 
	description="Implante a recuperação de site do Azure para proteger as máquinas virtuais locais para outro site local ou no Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Visão geral do Azure Site Recovery

<div class="dev-callout"> 
<p>O Azure Site Recovery orquestra a replicação e o failover em vários cenários:</p>


<ul>
<li>**Site do Hyper-V local para proteção do Azure com replicação do Hyper-V** - orquestrar a replicação, o failover e a recuperação de um site local com um ou mais servidores Hyper-V, mas sem o System Center VMM. Os dados de máquina virtual são replicados de um servidor host do Hyper-V de origem para o Azure. Leia <a href="http://go.microsoft.com/fwlink/?LinkId=522298">Introdução ao Azure Site Recovery: proteção entre um site do Hyper-V local e o Azure com a replicação do Hyper-V</a>.</li>
<li>**Proteção de site do VMM local para site do VMM local com replicação do Hyper-V** - orquestrar a replicação, o failover e a recuperação entre sites do VMM locais. Os dados de máquina virtual são replicados de um servidor host Hyper-V de origem para um servidor host de destino. Leia <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introdução ao Azure Site Recovery: proteção entre dois sites do VMM locais com replicação do Hyper-V</a>.</li>

<li>**Proteção de site do VMM local para site do VMM local com replicação SAN** - orquestra a replicação de ponta a ponta, o failover e a recuperação usando a replicação baseada em matriz de armazenamento entre dispositivos SAN que hospedam dados de máquina virtual em sites locais de origem e de destino. Leia <a href="http://go.microsoft.com/fwlink/?LinkId=518683">Introdução ao Azure Site Recovery: : proteção entre dois sites do VMM locais com replicação SAN</a>.</li>

<li>**Proteção de site do VMM local para Azure** - orquestrar a replicação, o failover e a recuperação entre um site do VMM local e o Azure. Os dados de máquina virtual replicados são armazenados no armazenamento do Azure. Leia <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Introdução ao Azure Site Recovery: proteção entre um site do VMM local e o Azure.</a></li>

<li>**Site do VMWare local para site do VMWare local com InMage** - InMage Scout é uma aquisição recente da Microsoft que fornece replicação em tempo real entre sites locais do VMWare. Agora o InMage está disponível como um produto separado que é obtido por meio de uma assinatura do serviço Azure Site Recovery. Leia <a href="http://go.microsoft.com/fwlink/?LinkId=518684">Introdução ao Azure Site Recovery: proteção entre sites do VMWare locais com InMage</a>.</li>
</ul>

<p>A matriz de recursos resume esses cenários.</p>

<table border="1">
<thead>
<tr>
	<th>Recurso</th><th>Local para Azure</th>
	<th>Local para local (Hyper-V)</th>
	<th>Local para local (SAN)</th>
</tr>
</thead>

<tr>
<td>Dados para Azure Site Recovery</td>
<td><p>Os metadados de nuvem são enviados ao Azure Site Recovery.</p> <p>Os dados replicados são armazenados no armazenamento do Azure.</p></td>
<td><p>Os metadados de nuvem são enviados ao Azure Site Recovery.</p> <p>Os dados replicados são armazenados no local especificado pelo servidor Hyper-V de destino.</p></td>
<td><p>Os metadados de nuvem são enviados ao Azure Site Recovery.</p> <p>Os dados replicados são armazenados no armazenamento de matriz de destino.</p></td>
</tr>

<tr>
<td>Requisitos de cofre</td>
<td><p>Você precisará de uma conta do Azure para configurar um cofre do Azure Site Recovery. Consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obtenha informações sobre preços em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de preços do Azure Site Recovery Manager</a>.</p></td>
<td><p>Conta do Azure com o Azure Site Recovery habilitado.</p>
</td><td><p>Conta do Azure com o Azure Site Recovery habilitado.</p></td>
</tr>

<tr>
<td>Replicação</td>
<td>As máquinas virtuais se replicam do servidor Hyper-V local para o armazenamento do Azure de destino. Você pode configurar a replicação inversa para replicar de volta para o local de origem.</td>
<td>As máquinas virtuais se replicam de um servidor Hyper-V local para outro. Você pode configurar a replicação inversa para replicar de volta para o local de origem.</td>
<td>As máquinas virtuais se replicam de um dispositivo de armazenamento SAN de origem para um dispositivo de armazenamento SAN de destino. Você pode configurar a replicação inversa para replicar de volta para o local de origem.</td>
</tr>

<tr>
<td>Armazenamento de máquina virtual</td>
<td>Disco rígido de máquina virtual armazenado no armazenamento do Azure</td>
<td>Disco rígido de máquina virtual armazenado em host Hyper-V.</td>
<td>Disco rígido de máquina virtual armazenado na matriz de armazenamento SAN.</td>
</tr>

<tr>
<td>Armazenamento do Azure</td>
<td><p>Conta de Armazenamento do Azure requerida.</p> <p>A conta deve ter georreplicação habilitada, estar localizada na mesma região que o serviço de Azure Site Recovery e ser associada à mesma assinatura.</p></td>
<td>Não aplicável</td>
<td>Não aplicável</td>
</tr>

<tr>
<td>Matriz de armazenamento SAN</td>
<td><p>Não aplicável</p></td>
<td>Não aplicável</td>
<td>A matriz de armazenamento SAN deve estar disponível nos sites de origem e de destino e ser gerenciada pelo VMM. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=518685">Pré-requisitos de implantação</a> para obter mais informações. </td>
</tr>

<tr>
<td>Servidor VMM</td>
<td>Requer um servidor VMM apenas no site de origem. O servidor VMM deve ter pelo menos uma nuvem que contenha pelo menos um cluster ou servidor host do Hyper-V.</td>
<td>Requer servidores VMM de origem e de destino com pelo menos uma nuvem em cada ou um único servidor VMM com duas nuvens As nuvens devem conter pelo menos um cluster ou servidor host do Hyper-V.</td>
<td>Exige servidores VMM de origem e de destino com pelo menos uma nuvem em cada. As nuvens devem conter pelo menos um cluster do Hyper-V.</td>
</tr>

<tr>
<td>Versão do System Center do VMM</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 com SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 com Pacote Cumulativo de Atualizações do VMM 5.0</p></td>
</tr>

<tr>
<td>Configuração do VMM</td>
<td><p>Configurar nuvens nos sites de origem e de destino</p><p>Configurar redes VM nos sites de origem e de destino</p><p>Configurar classificações de armazenamento nos sites de origem e de destino <p>Instalar o Provedor nos servidores VMM de origem e de destino</p></td>
<td><p>Configurar nuvens no site de origem</p><p>Configurar o armazenamento SAN</p><p>Configurar redes VM no site de origem</p><p>Instalar o Provedor no servidor VMM de origem</p><p>Habilitar a proteção da máquina virtual</p></td>
<td><p>Configurar nuvens nos sites de origem e de destino</p><p>Configurar redes VM nos sites de origem e destino</p><p>Instalar o Provedor no servidor VMM de origem e de destino</p><p>Habilitar a proteção da máquina virtual</p></td>
</tr>

<tr>
<td>Provedor do Azure Site Recovery</td>
<td>Instalar em um servidor VMM de origem</td>
<td>Instalar em servidores VMM de origem e de destino</td>
<td>Instalar em servidores VMM de origem e de destino</td>
</tr>

<tr>
<td>Agente de Serviços de Recuperação do Azure</td>
<td>Instalar nos servidores de host Hyper-V localizados em nuvens VMM</td>
<td>Não requerido</td>
<td>Não requerido</td>
</tr>

<tr>
<td>Pontos de recuperação de máquina virtual</td>
<td><p>Definir pontos de recuperação por tempo.</p> <p>Especifica por quanto tempo um ponto de recuperação deve ser mantido (0 a 24 h). Um ponto de recuperação é criado utilizando a fórmula 90/copy_frequency durante a primeira hora e, depois disso, a cada hora.</p></td>
<td><p>Definir pontos de recuperação por quantidade.</p> <p>Especifica quantos pontos de recuperação adicionais devem ser mantidos (0 a 15). Por padrão, um ponto de recuperação é criado por hora. Quando definido com o valor zero, somente o ponto de recuperação mais recente é armazenado na réplica do servidor host Hyper-V.</p></td>
<td>Definido nas configurações de armazenamento de matriz.</td>
</tr>

<tr>
<td>Mapeamento de rede</td>
<td><p>Mapear redes de máquinas virtuais para redes do Azure.</p> <p>O mapeamento de rede assegura que todas as máquinas virtuais pertencentes à mesma rede de máquinas virtuais de origem e que sofrem failover possam se conectar após o failover. Além disso, se houver um gateway de rede na rede Azure de destino, então, as máquinas virtuais poderão se conectar às máquinas virtuais presentes local. </p><p>Se o mapeamento não estiver habilitado, somente máquinas virtuais que falharem no mesmo plano de recuperação poderão se conectar umas às outras após o failover para o Azure.</p></td>
<td><p>Mapeie as redes de máquinas virtuais de origem para redes de máquinas virtuais de destino.</p> <p>O mapeamento de rede é utilizado para posicionar máquinas virtuais replicadas em servidores de host Hyper-V ideais, garantindo que as máquinas virtuais associadas à rede de máquinas virtuais de origem sejam associadas à rede de destino mapeada após o failover. </p><p>Se o mapeamento não estiver habilitado, as máquinas virtuais replicadas não serão conectadas a uma rede.</p></td>
<td><p>Mapeie as redes de máquinas virtuais de origem para redes de máquinas virtuais de destino.</p> <p>O mapeamento de rede assegura que máquinas virtuais associadas à rede VM de origem sejam associadas à rede de destino mapeada após o failover. </p><p>Se o mapeamento não estiver habilitado, as máquinas virtuais replicadas não serão conectadas a uma rede.</p></td>
</tr>

<tr>
<td>Mapeamento de armazenamento</td>
<td>Não aplicável</td>
<td><p>Mapeia classificações de armazenamento em servidores VMM de origem para classificações de armazenamento em servidores VMM de destino.</p> <p>Com a habilitação do mapeamento, os discos rígidos de máquinas virtuais na classificação de armazenamento de origem estarão localizados na classificação de armazenamento de destino após o failover.</p><p>Se o mapeamento de armazenamento não estiver habilitado, os discos rígidos virtuais replicados serão armazenados em um local padrão no servidor host Hyper-V de destino.</p></td>
<td><p>Mapeia entre matrizes de armazenamento e pools nos sites primários e secundários.</p></td>
</tr>

</table>

<p>Se tiver dúvidas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de Serviços de Recuperação do Azure</a>.</p> 

<!--HONumber=49-->