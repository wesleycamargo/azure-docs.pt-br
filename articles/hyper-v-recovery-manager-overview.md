<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Visão geral de recuperação de site do Azure

<div class="dev-callout">

Implante a recuperação de site do Azure para proteger máquinas virtuais executadas em servidores de host Hyper-V localizados nas nuvens do Virtual Machine Manager (VMM) do System Center. Você pode implantar a Recuperação de Site do Azure de dois modos:

-   **Proteção do local para o próprio local** - faça a replicação de máquinas virtuais de um local dessa instalação para outro. Você configura e habilita as configurações de proteção nos cofres da Recuperação de Site do Azure. Os dados de máquina virtual são replicados de um servidor de host Hyper-V de origem para um servidor de host de destino. A Recuperação de Site do Azure gerencia o processo. Há um tutorial disponível para esse cenário em [Introdução à Recuperação de Site do Azure: Proteção do local para o próprio local][Introdução à Recuperação de Site do Azure: Proteção do local para o próprio local].
-   **Proteção no local para o Azure** - faça a replicação de máquinas virtuais de um local dessa instalação para o Microsoft Azure. Você configura e habilita as configurações de proteção nos cofres da Recuperação de Site do Azure. A Recuperação de Site do Azure gerencia o processo e os dados de máquinas virtuais replicados são armazenados no armazenamento do Azure. Há um tutorial disponível para esse cenário em [Introdução à Recuperação de Site do Azure: Proteção no local para o Azure][Introdução à Recuperação de Site do Azure: Proteção no local para o Azure].

A tabela a seguir resume e compara as duas opções de implantação.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Recurso</th>
<th align="left">No local para o Azure</th>
<th align="left">Do local para o próprio local</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Recuperação de Site do Azure</td>
<td align="left">Metadados de nuvem são enviados para a Recuperação de Site do Azure. Os dados replicados são armazenados no armazenamento do Azure.</td>
<td align="left">Metadados de nuvem são enviados para a Recuperação de Site do Azure. Os dados replicados são armazenados no servidor de host Hyper-V de destino.</td>
</tr>
<tr class="even">
<td align="left">Requisitos de cofre</td>
<td align="left"><p>O cofre exige um certificado que esteja em conformidade com os <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">requisitos do cofre</a>.</p>
<p>Uma chave do cofre é gerada automaticamente. A chave assegura a integridade no tráfego entre o provedor de Recuperação de Site do Azure no servidor VMM e a Recuperação de Site do Azure.</p></td>
<td align="left"><p>O cofre exige um certificado que esteja em conformidade com os <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">requisitos do cofre</a>.</p>
<p>Uma chave do cofre é gerada automaticamente. A chave assegura a integridade no tráfego entre o provedor de Recuperação de Site do Azure no servidor VMM e a Recuperação de Site do Azure.</p></td>
</tr>
<tr class="odd">
<td align="left">Replicação</td>
<td align="left">As máquinas virtuais se replicam por meio de um servidor Hyper-V local para o armazenamento do Azure.</td>
<td align="left">As máquinas virtuais se replicam por meio de um servidor Hyper-V local para outro.</td>
</tr>
<tr class="even">
<td align="left">Armazenamento de máquina virtual</td>
<td align="left">Disco rígido de máquina virtual armazenado no armazenamento do Azure</td>
<td align="left">Disco rígido de máquina virtual armazenado em host Hyper-V.</td>
</tr>
<tr class="odd">
<td align="left">Armazenamento do Azure</td>
<td align="left"><p>Conta de Armazenamento do Azure requerida.</p>
<p>A conta deve ter georreplicação habilitada, estar localizada na mesma região que o serviço de Recuperação de Site do Azure e ser associada à mesma assinatura.</p></td>
<td align="left">Não aplicável</td>
</tr>
<tr class="even">
<td align="left">Servidor VMM</td>
<td align="left">Requer somente um servidor VMM de origem</td>
<td align="left">Requer servidores VMM de origem e de destino com pelo menos uma nuvem em cada, ou um único servidor VMM com duas nuvens</td>
</tr>
<tr class="odd">
<td align="left">Versão do System Center do servidor VMM</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 com SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Provedor de Recuperação de Site do Azure</td>
<td align="left">Instalar em um servidor VMM de origem</td>
<td align="left">Instalar em servidores VMM de origem e de destino</td>
</tr>
<tr class="odd">
<td align="left">Agente de Serviços de Recuperação do Azure</td>
<td align="left">Instalar nos servidores de host Hyper-V localizados em nuvens VMM</td>
<td align="left">Não requerido</td>
</tr>
<tr class="even">
<td align="left">Pontos de recuperação de máquina virtual</td>
<td align="left"><p>Definir pontos de recuperação por tempo.</p>
<p>Especifica por quanto tempo um ponto de recuperação deve ser mantido (0 a 24 h). Um ponto de recuperação é criado utilizando a fórmula 90/copy_frequency durante a primeira hora e, depois disso, a cada hora.</p></td>
<td align="left"><p>Definir pontos de recuperação por quantidade.</p>
<p>Especifica quantos pontos de recuperação adicionais devem ser mantidos (0 a 15). Por padrão, um ponto de recuperação é criado por hora. Quando definido com o valor zero, somente o ponto de recuperação mais recente é armazenado na réplica do servidor de host Hyper-V.</p></td>
</tr>
<tr class="odd">
<td align="left">Mapeamento de rede</td>
<td align="left"><p>Mapear redes de máquinas virtuais para redes do Azure.</p>
<p>O mapeamento de rede assegura que todas as máquinas virtuais pertencentes à mesma rede de máquinas virtuais de origem e que sofrem failover possam se conectar após o failover. Além disso, se houver um gateway de rede na rede Azure de destino, então, as máquinas virtuais poderão se conectar às máquinas virtuais presentes no local.</p>
<p>Se o mapeamento não estiver habilitado, somente máquinas virtuais que falharem no mesmo plano de recuperação poderão se conectar umas às outras após o failover para o Azure.</p></td>
<td align="left"><p>Mapeie as redes de máquinas virtuais de origem para redes de máquinas virtuais de destino.</p>
<p>O mapeamento de rede é utilizado para posicionar máquinas virtuais replicadas em servidores de host Hyper-V ideais, garantindo que as máquinas virtuais associadas à rede de máquinas virtuais de origem sejam associadas à rede de destino mapeada após o failover.</p>
<p>Se o mapeamento não estiver habilitado, as máquinas virtuais replicadas não serão conectadas a uma rede.</p></td>
</tr>
<tr class="even">
<td align="left">Mapeamento de armazenamento</td>
<td align="left">Não aplicável</td>
<td align="left"><p>Mapeia classificações de armazenamento em servidores VMM de origem para classificações de armazenamento em servidores VMM de destino.</p>
<p>Com a habilitação do mapeamento, os discos rígidos de máquinas virtuais na classificação de armazenamento de origem estarão localizados na classificação de armazenamento de destino após o failover.</p>
<p>Se o mapeamento de armazenamento não estiver habilitado, os discos rígidos virtuais replicados serão armazenados em um local padrão no servidor de host Hyper-V de destino.</p></td>
</tr>
</tbody>
</table>

Para dúvidas, visite o [Fórum de serviços de recuperação do Windows Azure][Fórum de serviços de recuperação do Windows Azure].

</div>

  [Introdução à Recuperação de Site do Azure: Proteção do local para o próprio local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introdução à Recuperação de Site do Azure: Proteção no local para o Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [requisitos do cofre]: %20http://go.microsoft.com/fwlink/?LinkId=386485
  [Fórum de serviços de recuperação do Windows Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
