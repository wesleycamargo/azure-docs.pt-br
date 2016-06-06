<properties
	pageTitle="O que fazer caso uma interrupção de serviço do Azure afete as Máquinas Virtuais do Azure | Microsoft Azure"
	description="Saiba o que fazer caso uma interrupção de serviço do Azure afete as Máquinas Virtuais do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#O que fazer caso uma interrupção de serviço do Azure afete as Máquinas Virtuais do Azure

Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece SLAs (Contratos de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos de plataforma internos que dão suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, confira [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](https://aka.ms/drtechguide).

Este documento aborda a recuperação de desastre verdadeira, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de que haja uma interrupção em toda a região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a Replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. Em caso de interrupção regional completa ou de um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.
 
>[AZURE.NOTE]Lembre-se de que você não tem nenhum controle sobre esse processo, e ele ocorrerá somente para falhas em toda a região. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [Estratégias de Dados para Recuperação de Desastre](https://aka.ms/drtechguide#DSDR).

Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para Máquina Virtual do Azure no caso de uma interrupção de serviço de toda a região em que seu aplicativo da Máquina Virtual do Azure é implantado.

##Opção 1: aguardar a recuperação 
Nesse caso, nenhuma ação sua é necessária. Saiba que estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de integridade do serviço Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Essa será a melhor opção se você não tiver instalado o ASR (Azure Site Recovery), o backup de VM (Máquina Virtual), o RA-GRS (Armazenamento com Redundância Geográfica de Acesso de Leitura) ou o GRS (Redundância Geográfica) antes da interrupção. Se você configurou o armazenamento GRS (ou RA-GRS) para a conta de armazenamento em que estão armazenados os seus VHDs de VM, você pode procurar sobre como recuperar o VHD da imagem base e tentar provisionar uma nova VM por meio dele. Essa não é uma opção preferencial, pois não há nenhuma garantia de sincronização de dados, a menos que o backup da VM do Azure ou ASR sejam usados não sendo, portanto, garantido que funcione.

Para clientes que desejam acesso imediato às máquinas virtuais, estão disponíveis as duas opções abaixo.

>[AZURE.NOTE]Lembre-se de que ambas as opções abaixo têm a possibilidade de perda parcial de dados.

##Opção 2: Restaurar uma VM (Máquina Virtual) de um backup 
Para clientes que têm um backup da VM configurado, você pode restaurar a máquina virtual de seu ponto de backup e recuperação.

Siga estas etapas para restaurar uma nova VM por meio de um Backup do Azure, consulte Restauração de máquinas virtuais no Azure.

Para ajudar a planejar sua infraestrutura de backup de Máquinas Virtuais do Azure, leia o artigo [Planejar sua infraestrutura de backup de VM no Azure](../backup/backup-azure-vms-introduction.md).

##Opção 3: Iniciar um failover usando o ASR (Azure Site Recovery) 
Se tiver configurado o Azure Site Recovery para trabalhar com suas máquinas virtuais do Azure afetadas, você poderá restaurar as VMs de suas réplicas. Essas réplicas podem residir no Azure ou até mesmo localmente. Nesse caso, você pode criar novas VMs por meio da réplica existente. Siga estas etapas para restaurar suas VMs de uma Réplica do Azure Site Recovery, consulte [Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Embora o sistema operacional de Máquina Virtual do Azure e os discos de dados sejam replicados para um VHD (Disco Rígido Virtual) secundário, se estiverem em uma conta de armazenamento GRS ou RA-GRS, cada VHD será replicado de forma independente e esse nível de replicação não garantirá a consistência entre os VHDs replicados. Se seu aplicativo e/ou bancos de dados que usam esses discos de dados tiverem dependências entre si, não será garantido que todos os VHDs serão replicados como um único instantâneo. Também não é garantido que a réplica de VHD do armazenamento GRS ou RA-GRS resultará em um instantâneo consistente de aplicativo para inicializar a VM.

##Referências 
[Recuperação de Desastre e Alta Disponibilidade para Aplicativos do Azure](https://aka.ms/drtechguide)

[Orientação técnica sobre a continuação de negócios do Azure](http://aka.ms/bctechguide)

[Fazer backup de máquinas virtuais do Azure](../backup/backup-azure-vms.md)

[Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)

Se as instruções não estiverem claras ou se você desejar que a Microsoft faça as operações em seu nome, entre em contato com o [Atendimento ao Cliente](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0525_2016-->