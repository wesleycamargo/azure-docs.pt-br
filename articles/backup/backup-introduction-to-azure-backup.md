<properties
	pageTitle="O que é o Backup do Azure? | Microsoft Azure"
	description="Com os serviços do Backup e recuperação do Azure, você pode fazer backup e restaurar dados e aplicativos de Windows Servers, máquinas de cliente do Windows, servidores SCDPM ou máquinas virtuais do Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="backup e restauração; serviços de recuperação"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="trinadhk"; "jimpark"/>

# O que é o Backup do Azure?
O Backup do Azure é o serviço que você usa para fazer backup e restaurar os dados na nuvem da Microsoft. Ele substitui a solução de backup local ou externa existente por uma solução baseada em nuvem confiável, segura e econômica. Ele também protege os ativos em execução na nuvem. O Backup do Azure fornece serviços de recuperação criados em uma infraestrutura de classe mundial que é escalonável, durável e altamente disponível.

[Assista a uma visão geral em vídeo do Backup do Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Por que usar o Backup do Azure?
As soluções tradicionais de backup foram desenvolvidas para tratar a nuvem como um ponto de extremidade semelhante a disco ou fita. Embora essa abordagem seja simples, é limitada e não aproveita plenamente uma plataforma de nuvem subjacente. Isso se traduz em uma solução ineficiente e cara. Por outro lado, o Backup do Azure oferece todas as vantagens de uma solução de backup na nuvem eficiente e econômica. Alguns dos principais benefícios que o Backup do Azure fornece são:

| RECURSO | BENEFÍCIO |
| ------- | ------- |
| Gerenciamento de armazenamento automático | Não são necessárias despesas de capital para dispositivos de armazenamento locais. O Backup do Azure automaticamente aloca e gerencia o armazenamento de backup, com um modelo de consumo de pagamento conforme o uso. |
| Dimensionamento ilimitado | Alta disponibilidade garantida, sem a sobrecarga de manutenção e monitoramento. O Backup do Azure usa a capacidade e a escala subjacentes da nuvem do Azure, com recursos não intrusivos de dimensionamento automático. |
| Várias opções de armazenamento | Escolha o armazenamento de backup com base na necessidade: <li>o blob de blocos LRS (Armazenamento com Redundância Local) é ideal para os clientes preocupados com o preço, além de proteger os dados contra falhas de hardware local. <li>O blob de blocos GRS (Armazenamento com Replicação Geográfica) fornece três cópias adicionais em um datacenter emparelhado, garantindo que os dados de backup estejam altamente disponíveis, mesmo que ocorra um desastre de nível de site do Azure. |
| Transferência de dados ilimitada | Não são cobrados os custos de transferências de dados de saída durante uma operação de restauração do cofre do Backup do Azure. Os dados de entrada para o Azure também são gratuitos. |
| Gerenciamento central | A simplicidade e a familiaridade do portal do Azure. À medida que o serviço evolui, recursos como o gerenciamento centralizado permitirão que você gerencie sua infraestrutura de backup em um único local. |
| Criptografia de dados. | Transmissão segura e armazenamento de dados do cliente na nuvem pública. A senha de criptografia é armazenada na origem e nunca é transmitida nem armazenada no Azure. A chave de criptografia é necessária para restaurar quaisquer dados e somente o cliente tem acesso completo aos dados no serviço. |  
| Integração do VSS | Os backups consistentes de aplicativos no Windows garantem que não haja necessidade de correção no momento da restauração. Isso reduz o RTO e permite que os clientes retornem a um estado de execução mais depressa. |
| Retenção de longo prazo | Em vez de pagar por soluções de backup em fita externa, os clientes podem fazer backup no Azure, o que fornece uma solução atraente com semântica semelhante à da fita por um custo muito baixo. |

## Componentes do Backup do Azure
Como o Backup do Azure é uma solução de backup híbrida, ele consiste em vários componentes que trabalham juntos para habilitar o backup de ponta a ponta e restaurar fluxos de trabalho.

![Componentes do Backup do Azure](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Cenários de implantação

| Componente | Pode ser implantado no Azure? | Pode ser implantado localmente? | Armazenamento de destino com suporte|
| --- | --- | --- | --- |
| Agente de Backup do Azure | <p>**Sim**</p> <p>O agente do Backup do Azure pode ser implantando em qualquer VM do Windows Server em execução no Azure.</p> | <p>**Sim**</p> <p>O agente do Backup do Azure pode ser implantando em qualquer VM do Windows Server ou computador físico.</p> | Criar cofre de backup do Azure |
| System Center Data Protection Manager (SCDPM) | <p>**Sim**</p> <p>Saiba mais sobre [como proteger cargas de trabalho no Azure usando o SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Sim**</p> <p>Saiba mais sobre [como proteger cargas de trabalho e VMs no seu datacenter](https://technet.microsoft.com/library/hh758173.aspx). | Disco anexado localmente,</p> <p>cofre de Backup do Azure,</p> <p>Fita (apenas localmente)</p> |
| Servidor de Backup do Azure | <p>**Sim**</p> <p>Saiba mais sobre [como proteger cargas de trabalho no Azure usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>**Sim**</p> <p>Saiba mais sobre [como proteger cargas de trabalho no Azure usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> | Criar cofre de backup do Azure |
| Backup do Azure (extensão VM) | <p>Sim</p> <p>Especializado no [backup de máquinas virtuais de IaaS do Azure](backup-azure-vms-introduction.md).</p> | <p>**Não**</p> <p>Use o SCDPM para fazer backup das máquinas virtuais em seu datacenter. | Cofre de Backup do Azure</p> |

## Aplicativos e cargas de trabalho

| Carga de trabalho | Máquina de origem | Solução de Backup do Azure |
| --- | --- |---|
| Arquivos e pastas | Windows Server | <p>[Agente de backup do Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Arquivos e pastas | Windows Client | <p>[Agente de backup do Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquina virtual do Hyper-V (Windows) | Windows Server | <p>System Center DPM</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquina virtual do Hyper-V (Linux) | Windows Server | <p>System Center DPM</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| VMs de IaaS do Azure (Windows)| - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) |
| VMs de IaaS do Azure (Linux) | - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) |

## Funcionalidade
Estas tabelas resumem como a funcionalidade do Backup do Azure é tratada em cada componente:

### Armazenamento

| Recurso | Agente de Backup do Azure | SCDPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Criar cofre de backup do Azure | ![Sim][green] | ![Sim][green] | ![Sim][green] | ![Sim][green] |
| Armazenamento em disco | | ![Sim][green] | ![Sim][green] | |
| Armazenamento em fita | | ![Sim][green] | | |
| Compactação (no cofre de backup) | ![Sim][green] | ![Sim][green]| ![Sim][green] | |
| Backup incremental | ![Sim][green] | ![Sim][green] | ![Sim][green] | ![Sim][green] |
| Eliminação de duplicação de disco | | ![Parcialmente][yellow] | ![Parcialmente][yellow]| | |

O cofre de Backup do Azure é o destino de armazenamento preferencial em todos os componentes. O SCDPM e o Servidor de Backup do Azure oferecem a opção de ter também uma cópia de disco local, mas apenas o SCDPM fornece a opção de gravar dados em um dispositivo de armazenamento em fita.

#### Backup incremental
Independentemente do armazenamento de destino (disco, fita, cofre de backup), todos os componentes dão suporte a backups incrementais. Isso garante que os backups lidem de forma eficiente com o armazenamento e o tempo, incluindo apenas as alterações incrementais desde o último backup e transferindo-as para o armazenamento de destino. Além disso, os backups são compactados para reduzir o volume de armazenamento.

>[AZURE.NOTE]O componente que não faz compactação é a extensão de VM. Todos os dados de backup são copiados da conta de armazenamento do cliente para o cofre de backup na mesma região, sem compactação. Embora isso aumente um pouco o armazenamento consumido, o armazenamento dos dados sem compactação possibilita tempos de restauração mais rápidos.

#### Eliminação de duplicação
Há suporte à eliminação de duplicação para o SCDPM e o Servidor de Backup do Azure quando [implantados em uma máquina virtual Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). A eliminação de duplicação é executada no nível do host, aproveitando o recurso de Eliminação de Duplicação do Windows Server, nos VHDs conectados como armazenamento de backup para a máquina virtual.

>[AZURE.WARNING]A eliminação de duplicação não está disponível no Azure para nenhum dos componentes do Backup do Azure! Quando o SCDPM e o Servidor de Backup do Azure são implantados no Azure, os discos de armazenamento conectados à VM não podem ser submetidos à eliminação de duplicação.

### Segurança

| Recurso | Agente de Backup do Azure | SCDPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Segurança de rede (para o Azure) | ![Sim][green] |![Sim][green] | ![Sim][green] | ![Parcialmente][yellow]|
| Segurança de rede (no Azure) | ![Sim][green] |![Sim][green] | ![Sim][green] | ![Parcialmente][yellow]|

Todo o tráfego de backup de seus servidores para o cofre de Backup do Azure é criptografado usando AES256, e os dados são enviados por um link HTTPS seguro. Os dados de backup também são armazenados no cofre de backup em sua forma criptografada. Somente o cliente armazena a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

>[AZURE.WARNING]A chave usada para criptografar os dados de backup está presente apenas com o cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Para o backup de VMs do Azure, você deve configurar explicitamente a criptografia *dentro* da máquina virtual. Use o BitLocker em máquinas virtuais do Windows e dm-cript em máquinas virtuais do Linux. O Backup do Azure não criptografa automaticamente dados de backup provenientes desse caminho.

### Cargas de trabalho com suporte

| Recurso | Agente de Backup do Azure | SCDPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Computador com Windows Server - arquivos/pastas | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Computador de cliente do Windows - arquivos/pastas | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Máquina virtual do Hyper-V (Windows) | | ![Sim][green] | ![Sim][green] | |
| Máquina virtual do Hyper-V (Linux) | | ![Sim][green] | ![Sim][green] | |
| Microsoft SQL Server | | ![Sim][green] | ![Sim][green] | |
| Microsoft SharePoint | | ![Sim][green] | ![Sim][green] | |
| Microsoft Exchange | | ![Sim][green] | ![Sim][green] | |
| Máquina virtual do Azure (Windows) | | | | ![Sim][green] |
| Máquina virtual do Azure (Linux) | | | | ![Sim][green] |

### Rede

| Recurso | Agente de Backup do Azure | SCDPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Compactação de rede (para o servidor de backup) | | ![Sim][green] | ![Sim][green] | |
| Compactação de rede (para o cofre de backup) | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Protocolo de rede (para o servidor de backup) | | TCP | TCP | |
| Protocolo de rede (para o cofre de backup) | HTTPS | HTTPS | HTTPS | HTTPS |

Como a extensão de VM lê diretamente os dados da conta de armazenamento do Azure através da rede de armazenamento, não é necessário otimizar esse tráfego. O tráfego passa pela rede de armazenamento local no controlador de domínio do Azure. Há pouca necessidade de compactação devido a considerações de largura de banda.

Para clientes que protegem dados para um servidor de backup (SCDPM ou Servidor de Backup do Azure), o tráfego do servidor primário para o servidor de backup também pode ser compactado para poupar a utilização de largura de banda.

### Backup e retenção

| Frequência de backup (para o cofre do Azure) | Três backups por dia | Dois backups por dia | Um backup por dia |
| --- | --- | --- | --- |
| Frequência de backup (em disco) | Não aplicável | <p>A cada 15 minutos para o SQL Server</p> <p>A cada hora para outras cargas de trabalho</p> | Não aplicável |
| Opções de retenção | Diária, semanal, mensal, anual | Diária, semanal, mensal, anual | Diária, semanal, mensal, anual |
| Período de retenção | Até 99 anos | Até 99 anos | Até 99 anos |
| Pontos de recuperação no cofre do Azure | ilimitado | ilimitado | ilimitado |
| Pontos de recuperação no disco local | Não aplicável | Não aplicável | Não aplicável |
| Pontos de recuperação em fita | Não aplicável | Não aplicável | Não aplicável |

## Qual a diferença entre o Backup do Azure e o Azure Site Recovery?
Muitos clientes confundem backup e recuperação de desastre. Ambos capturam dados e fornecem semântica de restauração, mas a proposta de valor principal é diferente para cada um deles.

O Backup do Azure faz backup de dados no local ou na nuvem. O Azure Site Recovery coordena a replicação, o failover e o failback de servidores físicos e máquinas virtuais. Você precisa de ambos para ter uma solução completa de recuperação de desastre, pois sua estratégia de recuperação de desastre precisa manter os dados seguros e recuperáveis (Backup do Azure) E manter as cargas de trabalho disponíveis e acessíveis (Azure Site Recovery) quando ocorrem interrupções.

Para tomar decisões sobre backup e recuperação de desastre, é importante compreender alguns conceitos importantes:

| CONCEITO | DETALHES | BACKUP | RECUPERAÇÃO DE DESASTRE (DR) |
| ------- | ------- | ------ | ----------------- |
| Objetivo de Ponto de Recuperação (OPR) | A quantidade de perda de dados que é aceitável caso uma recuperação precise ser feita. | As soluções de backup têm uma grande variação no RPO que é aceitável. Os backups de máquina virtual geralmente têm um RPO de um dia, enquanto os backups de banco de dados têm RPOs de até 15 minutos. | As soluções de recuperação de desastre têm RPOs extremamente baixos. A cópia de DR pode ter atraso de alguns segundos ou minutos. |
| Objetivo de tempo de recuperação (RTO) | O tempo necessário para concluir uma recuperação/restauração. | Devido ao RPO maior, a quantidade de dados que uma solução de backup precisa processar normalmente é muito maior, e isso leva a RTOs mais longos. Por exemplo, a restauração de dados de fitas pode levar dias, dependendo do tempo necessário para transportar a fita de um local externo. | As soluções de recuperação de desastre têm RTOs menores quando estão mais sincronizadas com a origem e menos alterações precisam processadas. |
| Retenção | Por quanto tempo os dados precisam ser armazenados | <p>Para cenários que exigem recuperação operacional (corrupção de dados, exclusão acidental de arquivos, falhas no sistema operacional), os dados de backup normalmente são retidos por 30 dias ou menos.</p> <p>Do ponto de vista de conformidade, talvez os dados precisem ser armazenados por meses ou até mesmo anos. Nesses casos, os dados de backup são ideais para arquivamento.</p> | A recuperação de desastre precisa apenas de dados de recuperação operacional: normalmente, algumas horas ou até um dia. Devido à captura de dados refinada usada em soluções de recuperação de desastre, a retenção de longo prazo não é recomendada com o uso de dados de recuperação de desastre. |

## Próximas etapas

- [Teste o Backup do Azure](backup-try-azure-backup-in-10-mins.md)
- As perguntas frequentes sobre o serviço de Backup do Azure estão listadas [aqui](backup-azure-backup-faq.md).
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!----HONumber=AcomDC_0114_2016-->