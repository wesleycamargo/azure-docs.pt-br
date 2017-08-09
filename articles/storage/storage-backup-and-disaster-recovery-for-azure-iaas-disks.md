---
title: "Backup e recuperação de desastre de discos de IaaS do Azure | Microsoft Docs"
description: "Neste artigo, explicaremos como planejar o Backup e DR (Recuperação de Desastre) de VMs (máquinas virtuais) e Discos de IaaS no Azure. Este documento aborda Discos Gerenciados e Discos Não Gerenciados"
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: luywang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0de675e46dbd527148ac5fedcd35d81962adfb7e
ms.contentlocale: pt-br
ms.lasthandoff: 07/21/2017

---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Backup e recuperação de desastre de discos de IaaS do Azure

Neste artigo, explicaremos como planejar o Backup e DR (Recuperação de Desastre) de VMs (máquinas virtuais) e Discos de IaaS no Azure. Este documento aborda Discos Gerenciados e Discos Não Gerenciados.

Primeiro, falaremos sobre as funcionalidades internas de tolerância a falhas da plataforma Azure que ajudam a proteger contra falhas locais. Em seguida, abordaremos os cenários de desastre que são não totalmente abrangidos pelas funcionalidades internas, que é o tópico principal abordado neste documento. Também mostraremos vários exemplos de cenários de carga de trabalho nos quais diferentes considerações de Backup e DR podem se aplicar. Depois, examinaremos possíveis soluções para DR de discos de IaaS. 

## <a name="introduction"></a>Introdução

A plataforma Azure usa vários métodos para redundância e tolerância a falhas para ajudar a proteger os clientes contra falhas de hardware localizadas que podem ocorrer. Falhas locais podem incluir problemas com um computador de servidor de armazenamento do Azure que armazena parte dos dados de um disco virtual ou as falhas de um SSD ou HD nesse servidor. Essas falhas de componente de hardware isoladas podem ocorrer durante as operações normais e a plataforma foi projetada para ser resiliente a essas falhas. Desastres graves podem resultar em falhas ou na incapacidade de acesso de um grande número de servidores de armazenamento ou de um datacenter inteiro. Embora as VMs e os discos normalmente sejam protegidos contra falhas localizadas, etapas adicionais são necessárias para proteger a carga de trabalho contra falhas catastróficas de toda a região (por exemplo, um desastre grave) que podem afetar a VM e os discos.

Além da possibilidade de falhas na plataforma, podem ocorrer problemas com os dados ou o aplicativo do cliente. Por exemplo, uma nova versão do aplicativo pode inadvertidamente fazer uma alteração significativa nos dados. Nesse caso, talvez você deseje reverter o aplicativo e os dados para uma versão anterior que contém o último estado válido conhecido. Isso exige a manutenção de backups regulares.

Para a recuperação de desastre regional, você deve fazer backup dos discos da VM de IaaS em outra região. 

Antes de examinarmos as opções de Backup e DR, vamos recapitular alguns métodos disponíveis para o tratamento de falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência de IaaS do Azure

*Resiliência* refere-se à tolerância a falhas normais que ocorrem em componentes de hardware. A resiliência é a capacidade de se recuperar de falhas e continuar funcionando. Não se trata de evitar falhas, mas responder a elas de uma maneira que evite o tempo de inatividade ou a perda de dados. A meta de resiliência é retornar o aplicativo a um estado totalmente funcional após uma falha. As Máquinas Virtuais e os Discos do Azure foram projetados para serem resilientes a falhas comuns de hardware. Vamos dar uma olhada em como a plataforma de IaaS do Azure fornece essa resiliência.

Uma máquina virtual consiste principalmente em duas partes: (1) um servidor de computação e (2) os discos persistentes. Ambos afetam a tolerância a falhas de uma máquina virtual.

Se o servidor host de computação do Azure que hospeda a VM tiver uma falha de hardware (o que é raro), o Azure restaurará automaticamente a VM em outro servidor, pois foi projetado para isso. Se isso ocorrer, será feita uma reinicialização e a VM será copiada em backup após algum tempo. O Azure detecta essas falhas de hardware automaticamente e executa recuperações para ajudar a garantir que a VM do cliente estará disponível assim que possível.

Em relação aos discos de IaaS, a durabilidade dos dados é essencial para a plataforma de armazenamento persistente. Os clientes do Azure têm aplicativos de negócios importantes em execução na IaaS e eles dependem da persistência dos dados. O Azure projeta a proteção para esses discos de IaaS com três cópias redundantes dos dados armazenados localmente, fornecendo alta durabilidade contra falhas locais. Se um dos componentes de hardware que contém o disco falhar, a VM não será afetada porque há duas cópias adicionais para dar suporte às solicitações do disco. Isso funcionará bem, mesmo se dois componentes de hardware diferentes que dão suporte a um disco falharem ao mesmo tempo (o que seria muito raro). Para ajudar a garantir que sempre mantemos três réplicas, o serviço de Armazenamento do Azure gera automaticamente uma nova cópia dos dados em segundo plano se uma das três cópias não fica disponível. Portanto, não deve ser necessário usar o RAID com discos do Azure para a tolerância a falhas. Uma simples configuração RAID 0 deve ser suficiente para a distribuição dos discos, se necessário, para criar volumes maiores.

Devido a essa arquitetura, o **Azure proporcionou durabilidade de nível empresarial de modo consistente para discos de IaaS, com uma [Taxa de Falha Anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate) líder do setor de 0%.**

Falhas de hardware localizadas no host de computação ou na plataforma de armazenamento podem, às vezes, resultar em indisponibilidade temporária para a VM que é coberta pelo [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) em relação à Disponibilidade da VM. O Azure também fornece um SLA líder do setor para instâncias de VM individuais que usam discos do Armazenamento Premium.

Para proteger cargas de trabalho do aplicativo contra o tempo de inatividade devido à indisponibilidade temporária de um disco ou de uma VM, os clientes podem utilizar [Conjuntos de Disponibilidade](../virtual-machines/windows/manage-availability.md). Duas ou mais máquinas virtuais em um conjunto de disponibilidade fornecem redundância para o aplicativo. Em seguida, o Azure cria essas VMs e esses discos em domínios de falha separados com diferentes componentes de energia, rede e servidor. Portanto, as falhas de hardware localizadas geralmente não afetam várias VMs no conjunto ao mesmo tempo, fornecendo alta disponibilidade para o aplicativo. É considerada uma melhor prática usar conjuntos de disponibilidade quando a alta disponibilidade é necessária. Para obter mais informações, consulte os aspectos da Recuperação de Desastre detalhados abaixo.

### <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastre

DR (recuperação de desastre) é a capacidade de recuperação de incidentes raros, exceto graves: falhas não transitórias em larga escala, como interrupções de serviço que afetam toda a região. A recuperação de desastre inclui backup e arquivamento de dados e pode incluir a intervenção manual, como a restauração um banco de dados com base em um backup.

A proteção interna da plataforma Azure contra falhas localizadas pode não proteger totalmente VMs/discos, no caso de desastres graves que podem causar interrupções em larga escala. Isso inclui eventos catastróficos, como um data center atingido por um furacão, terremoto, incêndio ou falhas de unidade de hardware em grande escala. Além disso, você pode encontrar falhas devido a problemas de dados ou do aplicativo.

Para ajudar a proteger as cargas de trabalho de IaaS contra interrupções, você deve planejar a redundância e os backups para permitir a recuperação. Para a recuperação de desastre, você deve planejar a redundância e o backup em uma localização geográfica diferente fora do site primário. Isso ajuda a garantir que o backup não é afetado pelo mesmo evento que originalmente afetou a VM ou os discos. Para obter mais informações, consulte [Recuperação de desastre para aplicativos criados no Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

Suas considerações de DR podem incluir os seguintes aspectos:

1. HA (Alta Disponibilidade) é a capacidade do aplicativo de continuar em execução em um estado íntegro, sem tempo de inatividade significativo. Por “estado íntegro” queremos dizer o aplicativo está respondendo e os usuários podem se conectar ao aplicativo e interagir com ele. Alguns aplicativos e bancos de dados críticos podem precisar estar sempre disponíveis, mesmo quando há falhas na plataforma. Para essas cargas de trabalho, talvez você precise planejar a redundância para o aplicativo, bem como para os dados.

2. Durabilidade de Dados: em alguns casos, a principal consideração é garantir que os dados são preservados no caso de um desastre. Portanto, talvez seja necessário fazer um backup dos dados em outro site. Para essas cargas de trabalho, talvez não seja necessário ter a redundância total para o aplicativo, mas um backup regular dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de backup e DR

Vamos examinar alguns exemplos típicos de cenários de carga de trabalho do aplicativo e as considerações sobre o planejamento da Recuperação de Desastre.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: principais soluções de banco de dados

Considere um servidor de banco de dados de produção como o SQL Server ou o Oracle que pode dar suporte à Alta Disponibilidade. Usuários e aplicativos de produção críticos dependem desse banco de dados. O plano de Recuperação de Desastre para esse sistema pode precisar dar suporte aos seguintes requisitos:

1. Os dados devem ser protegidos e recuperáveis.
2.  O servidor deve estar disponível para uso.

Isso pode exigir a manutenção de uma réplica do banco de dados em outra região como um backup. Dependendo dos requisitos de disponibilidade do servidor e de recuperação de dados, a solução pode variar de um site de réplica Ativo-Ativo ou Ativo-Passivo para backups offline periódicos dos dados. Bancos de dados relacionais, como o SQL Server e o Oracle, fornecem várias opções de replicação. Para o SQL Server, [Grupos de Disponibilidade AlwaysOn do SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) podem ser usados para alta disponibilidade.

Bancos de dados NoSQL como o MongoDB também dão suporte a [réplicas](https://docs.mongodb.com/manual/replication/) para redundância. As réplicas para alta disponibilidade podem ser usadas.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: um cluster de VMs redundantes

Considere uma carga de trabalho manipulada por um cluster de VMs que fornece redundância e balanceamento de carga. Um exemplo seria um cluster do Cassandra implantado em uma região. Esse tipo de arquitetura já fornece um alto nível de redundância nessa região. No entanto, para proteger a carga de trabalho contra uma falha de nível regional, você deve considerar a distribuição do cluster em duas regiões ou a realização de backups periódicos em outra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: carga de trabalho de aplicativos IaaS

Isso pode ser uma carga de trabalho de produção típica em execução em uma VM do Azure. Por exemplo, pode ser um servidor Web ou servidor de arquivos que mantém o conteúdo e outros recursos de um site. Também pode ser um aplicativo de negócios personalizado em execução em uma VM que armazenou seus dados, recursos e o estado do aplicativo nos discos da VM. Nesse caso, é importante fazer backups regularmente. A frequência de backup deve se basear na natureza da carga de trabalho da VM. Por exemplo, se o aplicativo é executado diariamente e modifica dados, o backup deve ser feito a cada hora.

Outro exemplo é um servidor de relatórios que efetua pull de dados de outras fontes e gera relatórios agregados. A perda dessa VM ou desses discos levará à perda dos relatórios. No entanto, talvez seja possível executar o processo de relatórios e regenerar o resultado. Nesse caso, não há realmente uma perda de dados, mesmo se o servidor de relatórios for atingido por um desastre e, portanto, você pode ter um nível mais alto de tolerância para a perda de parte dos dados no servidor de relatórios. Nesse caso, backups menos frequentes são uma opção para reduzir o custo.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: problemas de dados de aplicativos IaaS

Você tem um aplicativo que calcula, mantém e fornece dados comerciais críticos, como informações sobre preços. Uma nova versão do aplicativo tinha um bug de software que calculava os preços incorretamente e corrompeu os dados comerciais existentes fornecidos pela plataforma. Aqui, a melhor decisão será reverter para a versão anterior do aplicativo e dos dados primeiro. Para possibilitar isso, faça backups periódicos do sistema.

## <a name="disaster-recovery-solution-azure-backup-service"></a>Solução de Recuperação de Desastre: Serviço de Backup do Azure

O [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) pode ser usado para Backup e DR e funciona com [Managed Disks](storage-managed-disks-overview.md), bem como com [Discos Não Gerenciados](storage-about-disks-and-vhds-windows.md#unmanaged-disks). Crie um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup. 

Se você usar [discos do Armazenamento Premium](storage-premium-storage.md), [Managed Disks](storage-managed-disks-overview.md) ou outros tipos de disco com a opção [LRS (armazenamento com redundância local)](storage-redundancy.md#locally-redundant-storage), é especialmente importante utilizar backups periódicos de DR. O Backup do Azure armazena os dados no cofre dos Serviços de Recuperação para retenção de longo prazo. Escolha a opção [GRS (armazenamento com redundância geográfica)](storage-redundancy.md#geo-redundant-storage) para o cofre dos Serviços de Recuperação de Backup. Isso garantirá que os backups são replicados em outra região do Azure para proteger contra desastres regionais.

Para [Discos Não Gerenciados](storage-about-disks-and-vhds-windows.md#unmanaged-disks), use o tipo de armazenamento LRS para discos de IaaS, mas verifique se o Backup do Azure está habilitado com a opção GRS no cofre dos Serviços de Recuperação.

**Se você usar a opção [GRS](storage-redundancy.md#geo-redundant-storage)/[RA-GRS](storage-redundancy.md#read-access-geo-redundant-storage) para os Discos Não Gerenciados, ainda precisará de instantâneos consistentes para Backup e DR.** Use o [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) ou [instantâneos consistentes](#alternative-solution-consistent-snapshots).

Veja a seguir um resumo das soluções de DR.

| Cenário | Replicação automática | Solução de DR |
| --- | --- | --- |
| *Discos do Armazenamento Premium* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) |
| *Managed Disks* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) |
| *Discos LRS Não Gerenciados* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) |
| *Discos GRS Não Gerenciados* | Várias regiões ([GRS](storage-redundancy.md#geo-redundant-storage)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |
| *Discos RA-GRS Não Gerenciados* | Várias regiões ([RA-GRS](storage-redundancy.md#read-access-geo-redundant-storage)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |

A alta disponibilidade pode ser mais bem alcançada com o uso do Managed Disks em um Conjunto de Disponibilidade, junto com o Backup do Azure. Se você estiver usando Discos Não Gerenciados, ainda poderá usar o Backup do Azure para DR. Caso não possa usar o Backup do Azure, tirar [instantâneos consistentes](#alternative-solution-consistent-snapshots), conforme descrito em uma seção posterior, é uma solução alternativa para Backup e DR.

Suas escolhas de alta disponibilidade, backup e DR nos níveis do Aplicativo ou da Infraestrutura podem ser representadas conforme mostrado abaixo:

| *Level* | Alta disponibilidade   | Backup/DR |
| --- | --- | --- |
| *Aplicativo* | SQL AlwaysOn | Serviço de Backup do Azure |
| *Infraestrutura*  | Conjunto de disponibilidade  | GRS com instantâneos consistentes |

### <a name="using-the-azure-backup-service"></a>Usando o Serviço de Backup do Azure

O [Backup do Azure](../backup/backup-azure-vms-introduction.md) pode fazer backup das VMs que executam Windows ou Linux no Cofre dos Serviços de Recuperação do Azure. Fazer backup e restaurar dados críticos para os negócios é complicado, pois os dados críticos para os negócios precisam ser copiados em backup enquanto os aplicativos que produzem os dados estão em execução. Para resolver isso, o Backup do Azure fornece backups consistentes com o aplicativo para cargas de trabalho da Microsoft usando o VSS (Serviço de Sombra de Volume) para garantir que os dados são gravados corretamente no armazenamento. Para VMs Linux, somente backups consistentes com os arquivos são possíveis, pois o Linux não tem uma funcionalidade equivalente ao VSS.

![][1]

Quando o Backup do Azure inicia um trabalho de backup no horário agendado, ele dispara a extensão de backup instalada na VM para tirar um instantâneo pontual. Um instantâneo é tirado em conjunto com o VSS para obter um instantâneo consistente dos discos na máquina virtual sem precisar desligá-la. A extensão de backup na VM libera todas as gravações antes de tirar o instantâneo consistente de todos os discos. Depois de tirar o instantâneo, os dados são transferidos pelo Backup do Azure para o cofre de backup. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde o último backup.


Para restaurar, exiba os backups disponíveis por meio do Backup do Azure e, em seguida, inicie uma restauração. Crie e restaure backups do Azure por meio do [portal do Azure](https://portal.azure.com/), [usando o PowerShell](../backup/backup-azure-vms-automation.md ) ou usando a [CLI do Azure](https://docs.microsoft.com/azure/xplat-cli-install). Para obter mais informações, consulte Backup do Azure.

### <a name="steps-to-enable-backup"></a>Etapas para habilitar o backup

As etapas a seguir normalmente são usadas para habilitar os backups das VMs usando o [Portal do Azure](https://portal.azure.com/). Haverá algumas variações dependendo do cenário exato. Consulte a documentação do [Backup do Azure](../backup/backup-azure-vms-introduction.md) para obter detalhes completos. O Backup do Azure também [dá suporte a VMs com o Managed Disks](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Crie um cofre dos serviços de recuperação para uma VM usando as seguintes etapas:

    a.  Usando o [portal do Azure](https://portal.azure.com/), procure todos os recursos e localize “cofres dos Serviços de Recuperação”.

    b.  No menu de cofres dos Serviços de Recuperação, clique em Adicionar e siga as etapas para criar um novo cofre na mesma região da VM. Por exemplo, se a VM estiver na região Oeste dos EUA, escolha Oeste dos EUA para o cofre.

2.  Verifique a replicação de Armazenamento do cofre recém-criado. Para fazer isso, acesse o cofre na folha de cofres dos Serviços de Recuperação e acesse as configurações/Configuração de Backup. Verifique se a opção GRS está selecionada por padrão. Isso garantirá que o cofre é replicado automaticamente em um data center secundário. Por exemplo, o cofre no Oeste dos EUA será replicado automaticamente no Leste dos EUA.

3.  Configure a política de backup e selecione a VM na mesma interface do usuário.

4.  Verifique se o Agente de Backup está instalado na VM. Se a VM for criada usando uma imagem da galeria do Azure, o agente de Backup já estará instalado. Caso contrário (ou seja, se você estiver usando uma imagem personalizada), use as instruções para [Instalar o Agente de VM na máquina virtual](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Verifique se a VM permite a conectividade de rede para que o serviço de backup funcione. Siga estas instruções para a [Conectividade de rede](../backup/backup-azure-arm-vms-prepare.md#network-connectivity).

6.  Depois que as etapas acima forem concluídas, o backup será executado em intervalos regulares, conforme especificado na política de backup. Se necessário, dispare o primeiro backup manualmente no painel do cofre do portal do Azure.

Para automatizar o Backup do Azure usando scripts, consulte [Cmdlets do PowerShell para backup de VM](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Etapas de recuperação

Se você precisar reparar ou recriar uma VM, restaure a VM por meio de um dos pontos de recuperação de backup no cofre. Há duas opções diferentes para realizar a recuperação:

1.  Você pode criar uma nova VM como uma representação pontual da VM copiada em backup.

2.  Você pode restaurar os discos e, em seguida, usar o modelo para a VM para personalizar e recriar a VM restaurada. 

Consulte as instruções para [Usar o portal do Azure para restaurar máquinas virtuais](../backup/backup-azure-arm-restore-vms.md#restoring-a-vm-during-azure-datacenter-disaster). O documento também explica as etapas específicas para a restauração de VMs copiadas em backup para o data center emparelhado usando o cofre de backup com redundância geográfica no caso de um desastre no data center primário. Nesse caso, o Backup do Azure usa o serviço de Computação da região secundária para criar a máquina virtual restaurada.

Use também o PowerShell para [restaurar uma VM](../backup/backup-azure-vms-automation.md#restore-an-azure-vm) ou [criar uma nova VM com base em discos restaurados](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: instantâneos consistentes

Se não for possível usar o Serviço de Backup do Azure, implemente seu próprio mecanismo de backup usando instantâneos. É complicado criar instantâneos consistentes para todos os discos usados por uma VM e, em seguida, replicar esses instantâneos em outra região. Por esse motivo, o Azure considera o uso do Serviço de Backup uma opção melhor do que a criação de uma solução personalizada. Se você usar o armazenamento RA-GRS/GRS para discos, os instantâneos serão replicados automaticamente em um data center secundário. Se você usar o armazenamento LRS para discos, precisará replicar os dados por conta própria. Para obter mais informações, consulte [Fazer backup de discos não gerenciados de VM do Azure com instantâneos incrementais](storage-incremental-snapshots.md).

Um instantâneo é uma representação de um objeto em um ponto específico no tempo. Um instantâneo incorrerá em cobrança pelo tamanho incremental dos dados que ele mantém. Para obter mais informações, consulte [Criar um instantâneo de blob](storage-blob-snapshots.md).

### <a name="creating-snapshots-while-the-vm-is-running"></a>Criando instantâneos enquanto a VM está em execução

Embora você possa tirar um instantâneo a qualquer momento, se a VM estiver em execução, ainda haverá dados sendo transmitidos para os discos e os instantâneos poderão conter operações parciais que estavam em trânsito. Além disso, se houver vários discos envolvidos, os instantâneos de discos diferentes poderão ter ocorrido em momentos diferentes, o que significa que esses instantâneos podem não estar coordenados. Isso é especialmente problemático para volumes distribuídos cujos arquivos podem ser corrompidos se alterações estavam sendo feitas durante o backup.

Para evitar essa situação, o processo de backup deve implementar as seguintes etapas:

1.  Congelar todos os discos

2.  Liberar todas as gravações pendentes

3.  Em seguida, [criar um instantâneo de blob](storage-blob-snapshots.md) para todos os discos

Alguns aplicativos do Windows como o SQL Server fornecem um mecanismo de backup coordenado por meio do VSS para criar backups consistentes com o aplicativo. No Linux, use uma ferramenta como o fsfreeze para coordenar os discos, o que fornecerá backups consistentes com os arquivos, mas não instantâneos consistentes com o aplicativo. Esse processo é complicado; portanto, considere o uso de [Backup do Azure](../backup/backup-azure-vms-introduction.md) ou de uma solução de backup de terceiros que já implementa isso.

O processo acima resultará em uma coleção de instantâneos coordenados para todos os discos de VM, representando uma exibição pontual específica da VM. Esse é um ponto de restauração de backup para a VM. Repita o processo em intervalos agendados para criar backups periódicos. Veja abaixo as etapas para [copiar os backups para outra região](#copy-the-snapshots-to-another-region) para DR.

### <a name="creating-snapshots-while-the-vm-is-offline"></a>Criando instantâneos enquanto a VM está offline

Outra opção para criar backups consistentes é desligar a VM e tirar instantâneos de blob de cada disco. Isso é mais fácil do que coordenar instantâneos de uma VM em execução, mas exige alguns minutos de inatividade. Para esse processo, siga estas etapas:

1. Desligue a VM.

2. Crie um instantâneo de cada blob do VHD, o que leva apenas alguns segundos.

    Para criar um instantâneo, use o [PowerShell](storage-powershell-guide-full.md#how-to-manage-azure-blob-snapshots), a [API REST do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), a [CLI do Azure](https://docs.microsoft.com/azure/xplat-cli-install) ou uma das Bibliotecas de Clientes do Armazenamento do Azure, como [a biblioteca de clientes do Armazenamento para .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Inicie a VM, o que encerra o tempo de inatividade. Normalmente, todo o processo é concluído em alguns minutos.

Esse processo gera uma coleção de instantâneos consistentes para todos os discos, fornecendo um ponto de restauração de backup para a VM. Veja abaixo as etapas para copiar os instantâneos para outra região para DR.

### <a name="copy-the-snapshots-to-another-region"></a>Copiar os instantâneos para outra região

A criação de instantâneos por si só pode não ser suficiente para DR. Também é necessário replicar os backups de instantâneo em outra região.

Se você estiver usando GRS ou RA-GRS para os discos, os instantâneos serão replicados na região secundária automaticamente. Pode haver alguns minutos de retardo antes da replicação e se o data center primário ficar inoperante antes que os instantâneos concluam a replicação, você não poderá acessar os instantâneos no data center secundário. A probabilidade de que isso ocorra é pequena.

> [!Note] 
> Apenas ter os discos em uma conta GRS ou RA-GRS não protege a VM contra desastres. Também é necessário criar instantâneos coordenados ou usar o Backup do Azure. Isso é necessário para recuperar uma VM para um estado consistente.

Se estiver usando o LRS, copie os instantâneos para outra conta de armazenamento imediatamente após a criação do instantâneo. O destino de cópia pode ser uma conta de armazenamento LRS em outra região, resultando na cópia em uma região remota. Você também pode copiar o instantâneo para uma conta de armazenamento RA-GRS na mesma região. Nesse caso, o instantâneo será replicado lentamente na região secundária remota. O backup é protegido contra desastres no site primário após a conclusão da cópia e da replicação.

Para copiar os instantâneos incrementais para DR com eficiência, examine as instruções em [Fazer backup de discos não gerenciados de VM do Azure com instantâneos incrementais](storage-incremental-snapshots.md).

![][2]

### <a name="recovery-from-snapshots"></a>Recuperação com base em instantâneos

Para recuperar um instantâneo, copie-o para criar um novo blob. Se estiver copiando o instantâneo da conta primária, copie o instantâneo sobre o blob base do instantâneo, revertendo o disco para o instantâneo; isso é conhecido como promoção do instantâneo. Se estiver copiando o backup de instantâneo de uma conta secundária (no caso do RA-GRS), ele deverá ser copiado para uma conta primária. Copie um instantâneo [usando o PowerShell](storage-powershell-guide-full.md#how-to-copy-a-snapshot-of-a-blob) ou o utilitário AzCopy. Para obter mais informações, consulte [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md).

No caso de VMs com vários discos, é necessário copiar todos os instantâneos que fazem parte do mesmo ponto de restauração coordenado. Depois de copiar os instantâneos para blobs de VHD graváveis, use os blobs para recriar a VM usando o modelo da VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

O SQL Server em execução em uma VM tem suas próprias funcionalidades internas para fazer backup do banco de dados do SQL Server para o armazenamento de Blobs do Azure ou um compartilhamento de arquivos. Se a conta de armazenamento for GRS ou RA-GRS, você poderá acessar esses backups no data center secundário da conta de armazenamento em caso de desastre, com as mesmas restrições, conforme abordado anteriormente. Para obter mais informações, consulte [Backup e Restauração para SQL Server em Máquinas Virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Além de backup e restauração, os [Grupos de Disponibilidade AlwaysOn do SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) podem manter réplicas secundárias de bancos de dados para reduzir consideravelmente o tempo de recuperação de desastre.

## <a name="other-considerations"></a>Outras considerações

Este artigo abordou como fazer backup ou tirar instantâneos das VMs e de seus discos para dar suporte à recuperação de desastre e como usá-los para recuperação. Com o modelo do Azure Resource Manager, muitas pessoas usam modelos para criar suas VMs e outra infraestrutura no Azure. Use um modelo para criar uma VM que tem sempre a mesma configuração. Se você usar imagens personalizadas para criar as VMs, também deverá verificar se as imagens são protegidas usando uma conta RA-GRS para armazená-las.

Consequentemente, o processo de backup pode ser uma combinação de duas coisas:

1. Fazer backup dos dados (discos)
2. Fazer backup da configuração (modelos, imagens personalizadas)

Dependendo da opção de backup escolhida, talvez você precise administrar o backup dos dados e da configuração ou o serviço de backup pode administrar tudo isso para você.

## <a name="appendix---understanding-the-impact-of-lrs-grs-and-ra-grs"></a>Apêndice – Noções básicas do impacto do LRS, GRS e RA-GRS

Para contas de armazenamento no Azure, há três tipos de redundância de dados que devem ser considerados em relação à recuperação de desastre – LRS (redundância local), GRS (redundância geográfica) ou RA-GRS (redundância geográfica com acesso de leitura). 

O LRS (Armazenamento com Redundância Local) retém três cópias dos dados no mesmo data center. Ao gravar os dados, todas as três cópias são atualizadas antes que uma operação bem-sucedida seja retornada ao chamador, de modo que você saiba que elas são idênticas. O disco está protegido contra falhas locais, pois é muito improvável que todas as três cópias sejam afetadas ao mesmo tempo. No caso do LRS, não há nenhuma redundância geográfica e, portanto, o disco não está protegido contra falhas catastróficas que podem afetar todo um data center ou toda uma unidade de armazenamento.

Com o GRS e o RA-GRS, três cópias dos dados são retidas na região primária (escolhida por você) e mais três cópias dos dados são retidas em uma região secundária correspondente (definida pelo Azure). Por exemplo, se você armazenar dados no Oeste dos EUA, os dados serão replicados no Leste dos EUA. Isso é feito de forma assíncrona e há um pequeno atraso entre as atualizações no primário e no secundário. As réplicas dos discos no site secundário são consistentes por disco (com o atraso), mas as réplicas de vários discos ativos podem não estar sincronizadas **entre si**. Para ter réplicas consistentes em vários discos, são necessários instantâneos consistentes.

A principal diferença entre o GRS e o RA-GRS é que, com o RA-GRS, você pode ler a cópia secundária a qualquer momento. Se houver um problema que torna os dados na região primária inacessíveis, a equipe do Azure envidará todos os esforços para restaurar o acesso. Enquanto o primário estiver inoperante, se você tiver o RA-GRS habilitado, poderá acessar os dados no data center secundário. Portanto, se você pretende ler na réplica enquanto o primário estiver inacessível, o RA-GRS deve ser considerado.

Se isso acabar se tornando uma interrupção significativa, a equipe do Azure poderá disparar um failover geográfico e alterar as entradas DNS primárias para que apontem para o armazenamento secundário. Neste ponto, se você tiver o GRS ou o RA-GRS habilitado, poderá acessar os dados na região que costumava ser a secundária. Em outras palavras, se sua conta de armazenamento for GRS e houver um problema, você poderá acessar o armazenamento secundário somente se houver um failover geográfico.

Para obter mais informações, consulte [O que fazer se ocorrer uma interrupção do Armazenamento do Azure](storage-disaster-recovery-guidance.md). 

Observe que a Microsoft controla a ocorrência de um failover. O failover não é controlado por conta de armazenamento e, portanto, isso não é decidido por clientes individuais. Para implementar a Recuperação de Desastre em contas de armazenamento ou discos de máquina virtual específicos, use as técnicas descritas anteriormente neste artigo.



[1]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
