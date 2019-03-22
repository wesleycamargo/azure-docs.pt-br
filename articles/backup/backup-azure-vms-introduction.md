---
title: Sobre o backup de VM do Azure
description: Saiba mais sobre o backup de VM do Azure e observe algumas melhores práticas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 1e80b2083a2fce90259ac0634d9e7f796f459fcd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880935"
---
# <a name="about-azure-vm-backup"></a>Sobre o backup de VM do Azure

Este artigo descreve como o [serviço de Backup do Azure](backup-introduction-to-azure-backup.md) faz o backup de máquinas virtuais (VMs).

## <a name="backup-process"></a>Processo de backup

Aqui está como o Backup do Azure completa um backup para VMs do Azure:

1. Para VMs do Azure que está selecionado para backup, o Backup do Azure inicia um trabalho de backup acordo com o agendamento de backup que você especificar.
1. Durante o primeiro backup, uma extensão de backup está instalada na VM, se a VM estiver em execução.
    - Para VMs do Windows, o _VMSnapshot_ extensão está instalada.
    - Para VMs Linux, o _VMSnapshotLinux_ extensão está instalada.
1. Para VMs do Windows que estão em execução, o Backup coordena com o Windows Volume Shadow Copy Service (VSS) para criar um instantâneo consistente com o aplicativo da VM.
    - Por padrão, o Backup usa backups completos de VSS.
    - Se o Backup não é possível tirar um instantâneo consistente do aplicativo, ele leva um instantâneo consistente com o arquivo do armazenamento subjacente (porque nenhuma gravação de aplicativo ocorre enquanto a VM estiver parada).
1. Para VMs Linux, o Backup usa um backup consistente com o arquivo. Para instantâneos consistentes com o aplicativo, você precisa personalizar manualmente os scripts pré/pós.
1. Depois de Backup cria o instantâneo, ele transfere os dados para o cofre. 
    - O backup é otimizado, fazendo backup de cada disco VM em paralelo.
    - Para cada disco que está sendo feito backup, Backup do Azure lê os blocos no disco e identifica e transfere apenas os blocos de dados que foram alterados (o delta) desde o backup anterior.
    - Dados de instantâneo não podem ser imediatamente copiados no cofre. Esse processo pode levar algumas horas em horários de pico. Tempo total de backup para uma máquina virtual será menor que 24 horas para políticas de backup diárias.

1. Quando a transferência de dados for concluída, o instantâneo é removido e um ponto de recuperação é criado.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Criptografia de backups de VM do Azure

Quando você faz backup de VMs do Azure com o Backup do Azure, máquinas virtuais são criptografadas em repouso com SSE (Criptografia do Serviço de Armazenamento). O Backup do Azure também pode fazer backup de máquinas virtuais do Azure que são criptografadas usando o Azure Disk Encryption.

**Criptografia** | **Detalhes** | **Suporte**
--- | --- | ---
**Criptografia de Disco do Azure** | O Azure Disk Encryption criptografa os discos de dados e do sistema operacional para VMs do Azure.<br/><br/> O Azure Disk Encryption se integra com chaves de criptografia de disco BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. O Azure Disk Encryption também se integra com chaves de criptografia de chave do Azure Key Vault (KEKs). | O Backup do Azure dá suporte ao backup de VMs do Azure gerenciados e não gerenciados, criptografada com BEKs apenas ou com BEKs junto com KEKs.<br/><br/> BEKs e KEKs são submetidos a backup e criptografados.<br/><br/> Como KEKs e BEKs passam por backup, os usuários com as permissões necessárias podem restaurar as chaves e segredos de volta para o Cofre de chaves se necessário. Esses usuários também podem recuperar a VM criptografada.<br/><br/> Segredos e chaves criptografadas não podem ser lido por usuários não autorizados ou pelo Azure.
**SSE** | Com a SSE, o armazenamento do Azure fornece criptografia em repouso ao criptografar automaticamente os dados antes de armazená-lo. O armazenamento do Azure também descriptografa os dados antes de recuperá-los. | O Backup do Azure usa a SSE para criptografia em repouso de VMs do Azure.

Para VMs do Azure gerenciados e não gerenciados, o Backup oferece suporte a VMs criptografadas somente com BEKs ou VMs criptografadas com BEKs junto com KEKs.

Backup BEKs (segredos) e KEKs (chaves) são criptografados. Podem ser lidos e usados somente quando eles são restaurados volta para o Cofre de chaves por usuários autorizados. Usuários não autorizados nem o Azure pode ler ou usar o backup de chaves ou segredos.

BEKs também passam por backup. Portanto, se o BEKs forem perdidas, os usuários autorizados podem restaurar os BEKs ao Cofre de chaves e recuperar as VMs criptografadas. Somente os usuários com o nível necessário de permissões podem fazer backup e restaurar VMs criptografadas ou chaves e segredos.

## <a name="snapshot-creation"></a>Criação de instantâneos

O Backup do Azure usa instantâneos de acordo com o agendamento de backup. 

- **VMs do Windows:** Para VMs do Windows, o serviço de Backup coordena com o VSS para criar um instantâneo consistente com o aplicativo os discos de VM.

  - Por padrão, o Backup do Azure usa backups completos de VSS. [Saiba mais](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Para alterar a configuração para que o Backup do Azure usa backups de cópia VSS, defina a seguinte chave do registro em um prompt de comando:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **VMs do Linux:** Para tirar instantâneos consistentes com o aplicativo de VMs do Linux, use o pré-script de Linux e pós-script do framework para gravar seus próprios scripts personalizados para garantir a consistência.

  - O Backup do Azure invoca apenas os scripts de pré/pós escritos por você.
  - Se os scripts pré e pós-scripts são executados com êxito, o Backup do Azure marcará o ponto de recuperação como consistente com o aplicativo. No entanto, quando você estiver usando scripts personalizados, você é responsável, por fim, a consistência do aplicativo.
  - [Saiba mais](backup-azure-linux-app-consistent.md) sobre como configurar os scripts.

### <a name="snapshot-consistency"></a>Consistência do instantâneo

A tabela a seguir explica os diferentes tipos de consistência de instantâneo:

**Instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Consistente com o aplicativo** | Backups consistentes com o aplicativo capturam o conteúdo da memória e operações de E/S pendentes. Instantâneos consistentes com o aplicativo usam um gravador VSS (ou scripts de pré/pós para Linux) para garantir a consistência dos dados do aplicativo antes que ocorra um backup. | Quando você estiver recuperando uma VM com um instantâneo consistente do aplicativo, a VM é inicializada. Não há perda ou corrupção de dados. O aplicativo é iniciado em um estado consistente. | Windows: Todos os gravadores VSS foram bem-sucedidos<br/><br/> Linux: Pré/pós-scripts estão configurados e foram bem-sucedidos
**Sistema de arquivos consistente** | Backups consistentes de sistema de arquivos fornecem consistência obtendo um instantâneo de todos os arquivos ao mesmo tempo.<br/><br/> | Quando você estiver recuperando uma VM com um instantâneo consistente do sistema de arquivos, a VM é inicializada. Não há perda ou corrupção de dados. Os aplicativos precisam implementar seus próprios mecanismos de "correção" para garantir a consistência dos dados restaurados. | Windows: Alguns gravadores VSS falharam <br/><br/> Linux: Padrão (se os scripts pré/pós não forem configurados ou falha)
**Controle de falhas** | Instantâneos consistentes com falhas normalmente ocorrem se uma VM do Azure é fechado no momento do backup. Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup.<br/><br/> Um ponto de recuperação de controle de falhas não garante a consistência de dados para o sistema operacional ou o aplicativo. | Embora não haja nenhuma garantia, a VM é inicializada normalmente e, em seguida, ele inicia uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou operações de gravação que não foram transferidas para o disco antes que a falha são perdidas. Os aplicativos implementam sua própria verificação de dados. Por exemplo, um aplicativo de banco de dados pode usar seu log de transações para verificação. Se o log de transações tiver entradas que não estão no banco de dados, o software de banco de dados efetua transações até que os dados são consistentes. | A VM está em estado de desligamento

## <a name="backup-and-restore-considerations"></a>Considerações sobre backup e restauração 

**Consideração** | **Detalhes**
--- | ---
**Disco** | Backup de discos de VM é paralelo. Por exemplo, se uma VM tiver quatro discos, o serviço de Backup tentará fazer backup de todos os quatro discos em paralelo. O backup é incremental (somente dados alterados).
**Agendamento** |  Para reduzir o tráfego de backup, fazer backup de VMs diferentes em diferentes momentos do dia e verifique se que as horas não se sobrepõem. Fazer backup de VMs ao mesmo tempo provoca congestionamentos de tráfego.
**Preparando backups** | Tenha em mente o tempo necessário para preparar o backup. O tempo de preparação inclui instalando ou atualizando a extensão de backup e disparar um instantâneo de acordo com o agendamento de backup.
**Transferência de dados** | Considere o tempo necessário para Backup do Azure para identificar as alterações incrementais do backup anterior.<br/><br/> Em um backup incremental, o Backup do Azure determina as alterações, calculando a soma de verificação do bloco. Se um bloco for alterado, ele é marcado para a transferência para o cofre. O serviço analisa blocos identificados para tentar minimizar a quantidade de dados a serem transferidos. Depois de avaliar os blocos alterados, o Backup do Azure transfere as alterações para o cofre.<br/><br/> Pode haver um retardo entre a captura do instantâneo e a sua cópia para o cofre.<br/><br/> Em horários de pico, pode levar até oito horas para backups a serem processados. O tempo de backup para uma VM será menor que 24 horas para o backup diário.
**Backup inicial** | Embora o tempo total de backup para backups incrementais é menor que 24 horas, que pode não ser o caso para o primeiro backup. O tempo necessário para o backup inicial dependerá do tamanho dos dados e quando o backup é processado.
**Restaurar a fila** | Processos de Backup do Azure trabalhos de restauração de várias contas de armazenamento ao mesmo tempo, e ele coloca as solicitações de restauração em uma fila.
**Restaurar a cópia** | Durante o processo de restauração, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo total de restauração depende das operações de e/s por segundo (IOPS) e a taxa de transferência da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não esteja carregada com outras leituras e gravações de aplicativo.

### <a name="backup-performance"></a>Desempenho do backup

Esses cenários comuns podem afetar o tempo total de backup:

- **Adicionando um novo disco a uma VM do Azure protegida:** Se uma VM está passando por backup incremental e um novo disco é adicionado, aumentará o tempo de backup. O tempo total de backup pode durar mais de 24 horas devido a replicação inicial do novo disco, juntamente com a replicação delta de discos existentes.
- **Discos fragmentados:** Operações de backup são mais rápidas quando as alterações de disco são contíguas. Se as alterações forem distribuídas e fragmentadas em um disco, o backup será mais lento. 
- **Variação de disco:** Se protegido discos que estão passando por backup incremental tem uma variação de diária de mais de 200 GB, backup pode levar muito tempo (mais de oito horas) para concluir. 
- **Versões de backup:** A versão mais recente do Backup (conhecida como a versão a restauração instantânea) usa um processo mais otimizado comparação de soma de verificação para identificar as alterações. Mas se você estiver usando a restauração instantânea e ter excluído um instantâneo de backup, o backup alterna para comparação de soma de verificação. Nesse caso, a operação de backup será exceder 24 horas (ou falha).

## <a name="best-practices"></a>Práticas recomendadas

Quando você estiver configurando backups de VM, é recomendável seguir estas práticas:

- Modifique os horários de agendamento padrão que são definidos em uma política. Por exemplo, se o tempo padrão na diretiva é 12:00 AM, incremente o tempo por vários minutos para que os recursos são usados de forma ideal.
- Para backup de VMs que estão usando o armazenamento premium, é recomendável executar a versão mais recente do Backup do Azure ([a restauração instantânea](backup-instant-restore-capability.md)). Se você não estiver executando a versão mais recente, o Backup aloca aproximadamente 50 por cento do espaço de armazenamento total. O serviço de Backup exige que esse espaço para copiar o instantâneo para a mesma conta de armazenamento e para transferi-la para o cofre.
- Se você estiver restaurando VMs de um único cofre, é altamente recomendável que você use diferentes [contas de armazenamento de uso geral v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) para garantir que a conta de armazenamento de destino não seja limitada. Por exemplo, cada VM deve ter uma conta de armazenamento diferente. Por exemplo, se 10 VMs forem restauradas, use 10 contas de armazenamento diferente.
- As restaurações de uma camada de armazenamento de uso geral v1 (snapshot) serão concluídas em minutos porque o instantâneo está na mesma conta de armazenamento. As restaurações de camada de armazenamento de uso geral v2 (cofre) podem levar horas. Em casos em que os dados estão disponíveis no armazenamento de uso geral v1, recomendamos que você use o [a restauração instantânea](backup-instant-restore-capability.md) recurso para obter restaurações mais rápidas. (Se os dados devem ser restaurados de um cofre, em seguida, ele levará mais tempo.)
- O limite no número de discos por conta de armazenamento é relativo ao quanto os discos estão sendo acessados pelos aplicativos que são executados em uma infraestrutura como serviço (IaaS) VM. Como prática geral, se os discos de 5 a 10 ou mais estiverem presentes em uma única conta de armazenamento, balancear a carga, movendo alguns discos para separar as contas de armazenamento.

## <a name="backup-costs"></a>Custos de backup

VMs do Azure cujo backup foi feito com o Backup do Azure estão sujeitas aos [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

A cobrança não é iniciado até que o primeiro backup bem-sucedido seja concluído. Neste ponto, a cobrança de VMs protegidas e de armazenamento é iniciada. A cobrança continuará desde que os dados de backup para a máquina virtual são armazenados em um cofre. Se você interromper a proteção para uma VM, mas os dados de backup para a VM existirem em um cofre, a cobrança continuará.

A cobrança para uma VM especificada parará somente se a proteção for interrompida e os dados de backup forem excluídos. Quando a proteção for interrompido e não houver nenhum trabalho de backup ativo, o tamanho do último backup bem-sucedido de VM torna-se o tamanho da instância protegida usada para a fatura mensal.

O cálculo do tamanho da instância protegida se baseia a *real* tamanho da VM. O tamanho da VM é a soma de todos os dados na VM, excluindo o armazenamento temporário. Preço é baseado nos dados reais que tem armazenados nos discos de dados, não no máximo tamanho com suporte para cada disco de dados é anexado à VM.

Da mesma forma, a cobrança de armazenamento de backup é baseada na quantidade de dados que são armazenados no Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, dê uma VM em tamanho A2 Standard que tem dois discos de dados adicionais com um tamanho máximo de 4 TB. A tabela a seguir mostra os dados reais armazenados em cada um desses discos:

**Disco** | **Tamanho máximo** | **Dados reais presentes**
--- | --- | ---
Disco do sistema operacional | 4095 GB | 17 GB
Disco local/temporário | 135 GB | 5 GB (não incluído no backup)
Disco de dados 1 | 4095 GB | 30 GB
Disco de dados 2 | 4095 GB | 0 GB

O tamanho real da VM neste caso é de 17 GB + 30 GB + 0 GB = 47 GB. Esse tamanho de instância protegida (47 GB) se torna a base para a fatura mensal. À medida que aumenta a quantidade de dados na VM, o tamanho da instância protegida usada para alterações na cobrança para corresponder.

## <a name="next-steps"></a>Próximas etapas

Agora, [preparar para backup de VM do Azure](backup-azure-arm-vms-prepare.md).
