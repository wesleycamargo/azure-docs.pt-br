---
title: Arquitetura de Backup do Azure
description: Fornece uma visão geral da arquitetura, componentes e processos usados pelo serviço de Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 4be483994bd7bc5bd97b1e59df230f66e9b4e24e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430332"
---
# <a name="azure-backup-architecture"></a>Arquitetura de Backup do Azure

Use o [serviço de Backup do Azure](backup-overview.md) para fazer backup de dados na nuvem do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos de Backup do Azure. 


## <a name="what-does-azure-backup-do"></a>O que o Backup do Azure faz?

O Backup do Azure faz backup de dados, do estado da máquina e das cargas de trabalho em execução nas máquinas locais e VMs do Azure. Há vários cenários de Backup do Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona o Backup do Azure?

Você pode fazer backup de computadores e dados usando vários métodos.

- **Fazer backup dos computadores locais**:
    - Você pode fazer backup de computadores Windows locais diretamente no Azure usando o agente MARS (Serviços de Recuperação do Microsoft Azure) do Backup do Azure. Computadores Linux não tem suporte.
    - Você pode fazer backup de computadores locais em um servidor de backup (System Center DPM (Data Protection Manager) ou MABS (Servidor de Backup do Microsoft Azure)) e, por outro lado, fazer backup do servidor de backup em um cofre dos Serviços de Recuperação do Backup do Azure no Azure.
- **Fazer backup de VMs do Azure**:
    - Você pode fazer backup de VMs do Azure diretamente. O Backup do Azure instala uma extensão de backup no agente de VM do Azure em execução na VM para fazer isso. Isso faz backup de toda a VM.
    - Você pode fazer backup de arquivos e pastas específicos na VM do Azure executando o agente MARS.
    - Você pode fazer backup de VMs do Azure no MABS em execução no Azure e, depois, fazer backup do MABS no cofre.

Saiba mais sobre [quais itens podem ser submetidos a backup](backup-overview.md) e [cenários de backup com suporte](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Onde os dados são armazenados em backup?

O Backup do Azure armazena os dados em um cofre dos Serviços de Recuperação. Um cofre é uma entidade de armazenamento online no Azure usada para armazenar dados como cópias de backup, pontos de recuperação e políticas de backup.

- Os cofres facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.
- Em cada assinatura do Azure, você pode criar até 500 cofres.
- É possível monitorar os itens submetidos a backup em um cofre, incluindo as máquinas locais e as VMs do Azure.
- É possível gerenciar o acesso ao cofre com o [controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do Azure.
- Você especifica como os dados no cofre são replicados para redundância:
    - **LRS**: você pode usar o LRS (armazenamento com redundância local) na proteção contra falhas em um datacenter. O LRS replica os dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: Você pode usar o GRS (armazenamento com redundância geográfica): na proteção contra interrupções em toda a região. Ele replica os dados em uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Por padrão, os cofres dos Serviços de Recuperação de Backup usam o GRS. 




### <a name="backup-agents"></a>Agentes de backup

O Backup do Azure fornece agentes diferentes, dependendo do tipo de backup.

**Agente** | **Detalhes** 
--- | --- 
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | 1) É executado em Servidores do Windows locais individuais para fazer backup de arquivos, pastas e estado do sistema<br/><br/> 2) É executado em VMs do Azure para fazer backup de arquivos, pastas e estado do sistema.<br/><br/>  3) É executado em servidores MABS/DPM para fazer backup do disco de armazenamento local do MABS/DPM no Azure. 
**Extensão da VM do Azure** | É executado em VMs do Azure para fazer backup delas em um cofre.


## <a name="backup-types"></a>Tipos de backup

**Tipo de backup** | **Detalhes** | **Uso**
--- | --- | ---
**Full** | Um backup contém toda a fonte de dados. O backup completo ocupa mais largura de banda da rede. | Usado no backup inicial.
**Differential** |  Armazena os blocos que foram alterados desde o backup completo inicial. Usa uma quantidade menor de armazenamento e rede, e não mantém cópias redundantes dos dados inalterados.<br/><br/> Ineficiente porque os blocos de dados inalterados entre os backups subsequentes são transferidos e armazenados. | Não é usada pelo Backup do Azure.
**Incremental** | Alto armazenamento e eficiência de rede. Armazena somente os blocos de dados que foram alterados desde o backup anterior. <br/><br/> Não é necessário Com o backup incremental, não há necessidade de complementar com backups completos. | Usado pelo MABS/DPM para backups em disco e usado em todos os backups no Azure.

## <a name="sql-server-backup-types"></a>Tipos de backup do SQL Server

**Tipo de backup** | **Detalhes** | **Uso**
--- | --- | ---
**Backup completo** | Um backup completo do banco de dados faz backup do banco de dados inteiro. Ele contém todos os dados em um banco de dados específico ou um conjunto de arquivos ou grupos de arquivos e log suficiente para recuperar esses dados. | No máximo, você pode acionar um backup completo por dia.<br/><br/> Você pode optar por fazer um backup completo em um intervalo diário ou semanal.
**Backup diferencial** | Um backup diferencial é baseado no backup de dados completo anterior mais recente.<br/><br/> Ele captura apenas os dados que foram alterados desde o backup completo. |  No máximo, você pode acionar um backup diferencial por dia.<br/><br/> Você não pode configurar um backup completo e um backup diferencial no mesmo dia.
**Backup de log de transações** | Um backup de log permite a restauração pontual até um determinado segundo. | No máximo, você pode configurar backups de log de transações a cada 15 minutos.


### <a name="comparison"></a>Comparação

O consumo do armazenamento, o RTO (objetivo do tempo de recuperação) e o consumo de rede variam para cada tipo de backup. A imagem a seguir mostra uma comparação dos tipos de backup.
- A fonte de dados A é composta por 10 blocos de armazenamento, A1 até A10, dos quais é feito um backup mensal.
- Os blocos A2, A3, A4 e A9 alteram-se no primeiro mês e o bloco A5 altera-se no próximo mês.
- Para backups diferenciais, no segundo mês é feito o backup dos blocos que foram alterados, A2, A3, A4 e A9. No terceiro mês, é feito novamente o backup desses mesmos blocos, junto com o bloco A5 que foi alterado. O backup dos blocos alterados continua a ser feito até que o próximo backup completo aconteça.
- Para backups incrementais, depois que o backup completo é feito no primeiro mês, os blocos A2, A3, A4 e A9 são marcados como alterados e transferidos para o segundo mês. No terceiro mês, somente o bloco alterado A5 é marcado e transferido. 

![imagem mostrando comparações entre os métodos de backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Recursos de backup

A tabela a seguir resume os recursos para diferentes tipos de backup.

**Recurso** | **Máquinas locais do Windows (direto)** | **VMs do Azure** | **Máquinas/aplicativos com o MABS/DPM**
--- | --- | --- | ---
Fazer backup para o cofre | ![Sim][green] | ![sim][green] | ![Sim][green] 
Backup em disco do MABS/DPM e, em seguida, no Azure | | | ![Sim][green] 
Compactar os dados enviados para backup | ![Sim][green] | Nenhuma compactação é usada durante a transferência de dados. O armazenamento está um pouco inflado, mas a restauração é mais rápida.  | ![Sim][green] 
Executa o backup incremental |![Sim][green] |![sim][green] |![Sim][green] 
Fazer backup de discos com eliminação de duplicação | | | ![Parcialmente][yellow]<br/><br/> Para servidores do MABS/DPM implantados apenas localmente. 

![chave de tabela](./media/backup-architecture/table-key.png)





## <a name="architecture-direct-backup-of-azure-vms"></a>Arquitetura: Backup direto de VMs do Azure

1. Quando você habilita o backup para uma VM do Azure, um backup é executado de acordo com a programação especificada.
2. Durante o primeiro backup, uma extensão de backup é instalada na VM se ela estiver em execução.
    - Em VMs do Windows, a extensão VMSnapshot já está instalada.
    - Em VMs do Linux, a extensão VMSnapshot Linux já está instalada.
3. A extensão tira um instantâneo do nível de armazenamento. 
    - Em VMs do Windows que estejam em execução, o Backup entra em coordenação com o VSS para criar um instantâneo consistente com o aplicativo da VM. Por padrão, o Backup do Azure obtém backups completos de VSS. Se o Backup não conseguir obter um instantâneo consistente do aplicativo, ele gerará um instantâneo consistente com o arquivo.
    - O Backup de VMs do Linux obtém um backup consistente com o arquivo. Para obter instantâneos consistentes de aplicativos, personalize manualmente os pré/pós-scripts.
    - O backup é otimizado pela execução do backup em cada disco de VM em paralelo. Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados. 
4. Após o instantâneo, os dados são transferidos para o cofre. 
    - Apenas os blocos de dados que foram alterados desde o último backup serão copiados.
    - Os dados não são criptografados. O Backup do Azure pode fazer backup de VMs criptografadas do Azure usando o ADE (Azure Disk Encryption).
    - Dados de instantâneo não podem ser imediatamente copiados no cofre. Esse processo pode levar algumas horas em horários de pico. O tempo total de backup da VM será de menos de 24 horas para políticas de backup diárias.
5. Após o envio dos dados para o cofre, o instantâneo é removido e um ponto de recuperação é criado.

Observe que as VMs do Azure precisam de acesso à Internet para comandos de controle. Se você estiver fazendo backup em cargas de trabalho dentro da VM (por exemplo, backup do SQL Server), os dados de backup também precisarão de acesso à Internet. 

![Backup de VMs do Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders"></a>Arquitetura: Backup direto de arquivos/pastas de computadores Windows/VM do Azure locais

1. Para configurar o cenário, baixe e instale o agente do MARS (Serviços de Recuperação do Microsoft Azure) no computador, escolha os itens para fazer o backup, quando os backups serão executados e por quanto tempo serão mantidos no Azure.
2. O backup inicial é executado seguindo suas configurações de backup.
3. O agente do MARS usa o VSS (Serviço de Cópias de Sombra de Volume) do Windows para obter um instantâneo do ponto de tempo dos volumes escolhidos para backup.
    - O agente do MARS usa apenas a Gravação do Sistema do Windows para capturar o instantâneo.
    - O agente não usa todos os gravadores VSS do aplicativo e, portanto, não captura instantâneos consistentes com os aplicativos.
3. Depois de obter o instantâneo com o VSS, o agente do MARS cria um VHD na pasta de cache que você especificou ao configurar o backup e armazena as somas de verificação de cada bloco de dados. 
4. Os backups incrementais são executados de acordo com a programação que você especificar, a menos que você execute um backup ad hoc.
5. Nos backups incrementais, os arquivos alterados são identificados e um novo VHD é criado. Ele é compactado, criptografado e enviado para o cofre.
6. Após o backup incremental terminar, o novo VHD será mesclado com o VHD criado após a replicação inicial fornecendo o estado mais recente a ser usado na comparação do backup em andamento. 

![Backup do servidor local do Windows com o agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: Fazer backup para o DPM/MABS

1. Instale o agente de proteção do DPM ou MABS em computadores que você deseja proteger, e adicione computadores a um grupo de proteção do DPM.
    - Para proteger as máquinas locais, o servidor do DPM ou MABS deverá ser local.
    - Para proteger as VMs do Azure, o servidor do MABS deverá estar no Azure, em execução como uma VM do Azure.
    - A utilização do MABS/DPM pode proteger o backup de volumes, compartilhamentos, arquivos e pastas. Você pode proteger as máquinas com ou sem sistema operacional e aplicativos específicos com configurações de backup de reconhecimento de aplicativo.
2. Ao configurar a proteção de um computador ou aplicativo no DPM/MABS, você escolhe se vai fazer o backup no disco local do MABS/DPM, para armazenamento de curto prazo e no Azure (proteção online). Você também especifica quando o backup no armazenamento local do MABS/DPM e o backup online no Azure deverão ser executados.
3. É feito o backup do disco da carga de trabalho protegida nos discos locais do MABS/DPM de acordo com o agendamento especificado.
4. Os discos do MABS/DPM passam por backup para o cofre pelo agente MARS em execução no servidor DPM/MABS.

![Backup de máquinas/cargas de trabalho protegidas pelo DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Armazenamento da VM do Azure

- As VMs do Azure usam discos para armazenar seu sistema operacional, aplicativos e dados.
- As VMs do Azure têm pelo menos dois discos. Um disco para o sistema operacional e um disco temporário. Também podem ter discos de dados de aplicativos. Os discos são armazenados como VHDs.
- Os VHDs são armazenados como blobs de páginas nas contas de armazenamento standard ou premium no Azure.
    - Armazenamento Standard: suporte de disco confiável e de baixo custo para VMs que executam cargas de trabalho que não são sensíveis à latência. O Armazenamento Standard pode usar os discos SSD ou HDD padrão.
    - Armazenamento Premium: suporte de disco de alto desempenho. Usa discos SSD premium.
- Há diferentes níveis de desempenho de discos:
    - Discos HDD Standard: Com suporte de HDDs e usado para armazenamento econômico.
    - Discos SSD Standard: combina elementos de discos SSD premium e de discos HDD standard para oferecer desempenho mais consistente e a confiabilidade dos discos rígidos, mas ainda econômico.
    - Discos SSD Premium: Com suporte de SSD, proporciona alto desempenho e baixa latência para VMs que executam cargas de trabalho intensivas de E/S.
- Os discos podem ser gerenciados ou não:
    - Discos não gerenciados: Tipos tradicionais de discos usados pelas VMs. Com esses discos, você cria sua própria conta de armazenamento e a especifica ao criar o disco. Você precisa descobrir como maximizar os recursos de armazenamento para suas VMs.
    - Discos gerenciados: o Azure lida com a criação e o gerenciamento de contas de armazenamento para você. Você especifica o tamanho do disco e o nível de desempenho e o Azure cria e gerencia os discos para você. O Azure lida com o armazenamento conforme você adiciona discos e dimensiona as VMs.

Leia mais:

- saiba mais sobre o armazenamento em disco para VMs do [Windows](../virtual-machines/windows/managed-disks-overview.md) e do [Linux](../virtual-machines/linux/managed-disks-overview.md).
- Saiba mais sobre os [tipos de disco](../virtual-machines/windows/disks-types.md) disponíveis, como Standard e Premium.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Backup e restauração de VMs do Azure com o armazenamento premium 

Você pode fazer backup de VMs do Azure usando o armazenamento premium com o Backup do Azure:

- Ao fazer o backup de VMs do armazenamento premium, o serviço de Backup criará um local de preparo temporário, chamado "AzureBackup-", na conta do armazenamento. O tamanho do local de preparo é igual ao tamanho do instantâneo de ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium tenha espaço livre suficiente para acomodar o local de preparo temporário. [Saiba mais](../storage/common/storage-scalability-targets.md#premium-storage-account-scale-limits). Não modifique o local de preparo.
- Após a conclusão do trabalho de backup, o local de preparo será excluído.
- O preço do armazenamento usado para o local de preparo é consistente com os [preços do armazenamento premium](../virtual-machines/windows/disks-types.md#billing).

Ao restaurar VMs do Azure usando o armazenamento premium, você pode restaurá-las no armazenamento standard ou premium. Normalmente, você poderia restaurá-las no armazenamento premium, mas seria mais econômico restaurar como standard se apenas um subconjunto de arquivos da VM fosse necessário.

### <a name="backing-up-and-restoring-managed-disks"></a>Backup e restauração de discos gerenciados

Você pode fazer backup de VMs do Azure com discos gerenciados.
- Faça o backup de VMs com discos gerenciados da mesma maneira que faria com qualquer outra VM do Azure. Você pode fazer backup da VM diretamente nas configurações da máquina virtual ou pode habilitar o backup para VMs no cofre dos Serviços de Recuperação.
- Você pode fazer backup de VMs em discos gerenciados por meio de coleções de RestorePoint criadas com base em discos gerenciados.
- O Backup do Azure também oferece suporte ao backup de VMs de disco gerenciado criptografadas usando o ADE (Azure Disk Encryption).

Ao restaurar VMs com discos gerenciados, você pode restaurar em uma VM completa com discos gerenciados ou em uma conta de armazenamento.
- O Azure lida com os discos gerenciados durante o processo de restauração e, com a opção de conta de armazenamento, você gerencia a conta de armazenamento criada durante a restauração.
- Se você restaurar uma VM gerenciada que esteja criptografada, retire os segredos e chaves da VM do cofre de chaves antes de iniciar o processo de restauração.




## <a name="next-steps"></a>Próximas etapas

- [Revise](backup-support-matrix.md) a matriz de suporte para saber mais sobre os recursos compatíveis e limitações para cenários de backup.
- Configure o backup de um desses cenários:
    - [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md)
    - [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
    - [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
    - [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

