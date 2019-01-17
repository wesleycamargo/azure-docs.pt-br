---
title: Sobre o backup de VM do Azure
description: Saiba mais sobre o backup de VM do Azure e observe algumas melhores práticas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: cac219414418277ace09ba3a0b442f3bf74e6025
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107422"
---
# <a name="about-azure-vm-backup"></a>Sobre o backup de VM do Azure

Este artigo descreve como o [serviço de Backup do Azure](backup-introduction-to-azure-backup.md) faz backup de VMs do Azure.

## <a name="backup-process"></a>Processo de backup

Veja como o Backup do Azure completa um backup de VMs do Azure.

1. Para as VMs do Azure selecionadas para backup, o serviço de Backup do Azure inicia um trabalho de backup de acordo com o agendamento de backup especificado por você.
2. O serviço dispara a extensão de backup.
    - VMs do Windows usam a extensão _VMSnapshot_.
    - VMs do Linux usam a extensão _VMSnapshotLinux_.
    - A extensão é instalada durante o primeiro backup de VM.
    - Para instalar a extensão, a VM deve estar em execução.
    - Se a VM não estiver em execução, o serviço de Backup criará um instantâneo do armazenamento subjacente (já que nenhuma gravação de aplicativo ocorre enquanto a VM está parada).
4. A extensão de backup tira um instantâneo consistente com arquivo/consistente com falhas no nível do armazenamento.
5. Após o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde o backup anterior (o delta).
5. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Criptografia de dados

O Backup do Azure não criptografa os dados como parte do processo de backup. O Backup do Azure dá suporte ao backup de VMs do Azure criptografadas, usando o Azure Disk Encryption.

- Há suporte para backup de VMs criptografadas apenas com a BEK (Chave de Criptografia Bitlocker), e com BEK junto com KEK (Chave de Criptografia de Chave), para VMs do Azure gerenciadas e não gerenciadas.
- Tanto a BEK (segredos) quanto a KEK (chaves) submetidas a backup são criptografadas para que possam ser lidas e usadas somente quando restauradas no cofre de chaves por usuários autorizados.
- Como também ocorre o backup da BEK, em cenários nos quais a BEK é perdida, usuários autorizados podem restaurar a BEK no cofre de chaves e recuperar a VM criptografada. O backup de chaves e segredos de VMs criptografadas ocorre de forma criptografada, de modo que usuários não autorizados, nem o Azure, podem ler ou usar chaves e segredos em backup. Somente os usuários com o nível correto de permissões podem fazer backup e restaurar VMs criptografadas, bem como chaves e segredos.

## <a name="snapshot-consistency"></a>Consistência do instantâneo

Se você quiser instantâneos durante a execução de aplicativos, o Backup do Azure tira instantâneos consistentes com o aplicativo.

- **VMs do Windows**: Para VMs do Windows, o serviço de Backup é coordenado com o serviço VSS (Serviço de Cópias de Sombra de Volume) para obter um instantâneo consistente dos discos da VM.
    - Por padrão, o Backup do Azure usa backups completos de VSS. [Saiba mais](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Se você quiser alterar a configuração para que os Backups do Azure usem backups de cópia de VSS, defina a seguinte chave do Registro:
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
- **VMs Linux**: Para garantir que suas VMs do Linux sejam consistentes com o aplicativo quando o Backup do Azure tirar um instantâneo, use a estrutura de pré-script e pós-script do Linux. Escreva seus próprios scripts personalizados para garantir a consistência ao gerar um instantâneo de VM.
    -  O Backup do Azure invoca apenas os pré e pós-scripts escritos por você.
    - Se os pré e pós-scripts forem executados com êxito, o Backup do Azure marcará o ponto de recuperação como consistente com o aplicativo. No entanto, você é responsável pela consistência do aplicativo ao usar scripts personalizados.
    - [Saiba mais](backup-azure-linux-app-consistent.md) sobre a configuração de scripts.


#### <a name="consistency-types"></a>Tipos de consistência

A tabela a seguir explica os diferentes tipos de consistência.

**Instantâneo** | **Baseado em VSS** | **Detalhes** | **Recuperação**
--- | --- | --- | ---
**Consistente com o aplicativo** | Sim (somente Windows) | Backups consistentes com o aplicativo capturam o conteúdo da memória e operações de E/S pendentes. Instantâneos consistentes com o aplicativo usam o gravador VSS (ou pré/pós-script para Linux) que garantem a consistência dos dados do aplicativo antes do backup. | Ao recuperar com um instantâneo consistente com o aplicativo, a VM é inicializada. Não há perda ou corrupção de dados. O aplicativo é iniciado em um estado consistente.
**Consistente com o sistema de arquivos** | Sim (somente Windows) |  Backups consistentes com o arquivo fornecem backups consistentes de arquivos de disco por meio de um instantâneo simultâneo de todos os arquivos.<br/><br/> Pontos de recuperação do Backup do Azure são consistentes com o arquivo para:<br/><br/> - Backups de VM do Linux que não têm pré/pós-scripts, ou cujo script falhou.<br/><br/> - Backups de VM do Windows nos quais o VSS falhou. | Ao recuperar usando um instantâneo consistente com o aplicativo, a VM é inicializada. Não há perda ou corrupção de dados. Os aplicativos precisam implementar seus próprios mecanismos de "correção" para garantir a consistência dos dados restaurados.
**Controle de falhas** | Não  | Normalmente, o controle de falhas ocorre quando uma VM do Azure é desligada no momento do backup.  Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup.<br/><br/> Um ponto de recuperação de controle de falhas não garante a consistência de dados para o sistema operacional ou o aplicativo. | Não há garantias, mas, geralmente, a VM é inicializada e executa uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou gravações que não tenham sido transferidos para o disco serão perdidos. Os aplicativos implementam sua própria verificação de dados. Por exemplo, para um aplicativo de banco de dados, se o log de transações tiver entradas que não estão presentes no banco de dados, o software do banco de dados será executado até que os dados fiquem consistentes.


## <a name="service-and-subscription-limits"></a>Limites de serviço e assinatura

O Backup do Azure tem diversos limites de assinaturas e cofres.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Desempenho do backup

### <a name="disk-considerations"></a>Considerações de disco

A operação de backup oferece otimizações fazendo backup de cada disco da VM em paralelo. Por exemplo, se uma VM tiver quatro discos, o serviço tentará fazer backup dos quatro discos em paralelo. Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados (backup incremental).


### <a name="scheduling-considerations"></a>Considerações de agendamento

O agendamento do backup afeta o desempenho.

- Se você configurar políticas para que todas as VMs sejam copiadas em backup ao mesmo tempo, você terá agendado um congestionamento, pois o processo de backup tentará fazer backup de todos os discos em paralelo.
- Para reduzir o tráfego de backup, faça backup de VMs diferentes em horários diferentes do dia, sem sobreposição.


### <a name="time-considerations"></a>Considerações sobre tempo

Embora a maior parte do tempo do backup seja usada na leitura e cópia de dados, outras operações contribuem para o tempo total necessário para o backup de uma VM:

- **Instalar extensão de backup**: Tempo necessário para instalar ou atualizar a extensão de backup.
- **Disparar instantâneo**: Tempo para disparar um instantâneo. Os instantâneos são disparados próximo ao horário de backup agendado.
- **Tempo de espera da fila**: Como o serviço Backup processa trabalhos de várias contas de armazenamento de cliente simultaneamente, talvez os dados do instantâneo não sejam imediatamente copiados para o cofre dos Serviços de Recuperação. Nos horários de pico, pode levar até oito horas para que os backups sejam processados. No entanto, o tempo total de backup da VM é inferior a 24 horas para políticas de backup diárias.
- **Backup inicial**: Embora o tempo total de backup de menos de 24 horas seja válido para backups incrementais, talvez não seja para o primeiro backup. O tempo necessário dependerá do tamanho dos dados e do momento de execução do backup.
- **Tempo de transferência de dados**: Tempo necessário para o serviço de backup computar as alterações incrementais do backup anterior e transferir essas alterações para o armazenamento de cofre.

### <a name="factors-affecting-backup-time"></a>Fatores que afetam o tempo do backup

O backup é composto por duas fases: criação de instantâneos e transferência dos instantâneos para o cofre. O serviço de Backup otimiza o armazenamento.

- Ao transferir os dados de instantâneo para um cofre, o serviço transfere apenas as alterações incrementais do instantâneo anterior.
- Para determinar as alterações incrementais, o serviço calcula a soma de verificação dos blocos.
    - Se um bloco é alterado, o bloco é identificado como um bloco a ser enviado para o cofre.
    - O serviço executa uma busca mais detalhada em cada um dos blocos identificados, procurando oportunidades para minimizar os dados a serem transferidos.
    - Depois de avaliar todos os blocos alterados, o serviço une as alterações e as envia para o cofre.

Entre as situações que podem afetar o tempo de backup estão as seguintes:

- **Backup inicial de um disco recém-adicionado a uma VM já protegida**: Se uma VM estiver passando por um backup incremental e um novo disco for adicionado a essa VM, a duração do backup poderá ultrapassar 24 horas, pois o disco recém-adicionado precisará passar pela replicação inicial, juntamente com a replicação delta dos discos existentes.
- **Fragmentação**: O produto de backup examina em busca de alterações incrementais entre duas operações de backup. As operações de backup são mais rápidas quando as alterações no disco são colocadas em comparação com as alterações distribuídas pelo disco. 
- **Variação**: Uma variação diária (para a replicação incremental) por disco superior a 200 GB pode levar aproximadamente mais de 8 horas para concluir a operação. Se a VM tiver mais de um disco e se um desses discos estiver levando mais tempo para ser copiado em backup, isso poderá afetar a operação de backup geral (ou resultar em uma falha). 
- **Modo CC (Comparação da Soma de Verificação)**: O modo CC é comparativamente mais lento do que o modo otimizado usado pelo RP Instantâneo. Se você já estiver usando o RP Instantâneo e tiver excluído os instantâneos da Camada 1, o backup alternará para o modo CC fazendo com que a operação de Backup exceda 24 horas (ou falhe).

## <a name="restore-considerations"></a>Considerações de restauração

Uma operação de restauração é composta por duas tarefas principais: copiar dados do cofre para a conta de armazenamento escolhida e criar a máquina virtual. O tempo necessário para copiar dados do cofre depende de onde os backups estão armazenados no Azure e do local da conta de armazenamento. O tempo necessário para copiar os dados depende de:

- **Tempo de espera da fila**: Como o serviço processa trabalhos de restauração de várias contas de armazenamento ao mesmo tempo, as solicitações de restauração são colocadas em uma fila.
- **Tempo de cópia de dados**: Os dados são copiados do cofre para a conta de armazenamento. O tempo de restauração depende do IOPS e da taxa de transferência da conta de armazenamento selecionada, que é usada pelo serviço Backup do Azure. Para reduzir o tempo de cópia durante o processo de restauração, selecione uma conta de armazenamento não carregada com outras leituras e gravações de aplicativo.

## <a name="best-practices"></a>Práticas recomendadas

Recomendamos seguir essas práticas ao configurar backups de VM:

- Atualizar os cofres para o RP Instantâneo. Analise esses [benefícios](backup-upgrade-to-vm-backup-stack-v2.md), [considerações](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade) e, em seguida, faça o upgrade seguindo estas [instruções](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).  
- Considere a possibilidade de modificar a hora da política padrão fornecida (por ex.: Se a hora da política padrão for 12h, considere a possibilidade de incrementá-la em minutos) quando os instantâneos de dados forem tirados, a fim de garantir que os recursos sejam usados de forma ideal.
- Para a VM Premium no recurso RP não Instantâneo, o backup aloca cerca de 50% do espaço total da conta de armazenamento. O serviço de backup exige que esse espaço copie o instantâneo para a mesma conta de armazenamento e para transferi-lo para o cofre.
- Para restaurar VMs de um único cofre, é altamente recomendável usar [contas de armazenamento v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) diferentes para garantir que a conta de armazenamento de destino não seja limitada. Por exemplo, cada VM precisa ter uma conta de armazenamento diferente (se 10 VMs forem restauradas, considere o uso de 10 contas de armazenamento diferentes).
- As restaurações da camada de armazenamento da Camada 1 (instantâneo) serão concluídas em minutos (pois essa é a mesma conta de armazenamento) em relação à camada de armazenamento da Camada 2 (cofre), que poderá levar várias horas. Recomendamos que você use o recurso [RP Instantâneo](backup-upgrade-to-vm-backup-stack-v2.md) para obter restaurações mais rápidas, nos casos em que os dados estão disponíveis na Camada 1 (se os dados precisarem ser restaurados do cofre, isso levará tempo).
- O limite no número de discos por conta de armazenamento é relativo à intensidade de acesso dos discos por aplicativos executados na VM IaaS. Verifique se vários discos são hospedados em uma única conta de armazenamento. Como prática geral, se 5 a 10 discos ou mais estiverem presentes em uma única conta de armazenamento, balanceie a carga movendo alguns discos para contas de armazenamento separadas.

## <a name="backup-costs"></a>Custos de backup

VMs do Azure cujo backup foi feito com o Backup do Azure estão sujeitas aos [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

- A cobrança só é iniciada quando o primeiro backup bem-sucedido é concluído. Neste ponto, a cobrança de Instâncias Protegidas e de Armazenamento é iniciada.
- A cobrança continuará desde que haja qualquer dado de backup armazenado em um cofre para a máquina virtual. Se você interromper a proteção na máquina virtual, mas os dados de backup de máquina virtual existirem em um cofre, a cobrança continuará.
- A cobrança para uma VM especificada parará somente se a proteção for interrompida e os dados de backup forem excluídos.
- Quando a proteção for interrompido e não houver nenhum trabalho de backup ativo, o tamanho do último backup bem-sucedido de VM torna-se o tamanho da instância protegida usada para a fatura mensal.
- O cálculo de Instâncias Protegidas se baseia no tamanho *real* da máquina virtual, que é a soma de todos os dados na máquina virtual, excluindo o armazenamento temporário.
- O preço baseia-se nos dados reais armazenados no disco de dados.
- O preço para o backup de VMs não se baseia no tamanho máximo com suporte para cada disco de dados anexado à máquina virtual.
- Da mesma forma, a cobrança do armazenamento de backup é baseada na quantidade de dados armazenados no Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, leve uma máquina virtual A2 de tamanho padrão que tenha dois discos de dados adicionais com um tamanho máximo de 4 TB cada. A seguinte tabela fornece os dados reais armazenados em cada um desses discos:

| Tipo de disco | Tamanho máx. | Dados reais presentes |
| --------- | -------- | ----------- |
| Disco do sistema operacional |4095 GB |17 GB |
| Disco local/disco temporário |135 GB |5 GB (não incluído no backup) |
| Disco de dados 1 |4095 GB |30 GB |
| Disco de dados 2 |4095 GB |0 GB |

- Neste caso, o tamanho real da máquina virtual é de 17 GB + 30 GB + 0 GB = 47 GB.
- Esse tamanho de Instância Protegida (47 GB) se torna a base para a fatura mensal.
- Conforme aumenta a quantidade de dados na máquina virtual, o tamanho da Instância Protegida usada para cobrança também será alterado de acordo.


## <a name="next-steps"></a>Próximas etapas

Depois de revisar o processo de backup e considerações de desempenho, faça o seguinte:

- [Saiba mais sobre](../virtual-machines/windows/premium-storage-performance.md) como ajustar aplicativos para obter o desempenho ideal com o armazenamento do Azure. O artigo se concentra no armazenamento Premium, mas também é aplicável para discos de armazenamento padrão.
- [Comece a usar](backup-azure-arm-vms-prepare.md) o backup revisando as limitações e suporte de VM, criando um cofre e preparando VMs para backup.
