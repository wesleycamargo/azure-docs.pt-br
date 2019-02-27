---
title: Sobre o backup de VM do Azure
description: Saiba mais sobre o backup de VM do Azure e observe algumas melhores práticas.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c38c457bbf428d7252cf57168685201a2ca227ba
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446793"
---
# <a name="about-azure-vm-backup"></a>Sobre o backup de VM do Azure

Este artigo descreve como o [serviço de Backup do Azure](backup-introduction-to-azure-backup.md) faz backup de VMs do Azure.

## <a name="backup-process"></a>Processo de backup

Veja como o Backup do Azure completa um backup de VMs do Azure.

1. Para as VMs do Azure selecionadas para backup, o serviço de Backup do Azure inicia um trabalho de backup de acordo com o agendamento de backup especificado por você.
2. Durante o primeiro backup, uma extensão de backup é instalada na VM se ela estiver em execução.
    - Em VMs do Windows, a extensão _VMSnapshot_ já está instalada.
    - Em VMs do Linux, a extensão _VMSnapshotLinux_ já está instalada.
3. Em VMs do Windows que estejam em execução, o Backup entra em coordenação com o VSS para criar um instantâneo consistente com o aplicativo da VM.
    - Por padrão, o Backup do Azure obtém backups completos de VSS.
    - Se o Backup não puder capturar um instantâneo consistente do aplicativo, ele capturará um instantâneo consistente com o arquivo do armazenamento subjacente (já que nenhuma gravação de aplicativo ocorre enquanto a VM está parada).
4. O Backup de VMs do Linux obtém um backup consistente com o arquivo. Para obter instantâneos consistentes de aplicativos, personalize manualmente os pré/pós-scripts.
5. Após o instantâneo, os dados são transferidos para o cofre. 
    - O backup é otimizado pela execução do backup em cada disco de VM em paralelo.
    - Para cada disco que está passando por backup, o Backup do Azure lê os blocos no disco e identifica e transfere apenas os blocos de dados que foram alterados desde o backup anterior (o delta).
    - Após o instantâneo, os dados são transferidos para o cofre.
    - Dados de instantâneo não podem ser imediatamente copiados no cofre. Esse processo pode levar algumas horas em horários de pico. O tempo total de backup da VM será de menos de 24 horas para políticas de backup diárias.

6. Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Criptografia de backups de VM do Azure

Quando você faz backup de VMs do Azure com o Backup do Azure, máquinas virtuais são criptografadas em repouso com SSE (Criptografia do Serviço de Armazenamento). Além disso, o Backup do Azure pode fazer backup de VMs criptografadas do Azure usando o ADE (Azure Disk Encryption).


**Criptografia** | **Detalhes** | **Suporte**
--- | --- | ---
**ADE** | ADE criptografa discos de dados e sistema operacional para VMs do Azure.<br/><br/> O ADE integra-se com BEK (chaves de criptografia do BitLocker) protegidas em um cofre de chaves como segredos ou com KEK (chaves de criptografia de chave) do Azure Key Vault. | O Backup do Azure dá suporte ao backup de VMs do Azure gerenciadas e não gerenciadas, criptografadas apenas com BEK ou com BEK junto com KEK.<br/><br/> BEK passa por backup e é criptografada.<br/><br/> Como KEK e BEK passam por backup, se necessário, usuários com permissões podem restaurar chaves e segredos para o cofre de chaves e recuperar a VM criptografada.<br/><br/> Segredos e chaves criptografadas não podem ser lido por usuários não autorizados ou pelo Azure.
**SSE** | Com SSE, o armazenamento do Azure fornece criptografia em repouso criptografando automaticamente os dados antes de armazená-los e descriptografando-os antes da recuperação. | O Backup do Azure usa a SSE para criptografia em repouso de VMs do Azure.

- Há suporte para backup de VMs criptografadas apenas com a BEK (Chave de Criptografia BitLocker), e com BEK junto com KEK (Chave de Criptografia de Chave), para VMs do Azure gerenciadas e não gerenciadas.
- Os backups dos KEK (chaves) e BEK (segredos) são criptografados. Eles podem ser lidos e usados somente quando restaurados para o cofre de chaves por usuários autorizados. Nem usuários não autorizados nem o Azure podem ler nem usar backup de chaves nem segredos.
- Como também ocorre o backup da BEK, se a BEK for perdida, os usuários autorizados poderão restaurar a BEK para o KeyVault e recuperar a VM criptografada. 
- Somente os usuários com o nível correto de permissões podem fazer backup e restaurar VMs criptografadas, bem como chaves e segredos.



## <a name="taking-snapshots"></a>Capturando instantâneos

Instantâneos de Backup do Azure de acordo com o agendamento de backup. 

- **VMs do Windows**: Para VMs do Windows, o serviço de Backup é coordenado com o serviço VSS (Serviço de Cópias de Sombra de Volume) para capturar um instantâneo consistente com o aplicativo dos discos da VM.
    - Por padrão, o Backup do Azure usa backups completos de VSS. [Saiba mais](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Se você quiser alterar a configuração para que os Backups do Azure usem backups de cópia de VSS, defina a seguinte chave do Registro de um prompt de comando: **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **VMs Linux**: Se você quiser capturar instantâneos consistentes de aplicativos de VM do Linux, use a estrutura de pré-script e pós-script do Linux para escrever seus próprios scripts personalizados para garantir a consistência.
    -  O Backup do Azure invoca apenas pré e pós-scripts escritos por você.
    - Se os pré e pós-scripts forem executados com êxito, o Backup do Azure marcará o ponto de recuperação como consistente com o aplicativo. No entanto, você é responsável pela consistência do aplicativo ao usar scripts personalizados.
    - [Saiba mais](backup-azure-linux-app-consistent.md) sobre a configuração de scripts.


### <a name="snapshot-consistency"></a>Consistência do instantâneo

A tabela a seguir explica os diferentes tipos de consistência.

**Instantâneo** | **Detalhes** | **Recuperação** | **Consideração**
--- | --- | --- | ---
**Consistente com o aplicativo** | Backups consistentes com o aplicativo capturam o conteúdo da memória e operações de E/S pendentes. Instantâneos consistentes com o aplicativo usam o gravador VSS (ou pré/pós-script para Linux) que garantem a consistência dos dados do aplicativo antes do backup. | Ao recuperar com um instantâneo consistente com o aplicativo, a VM é inicializada. Não há perda ou corrupção de dados. O aplicativo é iniciado em um estado consistente. | Windows: Todos os gravadores VSS foram bem-sucedidos<br/><br/> Linux: Pré/pós-scripts estão configurados e foram bem-sucedidos
**Consistente com o sistema de arquivos** | Backups consistentes com o arquivo fornecem backups consistentes de arquivos de disco por meio de um instantâneo simultâneo de todos os arquivos.<br/><br/> | Ao recuperar usando um instantâneo consistente com o aplicativo, a VM é inicializada. Não há perda ou corrupção de dados. Os aplicativos precisam implementar seus próprios mecanismos de "correção" para garantir a consistência dos dados restaurados. | Windows: Alguns gravadores VSS falharam <br/><br/> Linux: Padrão (se os pré/pós-scripts não estão configurados ou falharam)
**Controle de falhas** | Normalmente, o controle de falhas ocorre quando uma VM do Azure é desligada no momento do backup.  Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup.<br/><br/> Um ponto de recuperação de controle de falhas não garante a consistência de dados para o sistema operacional ou o aplicativo. | Não há garantias, mas, geralmente, a VM é inicializada e executa uma verificação de disco para corrigir erros de corrupção. Quaisquer dados na memória ou gravações que não tenham sido transferidos para o disco serão perdidos. Os aplicativos implementam sua própria verificação de dados. Por exemplo, para um aplicativo de banco de dados, se o log de transações tiver entradas que não estão presentes no banco de dados, o software do banco de dados será executado até que os dados fiquem consistentes. | A VM está em estado de desligamento


## <a name="restore-considerations"></a>Considerações de restauração 



**Consideração** | **Detalhes**
--- | ---
**Disco** | O backup de disco da VM é paralelo. Por exemplo, se uma VM tiver quatro discos, o serviço tentará fazer backup dos quatro discos em paralelo. O backup é incremental (somente dados alterados).
**Agendamento** |  Para reduzir o tráfego de backup, faça backup de VMs diferentes em horários diferentes do dia, sem sobreposições. Fazer backup de VMs ao mesmo tempo provoca congestionamentos de tráfego.
**Preparando backups** | Considere o tempo de preparação de backup, que inclui instalar ou atualizar a extensão de backup e disparar um instantâneo de acordo com o agendamento de backup.
**Transferência de dados** | Tempo necessário para o serviço de backup computar as alterações incrementais do backup anterior.<br/><br/> Em um backup incremental, determine as alterações, os computadores de serviço e a soma de verificação do bloco. Se um bloco for alterado, ele será identificado para o envio para o cofre. O serviço detalha blocos identificados para tentar minimizar ainda mais os dados a serem transferidos. Após avaliar todos os blocos alterados, as alterações são transferidas para o cofre.<br/><br/> Pode haver um retardo entre a captura do instantâneo e a sua cópia para o cofre.<br/><br/> Em horários de pico, podem levar até oito horas para backups serem processados. O tempo de backup para uma VM será menor que 24 horas para o backup diário.
**Backup inicial** | Embora o tempo total de backup de menos de 24 horas seja válido para backups incrementais, talvez não seja para o primeiro backup. O tempo necessário dependerá do tamanho dos dados e do momento de execução do backup.
**Restaurar a fila** | Os processos de Backup do Azure restauram trabalhos de várias contas de armazenamento ao mesmo tempo e as solicitações de restauração são colocadas em uma fila.
**Restaurar a cópia** | Durante o processo de restauração, os dados são copiados do cofre para a conta de armazenamento.<br/><br/> O tempo de restauração depende da IOPS e da taxa de transferência da conta de armazenamento.<br/><br/> Para reduzir o tempo de cópia, selecione uma conta de armazenamento que não esteja carregada com outras leituras e gravações de aplicativo.


### <a name="backup-performance"></a>Desempenho do backup

Estes cenários comuns podem afetar o tempo de backup:

- Adicione um novo disco a uma VM do Azure protegida: Se uma VM estiver passando por backup incremental e um novo disco for adicionado, o backup poderá durar mais de 24 horas devido à replicação inicial do novo disco, juntamente com a replicação delta de discos existentes.
- Discos fragmentados: Operações de backup são mais rápidas quando as alterações de disco são colocadas. Se as alterações forem distribuídas e fragmentadas em um disco, o backup será mais lento. 
- Variação de disco: Se discos protegidos passando por backup incremental tiverem uma variação de diária de mais de 200 GB, o backup poderá levar muito tempo (mais de oito horas) para ser concluído. 
- Versões de backup: Se você estiver usando a versão mais recente do Backup (conhecida como a versão de Restauração Instantânea), ela usará um processo mais otimizado de comparação de soma de verificação para comparar as alterações. Se você estiver usando a versão mais recente e tiver excluído um instantâneo de backup, as opções de backup para usar a comparação de soma de verificação e a operação de backup excederão 24 horas (ou falharão).

## <a name="best-practices"></a>Práticas recomendadas 
Recomendamos seguir essas práticas ao configurar backups de VM:

- Considere modificar a hora do agendamento padrão definida em uma política. Por exemplo, se a hora padrão para a política for às 00h, considere a possibilidade de incrementá-la em minutos para que os recursos sejam usados de forma ideal.
- Para a VM Premium no recurso RP não Instantâneo, o backup aloca cerca de 50% do espaço total da conta de armazenamento. O serviço de backup exige que esse espaço copie o instantâneo para a mesma conta de armazenamento e para transferi-lo para o cofre.
- Para restaurar VMs de um único cofre, é altamente recomendável usar [contas de armazenamento v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) diferentes para garantir que a conta de armazenamento de destino não seja limitada. Por exemplo, cada VM precisa ter uma conta de armazenamento diferente (se 10 VMs forem restauradas, considere o uso de 10 contas de armazenamento diferentes).
- As restaurações da camada de armazenamento da Camada 1 (instantâneo) serão concluídas em minutos (pois essa é a mesma conta de armazenamento) em relação à camada de armazenamento da Camada 2 (cofre), que poderá levar várias horas. Recomendamos que você use o recurso [Restauração instantânea](backup-instant-restore-capability.md) para obter restaurações mais rápidas, nos casos em que os dados estão disponíveis na Camada 1 (se os dados precisarem ser restaurados do cofre, isso levará tempo).
- O limite no número de discos por conta de armazenamento é relativo à intensidade de acesso dos discos por aplicativos executados na VM IaaS. Verifique se vários discos são hospedados em uma única conta de armazenamento. Como prática geral, se 5 a 10 discos ou mais estiverem presentes em uma única conta de armazenamento, balanceie a carga movendo alguns discos para contas de armazenamento separadas.

## <a name="backup-costs"></a>Custos de backup

VMs do Azure cujo backup foi feito com o Backup do Azure estão sujeitas aos [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

- A cobrança só é iniciada quando o primeiro backup bem-sucedido é concluído. Neste ponto, a cobrança de VMs protegidas e de armazenamento é iniciada.
- A cobrança continuará desde que haja qualquer dado de backup armazenado em um cofre para a VM. Se você interromper a proteção para uma VM, mas os dados de backup para a VM existirem em um cofre, a cobrança continuará.
- A cobrança para uma VM especificada parará somente se a proteção for interrompida e os dados de backup forem excluídos.
- Quando a proteção for interrompido e não houver nenhum trabalho de backup ativo, o tamanho do último backup bem-sucedido de VM torna-se o tamanho da instância protegida usada para a fatura mensal.
- O cálculo de instâncias protegidas se baseia no tamanho *real* da VM, que é a soma de todos os dados na VM, excluindo o armazenamento temporário.
- O preço baseia-se nos dados reais armazenados no disco de dados.
- O preço para o backup de VMs não se baseia no tamanho máximo com suporte para cada disco de dados anexado à VM.
- Da mesma forma, a cobrança do armazenamento de backup é baseada na quantidade de dados armazenados no Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, leve uma VM A2 de tamanho padrão que tenha dois discos de dados adicionais com um tamanho máximo de 4 TB cada. A seguinte tabela fornece os dados reais armazenados em cada um desses discos:

**Disco** | **Tamanho máximo** | **Dados reais presentes**
--- | --- | ---
Disco do sistema operacional | 4095 GB | 17 GB 
Disco local/temporário | 135 GB | 5 GB (não incluído no backup) 
Disco de dados 1 | 4095 GB | 30 GB 
Disco de dados 2 | 4095 GB | 0 GB 

- O tamanho real da VM neste caso é de 17 GB + 30 GB + 0 GB = 47 GB.
- Esse tamanho de instância protegida (47 GB) se torna a base para a fatura mensal.
- Conforme aumenta a quantidade de dados na VM, o tamanho da instância protegida usada para cobrança também será alterado de acordo.


## <a name="next-steps"></a>Próximas etapas

Agora, [prepare](backup-azure-arm-vms-prepare.md)-se para o backup da VM do Azure.
