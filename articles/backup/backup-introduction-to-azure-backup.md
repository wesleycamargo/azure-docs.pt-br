<properties
	pageTitle="O que é o Backup do Azure? | Microsoft Azure"
	description="Com o Backup e os Serviços de Recuperação do Azure, você pode fazer backup e restaurar dados e aplicativos de Windows Servers, máquinas de cliente Windows, servidores com System Center DPM e máquinas virtuais do Azure."
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
	ms.date="04/06/2016"
	ms.author="jimpark; trinadhk"/>

# O que é o Backup do Azure?
O Backup do Azure é o serviço que você usa para fazer backup e restaurar os dados na nuvem da Microsoft. Ele substitui a solução de backup local ou externa existente por uma solução confiável, segura e econômica baseada em nuvem. Além disso, ajuda a proteger os ativos executados em nuvem. O Backup do Azure fornece serviços de recuperação integrados a uma infraestrutura de primeiro nível escalonável, duradoura e altamente disponível.

[Assista a uma visão geral em vídeo do Backup do Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Por que usar o Backup do Azure?
As soluções tradicionais de backup evoluíram para tratar a nuvem como um ponto de extremidade semelhante a um disco ou fita. Embora essa abordagem seja simples, ela também é limitada. Ele não aproveita plenamente uma plataforma de nuvem subjacente, o que se traduz em uma solução ineficiente e cara. Por outro lado, o Backup do Azure oferece todas as vantagens de uma solução de backup de nuvem poderosa e acessível. Veja alguns dos principais benefícios fornecidos pelo Backup do Azure:

| Recurso | Benefício |
| ------- | ------- |
| Gerenciamento de armazenamento automático | Não são necessárias despesas de capital para dispositivos de armazenamento locais. O Backup do Azure aloca e gerencia automaticamente o armazenamento de backup, com um modelo de consumo de pagamento conforme o uso. |
| Dimensionamento ilimitado | Aproveite as garantias de alta disponibilidade, sem a sobrecarga de manutenção e monitoramento. O Backup do Azure usa a capacidade e a escala subjacentes da nuvem do Azure, com recursos não intrusivos de dimensionamento automático. |
| Várias opções de armazenamento | Escolha seu armazenamento de backup de acordo com a necessidade:<li>um blob de blocos de armazenamento com redundância local é ideal para os clientes preocupados com o preço e ainda protege os dados contra falhas de hardware local. <li>Um blob de blocos de armazenamento com replicação geográfica fornece mais três cópias em um datacenter emparelhado. As cópias adicionais ajudam a garantir que os dados de backup sejam altamente disponíveis, mesmo se ocorrer um desastre de nível de site do Azure. |
| Transferência de dados ilimitada | Não há cobrança para transferências de dados de saída durante uma operação de restauração do cofre do Backup. Os dados de entrada para o Azure também são gratuitos. Funciona com o serviço de importação onde está disponível. |
| Criptografia de dados | A criptografia de dados permite a transmissão segura e o armazenamento de dados do cliente na nuvem pública. A senha de criptografia é armazenada na origem e nunca é transmitida nem armazenada no Azure. A chave de criptografia é necessária para restaurar quaisquer dados e somente o cliente tem acesso completo aos dados no serviço. |  
| Backup consistente com o aplicativo | Os backups consistentes com aplicativos no Windows ajudam a garantir que não haja necessidade de correções no momento da restauração, o que reduz o objetivo de tempo de recuperação. Isso permite que os clientes retornem ao estado de execução mais rapidamente. |
| Retenção de longo prazo | Em vez de pagar por soluções de backup em fita externa, os clientes podem fazer backup no Azure, o que fornece uma solução atraente e semelhante à fita a um baixo custo. |

## Componentes do Backup do Azure
Como o Backup é uma solução de backup híbrida, é formado por vários componentes que trabalham juntos para permitir fluxos completos de backup e restauração.

![Componentes do Backup do Azure](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

### Cenários de implantação

| Componente | Pode ser implantado no Azure? | Pode ser implantado localmente? | Armazenamento de destino com suporte|
| --- | --- | --- | --- |
| Agente de Backup do Azure | <p>**Sim**</p> <p>O agente de Backup do Azure pode ser implantado em qualquer VM com Windows Server em execução no Azure.</p> | <p>**Sim**</p> <p>O agente de Backup pode ser implantado em qualquer VM com Windows Server ou máquina física.</p> | <p>Cofre de Backup do Azure</p> |
| System Center Data Protection Manager (DPM) | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho no Azure usando o System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho e as VMs em seu datacenter](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Disco conectado localmente,</p> <p>cofre de Backup do Azure,</p> <p>fita (apenas local)</p> |
| Servidor de Backup do Azure | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho no Azure usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>**Sim**</p> <p>Saiba mais sobre [como proteger as cargas de trabalho no Azure usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>Disco conectado localmente,</p> <p>cofre de Backup do Azure</p> |
| Backup do Azure (extensão VM) | <p>**Sim**</p><p>Faz parte da malha do Azure</p><p>Especializado em [backup máquinas virtuais IaaS (infraestrutura do Azure como serviço)](backup-azure-vms-introduction.md).</p> | <p>**Não**</p> <p>Use o System Center DPM para fazer backup de máquinas virtuais em seu datacenter.</p> | <p>Cofre de backup do Azure</p> |

### Limitações e benefícios no nível de componente

| Componente | Benefícios | Limitações | Granularidade da recuperação |
| --- | --- | --- | --- |
| Agente de Backup do Azure (MARS) | <li>Pode fazer backup de arquivos e de pastas em um computador com o sistema operacional Windows, seja físico ou virtual (as VMs podem estar em qualquer lugar no local ou no Azure)<li>Nenhum servidor de backup separado necessário<li>Usa o Cofre de Backup do Azure | <li>Backup/restauração no nível de arquivo três <li>Restauração somente no nível de arquivo/pasta/volume, sem reconhecimento de aplicativo<li>Não há suporte para Linux | arquivos/pastas/volumes |
| System Center Data Protection Manager | <li>Instantâneos com reconhecimento de aplicativo (VSS)<li>Flexibilidade total para quando forem feitos backups<li>Granularidade de recuperação (todos)<li>Pode usar o Cofre de Backup Azure<li>Suporte para Linux (se hospedada no Hyper-V) | <li>Falta de suporte heterogêneo (backup de VM VMware, backup de cargas de trabalho do Oracle). | arquivos/pastas/volumes<br>/VMs/aplicativos |
| Servidor de Backup do Microsoft Azure | <li>Instantâneos com reconhecimento de aplicativo (VSS)<li>Flexibilidade total para quando forem feitos backups<li>Granularidade de recuperação (todos)<li>Pode usar o Cofre de Backup Azure<li>Suporte a Linux (se hospedado no Hyper-V)<li>Não exige uma licença do System Center | <li>Falta de suporte heterogêneo (backup de VM VMware, backup de cargas de trabalho do Oracle).<li>Sempre requer assinatura dinâmica do Azure<li>Não há suporte para backup em fita | arquivos/pastas/volumes<br>/VMs/aplicativos |
| Backup de VM IaaS do Azure | <li>Backups nativos para Windows/Linux<li>Nenhuma instalação de agente específico necessária<li>Backup em nível de malha com nenhuma infraestrutura de backup necessária | <li>Backup/restauração no nível do disco uma vez por dia<li>Não pode fazer backup no local | VMs<br>Todos os discos (usando o PowerShell) |

## Quais aplicativos e cargas de trabalho podem passar por backup?

| Carga de trabalho | Máquina de origem | Solução de Backup do Azure |
| --- | --- |---|
| Arquivos e pastas | Windows Server | <p>[Agente de Backup do Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (mais o agente do Backup do Azure)</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| Arquivos e pastas | Windows Client | <p>[Agente de Backup do Azure](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (mais o agente do Backup do Azure)</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| Máquina virtual do Hyper-V (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (mais o agente do Backup do Azure),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| Máquina virtual do Hyper-V (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (mais o agente do Backup do Azure),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (mais o agente do Backup do Azure),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (mais o agente do Backup do Azure),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (mais o agente do Backup do Azure),</p> <p>[Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente de Backup do Azure)</p> |
| VMs de IaaS do Azure (Windows) | - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) | | VMs de IaaS do Azure (Linux) | - | [Backup do Azure (extensão de VM)](backup-azure-vms-introduction.md) |

## Suporte para ARM e para Linux

| Componente | Suporte a ARM | Suporte para Linux (endossado pelo Azure) |
| --- | --- | --- |
| Agente de Backup do Azure (MARS) | Sim | Não (somente agente baseado no Windows) |
| System Center Data Protection Manager | Sim (agente em convidado) | Somente Hyper-V (VM que não seja do Azure) Somente o backup consistente com arquivo é possível |
| Servidor de Backup do Azure (MABS) | Sim (agente em convidado) | Somente Hyper-V (VM que não seja do Azure) Somente o backup consistente com arquivo é possível (igual ao DPM) |
| Backup de VM IaaS do Azure | Em visualização pública | Em visualização pública - VMs do Linux no modelo de implantação do Gerenciador de Recursos <br>(consistência no nível do sistema de arquivos)<br><br>Sim para VMs do Linux no modelo de implantação clássico |

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

## Funcionalidade
Estas cinco tabelas resumem como a funcionalidade de backup é tratada em cada componente:

### Armazenamento

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Criar cofre de backup do Azure | ![Sim][green] | ![Sim][green] | ![Sim][green] | ![Sim][green] |
| Armazenamento em disco | | ![Sim][green] | ![Sim][green] | |
| Armazenamento em fita | | ![Sim][green] | | |
| Compactação (no cofre de backup) | ![Sim][green] | ![Sim][green]| ![Sim][green] | |
| Backup incremental | ![Sim][green] | ![Sim][green] | ![Sim][green] | ![Sim][green] |
| Eliminação de duplicação de disco | | ![Parcialmente][yellow] | ![Parcialmente][yellow]| | |

![chave de tabela](./media/backup-introduction-to-azure-backup/table-key.png)

O cofre de Backup é o destino de armazenamento preferencial em todos os componentes. O System Center DPM e o Servidor de Backup também oferecem a opção de ter uma cópia do disco local. No entanto, somente o System Center DPM fornece a opção de gravar dados em um dispositivo de armazenamento em fita.

#### Backup incremental
Cada componente dá suporte a backup incremental, independentemente do armazenamento de destino (disco, fita, cofre de backup). O backup incremental garante que os backups sejam eficientes em termos de tempo e armazenamento, transferindo somente as alterações feitas desde o último backup.

#### Compactação
Os backups são compactados para reduzir o espaço de armazenamento necessário. O único componente que não usa a compactação é a extensão de VM. Com a extensão de VM, todos os dados de backup são copiados da conta de armazenamento do cliente para o cofre de backup na mesma região, sem compactação. Embora a ausência de compactação aumente um pouco o armazenamento usado, armazenar os dados sem compactação possibilita tempos de restauração mais rápidos.

#### Eliminação de duplicação
Há suporte à eliminação de duplicação para o System Center DPM e o Servidor de Backup quando são [implantados em uma máquina virtual Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). A eliminação de duplicação é executada no nível do host, usando a eliminação de duplicação do Windows Server em VHDs (discos rígidos virtuais) conectados como armazenamento de backup às máquinas virtuais.

>[AZURE.WARNING] A eliminação de duplicação não está disponível no Azure para nenhum dos componentes do Backup. Quando o System Center DPM e o Servidor de Backup são implantados no Azure, os discos de armazenamento conectados à VM não podem ser submetidos à eliminação de duplicação.

### Segurança

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Segurança de rede (para o Azure) | ![Sim][green] |![Sim][green] | ![Sim][green] | ![Parcialmente][yellow]|
| Segurança de rede (no Azure) | ![Sim][green] |![Sim][green] | ![Sim][green] | ![Parcialmente][yellow]|

![chave de tabela](./media/backup-introduction-to-azure-backup/table-key.png)

Todo tráfego de backup de seus servidores para o cofre do Backup é criptografado usando a criptografia AES (Advanced Encryption Standard) 256. Os dados são enviados por um link HTTPS seguro. Os dados de backup também são armazenados no cofre de backup na forma criptografada. Somente o cliente possui a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

>[AZURE.WARNING] A chave usada para criptografar os dados de backup está presente apenas com o cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Fazer backup de VMs do Azure requer a configuração da criptografia *na* máquina virtual. Use o BitLocker em máquinas virtuais do Windows e **dm-crypt** em máquinas virtuais Linux. O Backup do Azure não criptografa automaticamente dados de backup provenientes desse caminho.

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

![chave de tabela](./media/backup-introduction-to-azure-backup/table-key-2.png)

### Rede

| Recurso | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| ------- | --- | --- | --- | ---- |
| Compactação de rede (para o servidor de backup) | | ![Sim][green] | ![Sim][green] | |
| Compactação de rede (para o cofre de backup) | ![Sim][green] | ![Sim][green] | ![Sim][green] | |
| Protocolo de rede (para o servidor de backup) | | TCP | TCP | |
| Protocolo de rede (para o cofre de backup) | HTTPS | HTTPS | HTTPS | HTTPS |

![chave de tabela](./media/backup-introduction-to-azure-backup/table-key-2.png)

Como a extensão da VM lê os dados diretamente da conta de armazenamento do Azure por meio da rede de armazenamento, não é necessário otimizar esse tráfego. O tráfego passa pela rede de armazenamento local no datacenter do Azure, portanto, há pouca necessidade de compactação devido a considerações de largura de banda.

Se você estiver fazendo backup de seus dados em um servidor de backup (DPM ou Servidor de Backup), o tráfego do servidor primário para o servidor de backup poderá ser compactado para economizar largura de banda.

#### Limitação de rede
O agente de Backup do Azure fornece o recurso de limitação, que permite controlar como a largura de banda é usada durante a transferência de dados. A limitação pode ser útil se você precisa fazer backup de dados durante o horário de expediente, mas não quer que o processo de backup interfira no outro tráfego de Internet. A limitação da transferência de dados aplica-se a atividades de backup e restauração.

### Backup e retenção

| | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup do Azure (extensão VM) |
| --- | --- | --- | --- | --- |
| Frequência de backup (para o cofre de backup) | Três backups por dia | Dois backups por dia |Dois backups por dia | Um backup por dia |
| Frequência de backup (em disco) | Não aplicável | <p>A cada 15 minutos para o SQL Server</p> <p>A cada hora para outras cargas de trabalho</p> | <p>A cada 15 minutos para o SQL Server</p> <p>A cada hora para outras cargas de trabalho</p> |Não aplicável |
| Opções de retenção | Diária, semanal, mensal, anual | Diária, semanal, mensal, anual | Diária, semanal, mensal, anual |Diária, semanal, mensal, anual |
| Período de retenção | Até 99 anos | Até 99 anos | Até 99 anos | Até 99 anos |
| Pontos de recuperação no cofre do Backup | Ilimitado | Ilimitado | Ilimitado | Ilimitado |
| Pontos de recuperação no disco local | Não aplicável | 64 para Servidores de Arquivos,<br><br>448 para Servidores de Aplicativos | 64 para Servidores de Arquivos,<br><br>448 para Servidores de Aplicativos |Não aplicável |
| Pontos de recuperação em fita | Não aplicável | Ilimitado | Não aplicável | Não aplicável |

## O que é o arquivo de credencial do cofre?

O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup. O portal, em seguida, carrega a chave pública no ACS (Serviço de Controle de Acesso). A chave privada é fornecida ao usuário ao baixar as credenciais e, em seguida, inserida durante o registro do computador. A chave privada autentica o computador para enviar os dados de backup a um cofre identificado no serviço de Backup do Azure.

As credenciais do cofre são usadas somente durante o fluxo de trabalho de registro. É sua responsabilidade garantir que o arquivo de credenciais do cofre não seja comprometido. Se esse arquivo acabar em poder de qualquer usuário não autorizado, o arquivo de credenciais do cofre poderá ser usado para registrar outros computadores no mesmo cofre. No entanto, como os dados de backup são criptografados usando uma senha que pertence apenas ao cliente, os dados de backup existentes não podem ser comprometidos. Para reduzir esse problema, as credenciais do cofre são definidas para expirar em 48 horas. Embora você possa baixar várias vezes as credenciais de um cofre de backup, apenas o último arquivo é aplicável durante o fluxo de trabalho de registro.

## Qual a diferença entre o Backup do Azure e o Azure Site Recovery?
Muitos clientes confundem recuperação de backup e recuperação de desastre. Ambos capturam dados e fornecem semântica de restauração, mas a proposta principal é diferente para cada um deles.

O Backup do Azure faz backup de dados no local e na nuvem. O Azure Site Recovery coordena a replicação, o failover e o failback de servidores físicos e máquinas virtuais. Ambos os serviços são importantes, pois a solução de recuperação de desastre precisa manter os dados seguros e recuperáveis (Backup) *e* manter as cargas de trabalho disponíveis (Recuperação de Site) quando ocorrem falhas.

Os conceitos a seguir o ajudam a tomar decisões importantes sobre backup e recuperação de desastre.

| Conceito | Detalhes | Backup | DR (Recuperação de desastre) |
| ------- | ------- | ------ | ----------------- |
| RPO (Objetivo de Ponto de Recuperação) | A quantidade de perda de dados aceitável se uma recuperação precisar ser feita. | Há uma grande variação do RPO aceitável entre as soluções de backup. Os backups de máquina virtual geralmente têm um RPO de um dia, enquanto os backups de banco de dados têm RPOs de até 15 minutos. | Soluções de recuperação de desastre têm RPOs baixos. A cópia de DR pode ter um atraso de alguns segundos ou minutos. |
| RTO (Objetivo de Tempo de Recuperação) | O tempo necessário para concluir uma recuperação ou restauração. | Devido ao RPO maior, a quantidade de dados que uma solução de backup precisa processar normalmente é muito maior, o que leva a RTOs mais longos. Por exemplo, a restauração de dados de fitas pode demorar dias, dependendo do tempo necessário para transportar a fita de um local externo. | As soluções de recuperação de desastre têm RTOs menores, pois estão mais sincronizadas com a origem. Uma quantidade menor de alterações precisa ser processada. |
| Retenção | Por quanto tempo os dados precisam ser armazenados | Para cenários que exigem recuperação operacional (dados corrompidos, exclusão acidental de arquivos, falhas no sistema operacional), os dados de backup normalmente são retidos por 30 dias ou menos.<br>De um ponto de vista de conformidade, talvez os dados tenham de ser armazenados por meses ou até por anos. Nesses casos, os dados de backup são ideais para arquivamento. | A recuperação de desastre precisa apenas de dados de recuperação operacional, o que normalmente leva algumas horas ou até um dia. Devido à captura de dados refinada usada em soluções de DR, não recomendamos o uso de dados de DR para a retenção de longo prazo. |

## Próximas etapas

Experimente uma simples operação de Backup do Azure. Para obter instruções, consulte um destes tutoriais:

- [Teste o Backup do Azure](backup-try-azure-backup-in-10-mins.md)
- [Teste o Backup de VM do Azure](backup-azure-vms-first-look.md)

Como esses tutoriais ajudam a fazer backup rapidamente, eles mostram somente o caminho mais direto para fazer backup de seus dados. Para obter informações adicionais sobre o tipo de backup que você deseja fazer, confira:

- [Fazer backup de computadores Windows](backup-configure-vault.md)
- [Fazer backup de cargas de trabalho do aplicativo](backup-azure-microsoft-azure-backup.md)
- [Backup de VM IaaS do Azure](backup-azure-vms-prepare.md)



[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0413_2016-->