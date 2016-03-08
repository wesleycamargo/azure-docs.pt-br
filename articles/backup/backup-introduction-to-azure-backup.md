<properties
	pageTitle="O que é o Backup do Azure? | Microsoft Azure"
	description="Com o Backup do Azure e os serviços de recuperação, você pode fazer backup e restaurar dados e aplicativos de Windows Servers, máquinas de cliente Windows, servidores com System Center DPM e máquinas virtuais do Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="backup e restauração; serviços de recuperação; soluções de backup"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/02/2016"
	ms.author="trinadhk;jimpark"/>

# O que é o Backup do Azure?
O Backup do Azure é o serviço que você usa para fazer backup e restaurar os dados na nuvem da Microsoft. Ele substitui a solução de backup local ou externa existente por uma solução confiável, segura e econômica baseada em nuvem. Além disso, ajuda a proteger os ativos executados em nuvem. O Backup do Azure fornece serviços de recuperação integrados a uma infraestrutura de primeiro nível escalonável, duradoura e altamente disponível.

[Assista a uma visão geral em vídeo do Backup do Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Por que usar o Backup do Azure?
As soluções tradicionais de backup evoluíram para tratar a nuvem como um ponto de extremidade semelhante a um disco ou fita. Embora essa abordagem seja simples, ela também é limitada. Ela não aproveita totalmente uma plataforma de nuvem subjacente. Isso se traduz em uma solução ineficiente e cara. Por outro lado, o Backup do Azure oferece todas as vantagens de uma solução de backup na nuvem eficiente e econômica. Veja alguns dos principais benefícios fornecidos pelo Backup do Azure:

| Recurso | Benefício |
| ------- | ------- |
| Gerenciamento de armazenamento automático | Não são necessárias despesas de capital para dispositivos de armazenamento locais. O Backup do Azure aloca e gerencia automaticamente o armazenamento de backup, com um modelo de consumo de pagamento conforme o uso. |
| Dimensionamento ilimitado | Aproveite as garantias de alta disponibilidade, sem a sobrecarga de manutenção e monitoramento. O Backup do Azure usa a capacidade e a escala subjacentes da nuvem do Azure, com recursos não intrusivos de dimensionamento automático. |
| Várias opções de armazenamento | Escolha seu armazenamento de backup de acordo com a necessidade: <li>um blob de blocos de armazenamento com redundância local é ideal para os clientes preocupados com o preço, e ainda protege os dados contra falhas de hardware local. <li>Um blob de blocos de armazenamento com replicação geográfica fornece três cópias adicionais em um datacenter emparelhado. Isso ajuda a garantir que os dados de seu backup estejam altamente disponíveis, mesmo se ocorrer um desastre no nível do site do Azure. |
| Transferência de dados ilimitada | Não há cobrança para transferências de dados de saída durante uma operação de restauração do cofre do Backup. Os dados de entrada para o Azure também são gratuitos. |
| Gerenciamento central | O portal do Azure fornece simplicidade e familiaridade. À medida que o serviço evolui, recursos como o gerenciamento centralizado permitirão que você gerencie sua infraestrutura de backup em um único local. |
| Criptografia de dados. | Isso permite uma transmissão segura e o armazenamento de dados do cliente na nuvem pública. A senha de criptografia é armazenada na origem e nunca é transmitida nem armazenada no Azure. A chave de criptografia é necessária para restaurar quaisquer dados e somente o cliente tem acesso completo aos dados no serviço. |  
| Backup consistente com o aplicativo | Os backups consistentes com aplicativos no Windows ajudam a garantir que não haja necessidade de correções no momento da restauração. Isso reduz o objetivo do tempo de recuperação e permite que os clientes retornem a um estado de execução mais depressa. |
| Retenção de longo prazo | Em vez de pagar por soluções de backup em fita externas, os clientes podem fazer backup no Azure. Isso fornece uma solução atraente com semântica semelhante à fita por um custo muito baixo. |

## Componentes do Backup do Azure
Como o Backup é uma solução de backup híbrida, é formado por vários componentes que trabalham juntos para permitir fluxos completos de backup e restauração.

![Componentes do Backup do Azure](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Cenários de implantação

| Componente | Pode ser implantado no Azure? | Pode ser implantado localmente? | Armazenamento de destino com suporte|
| --- | --- | --- | --- |
| Agente de Backup do Azure | <p>**Sim**</p> <p>O agente Backup do Azure pode ser implantado em qualquer VM com Windows Server em execução no Azure.</p> | <p>**Sim**</p> <p>O agente Backup pode ser implantado em qualquer VM com Windows Server na máquina física.</p> | <p>Cofre de Backup do Azure</p> |
| System Center Data Protection Manager (DPM) | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho no Azure usando o System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho e VMs em seu datacenter](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Disco conectado localmente,</p> <p>Cofre do Backup do Azure,</p> <p>fita (apenas local)</p> |
| Servidor de Backup do Azure | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho no Azure usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho no Azure usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>Cofre de Backup do Azure</p> |
| Backup do Azure (extensão VM) | <p>Sim</p> <p>Especializado para [backup de máquinas virtuais IaaS (infraestrutura do Azure como serviço)](backup-azure-vms-introduction.md).</p> | <p>**Não**</p> <p>Use o System Center DPM para fazer backup de máquinas virtuais em seu datacenter.</p> | <p>Cofre de Backup do Azure</p> |

## Quais aplicativos e cargas de trabalho podem passar por backup?

| Carga de trabalho | Máquina de origem | Solução de Backup do Azure |
| --- | --- |---|
| Arquivos e pastas | Windows Server | <p>[Agente de backup do Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Arquivos e pastas | Windows Client | <p>[Agente de backup do Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquina virtual do Hyper-V (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Máquina virtual do Hyper-V (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</p> |
| VMs de IaaS do Azure (Windows)| - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) | | VMs de IaaS do Azure (Linux) | - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) |

## Funcionalidade
Estas cinco tabelas resumem como a funcionalidade do Backup é tratada em cada componente:

### Armazenamento

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Criar cofre de backup do Azure | ![Sim][green] | ![Sim][green] | ![Sim][green] | ![Sim][green] |
| Armazenamento em disco | | ![Sim][green] | ![Sim][green] | |
| Armazenamento em fita | | ![Sim][green] | | |
| Compactação (no cofre de backup) | ![Sim][green] | ![Sim][green]| ![Sim][green] | |
| Backup incremental | ![Sim][green] | ![Sim][green] | ![Sim][green] | ![Sim][green] |
| Eliminação de duplicação de disco | | ![Parcialmente][yellow] | ![Parcialmente][yellow]| | |

**Chave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sim][green]= Com suporte &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Parcialmente][yellow]= Com suporte parcial &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Sem suporte

O cofre de Backup é o destino de armazenamento preferencial em todos os componentes. O System Center DPM e o Servidor de Backup também oferecem a opção de ter uma cópia no disco local, mas apenas o System Center DPM fornece a opção de gravar dados em um dispositivo de armazenamento em fita.

#### Backup incremental
Independentemente do armazenamento de destino (disco, fita, cofre de backup), todos os componentes dão suporte a backups incrementais. Isso ajuda a garantir que os backups lidem de forma eficiente com o armazenamento e o tempo, incluindo apenas as alterações incrementais desde o último backup e transferindo-as para o armazenamento de destino. Além disso, os backups são compactados para reduzir o volume de armazenamento.

O componente que não faz compactação é a extensão de VM. Todos os dados de backup são copiados da conta de armazenamento do cliente para o cofre de backup na mesma região, sem compactação. Embora isso aumente um pouco o armazenamento consumido, o armazenamento dos dados sem a compactação permite um tempo de restauração menor.

#### Eliminação de duplicação
Há suporte para a eliminação de duplicação para o System Center DPM e o Servidor de Backup quando são [implantados em uma máquina virtual Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). A eliminação de duplicação é executada no nível do host, aproveitando o recurso de eliminação de duplicação do Windows Server nos VHDs (discos rígidos virtuais) conectados como armazenamento de backup na máquina virtual.

>[AZURE.WARNING] A eliminação de duplicação não está disponível no Azure para nenhum dos componentes do Backup. Quando o System Center DPM e o Servidor de Backup são implantados no Azure, os discos de armazenamento conectados à VM não podem ser submetidos à eliminação de duplicação.

### Segurança

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Segurança de rede (para o Azure) | ![Sim][green] |![Sim][green] | ![Sim][green] | ![Parcialmente][yellow]|
| Segurança de rede (no Azure) | ![Sim][green] |![Sim][green] | ![Sim][green] | ![Parcialmente][yellow]|

**Chave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sim][green]= Com suporte &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Parcialmente][yellow]= Com suporte parcial &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Sem suporte

Todo tráfego de backup de seus servidores para o cofre do Backup é criptografado usando a criptografia AES (Advanced Encryption Standard) 256. Os dados são enviados por um link HTTPS seguro. Os dados de backup também são armazenados no cofre de backup na forma criptografada. Somente o cliente possui a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

>[AZURE.WARNING] A chave usada para criptografar os dados de backup está presente apenas com o cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Para o backup de VMs do Azure, você deve configurar explicitamente a criptografia *dentro* da máquina virtual. Use o BitLocker em máquinas virtuais do Windows e **dm-cript** em máquinas virtuais do Linux. O Backup do Azure não criptografa automaticamente dados de backup provenientes desse caminho.

### Cargas de trabalho com suporte

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Máquinas com Windows Server--arquivos e pastas | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Máquina com cliente Windows--arquivos e pastas | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Máquina virtual do Hyper-V (Windows) | | ![Sim][green] | ![Sim][green] | |
| Máquina virtual do Hyper-V (Linux) | | ![Sim][green] | ![Sim][green] | |
| Microsoft SQL Server | | ![Sim][green] | ![Sim][green] | |
| Microsoft SharePoint | | ![Sim][green] | ![Sim][green] | |
| Microsoft Exchange | | ![Sim][green] | ![Sim][green] | |
| Máquina virtual do Azure (Windows) | | | | ![Sim][green] |
| Máquina virtual do Azure (Linux) | | | | ![Sim][green] |

**Chave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sim][green]= Com suporte &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Sem suporte

### Rede

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Compactação de rede (para o servidor de backup) | | ![Sim][green] | ![Sim][green] | |
| Compactação de rede (para o cofre de backup) | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Protocolo de rede (para o servidor de backup) | | TCP | TCP | |
| Protocolo de rede (para o cofre de backup) | HTTPS | HTTPS | HTTPS | HTTPS |

**Chave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sim][green]= Com suporte &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;blank&gt;* = Sem suporte

Como a extensão da VM lê os dados diretamente da conta de armazenamento do Azure por meio da rede de armazenamento, não é necessário otimizar esse tráfego. O tráfego passa pela rede de armazenamento local no datacenter do Azure, portanto, há pouca necessidade de compactação devido a considerações de largura de banda.

Para clientes que protegem seus dados em um servidor de backup (System Center DPM ou Servidor de Backup), o tráfego do servidor primário para o servidor de backup também pode ser compactado para economizar largura de banda.

### Backup e retenção

| | Agente de Backup do Azure | System Center DPM e Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| --- | --- | --- | --- |
| Frequência de backup (para o cofre de Backup) | Três backups por dia | Dois backups por dia | Um backup por dia |
| Frequência de backup (em disco) | Não aplicável | <p>A cada 15 minutos para SQL Server</p> <p>A cada hora para outras cargas de trabalho</p> | Não aplicável |
| Opções de retenção | Diária, semanal, mensal, anual | Diária, semanal, mensal, anual | Diária, semanal, mensal, anual |
| Período de retenção | Até 99 anos | Até 99 anos | Até 99 anos |
| Pontos de recuperação no cofre do Backup | Ilimitado | Ilimitado | Ilimitado |
| Pontos de recuperação no disco local | Não aplicável | Não aplicável | Não aplicável |
| Pontos de recuperação em fita | Não aplicável | Não aplicável | Não aplicável |

## O que é o arquivo de credencial do cofre?

O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup. O portal, em seguida, carrega a chave pública no ACS (Serviço de Controle de Acesso). A chave privada do certificado é disponibilizada para o usuário como parte do fluxo de trabalho que é fornecido como uma entrada no fluxo de trabalho de registro do computador. Isso autentica o computador para enviar os dados de backup para um cofre identificado no serviço de Backup do Azure.

As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro. É responsabilidade do usuário garantir que o arquivo de credenciais do cofre não seja comprometido. Se esse arquivo acabar em poder de qualquer usuário não autorizado, o arquivo de credenciais do cofre poderá ser usado para registrar outros computadores no mesmo cofre. No entanto, como os dados de backup são criptografados usando uma senha que pertence ao cliente, os dados de backup existentes não poderão ser comprometidos. Para atenuar esse problema, as credenciais do cofre são definidas para expirar em 48 horas. Você pode baixar as credenciais de um cofre de backup quantas vezes quiser – mas apenas o arquivo mais recente de credencial de cofre será aplicável durante o fluxo de trabalho de registro.

## Qual a diferença entre o Backup e o Azure Site Recovery?
Muitos clientes confundem recuperação de backup e recuperação de desastre. Ambos capturam dados e fornecem semântica de restauração, mas a proposta principal é diferente para cada um deles.

O Backup do Azure faz backup de dados no local e na nuvem. O Azure Site Recovery coordena a replicação, o failover e o failback de servidores físicos e máquinas virtuais. Você precisa dos dois para ter uma solução de recuperação de desastres completa. A estratégia de recuperação de desastre precisa manter os dados seguros e recuperáveis (Backup) *e* manter suas cargas de trabalho disponíveis e acessíveis (Recuperação de Site) no momento da interrupção.

Para tomar decisões sobre backup e recuperação de desastre, é necessário entender os seguintes conceitos importantes:

| Conceito | Detalhes | Backup | DR (Recuperação de desastre) |
| ------- | ------- | ------ | ----------------- |
| RPO (Objetivo de Ponto de Recuperação) | A quantidade de perda de dados que é aceitável caso uma recuperação precise ser feita. | Há uma grande variação do RPO aceitável entre as soluções de backup. Os backups de máquina virtual geralmente têm um RPO de um dia, enquanto os backups de banco de dados têm RPOs de até 15 minutos. | As soluções de recuperação de desastre têm RPOs extremamente baixos. A cópia de DR pode ter um atraso de alguns segundos ou minutos. |
| RTO (Objetivo de Tempo de Recuperação) | O tempo necessário para concluir uma recuperação ou restauração. | Devido ao RPO maior, a quantidade de dados que uma solução de backup precisa processar normalmente é muito maior. Isso gera RTOs maiores. Por exemplo, a restauração de dados de fitas pode demorar dias, dependendo do tempo necessário para transportar a fita de um local externo. | As soluções de recuperação de desastre têm RTOs menores, pois estão mais sincronizadas com a origem. Uma quantidade menor de alterações precisa ser processada. |
| Retenção | Por quanto tempo os dados precisam ser armazenados | <p>Para cenários que exigem recuperação operacional (dados corrompidos, exclusão acidental de arquivos, falhas no sistema operacional), os dados de backup normalmente são retidos por 30 dias ou menos.</p> <p>Para fins de conformidade, talvez seja necessário armazenar os dados por meses ou até mesmo anos. Nesses casos, os dados de backup são ideais para arquivamento.</p> | A recuperação de desastres precisa apenas dos dados de recuperação operacionais. Isso geralmente demora poucas horas ou até um dia. Devido à captura de dados refinada usada em soluções de DR, não recomendamos o uso de dados de DR para a retenção de longo prazo. |


## Próximas etapas

- [Teste o Backup do Azure](backup-try-azure-backup-in-10-mins.md)
- [Perguntas frequentes sobre o serviço de Backup do Azure](backup-azure-backup-faq.md)
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0302_2016-->