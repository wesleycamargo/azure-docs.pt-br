---
title: Planejar sua infraestrutura de backup da VM no Azure | Microsoft Docs
description: "Considerações importantes ao planejar o backup de máquinas virtuais no Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "backup de vms, backup de máquinas virtuais"
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2017
ms.author: markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 223d940b82c3e28926f959bb721e63c467090834
ms.lasthandoff: 03/22/2017


---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planejar sua infraestrutura de backup da VM no Azure
Este artigo fornece sugestões de desempenho e recursos para ajudá-lo a planejar a infraestrutura de backup da VM. Ele também define os principais aspectos do serviço de Backup. Esses aspectos podem ser críticos para determinar a arquitetura, o planejamento de capacidade e o agendamento. Se você [preparou o ambiente](backup-azure-vms-prepare.md), o planejamento será a próxima etapa antes de começar a [fazer backup das VMs](backup-azure-vms.md). Se você precisa de mais informações sobre máquinas virtuais do Azure, confira a [documentação da Máquina Virtual](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Como o Azure faz backup de máquinas virtuais?
Quando o serviço de Backup do Azure inicia um trabalho de backup no horário agendado, ele dispara a extensão de backup para obter um instantâneo pontual. No caso de VMs Windows, um instantâneo é criado em conjunto com o VSS (Serviço de Cópias de Sombra de Volume) para obter um instantâneo consistente dos discos na máquina virtual sem precisar fechá-la. Para VMs Linux, os clientes podem escrever próprios scripts personalizados que serão invocados durante a criação de um instantâneo de VM, a fim de garantir a consistência, conforme descrito adiante neste artigo. 

Depois que o instantâneo é criado, os dados são transferidos pelo serviço do Backup do Azure para o cofre de backup. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde o último backup.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

> [!NOTE]
> O Backup do Azure não inclui o disco temporário conectado à máquina virtual ao fazer backup. Saiba mais sobre [disco temporário](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)
>
>

### <a name="data-consistency"></a>Consistência de dados
Fazer backup e restaurar dados corporativos críticos é complicado porque os dados críticos precisam ter o backup feito enquanto os aplicativos que produzem os dados estão em execução. Para resolver isso, o Backup do Azure dá suporte a backups consistentes com o aplicativo para VMs Windows e Linux
#### <a name="windows-vm"></a>VM Windows
O Backup do Azure realiza backups completos de VSS em VMs do Windows (leia mais sobre [backup completo de VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Para permitir backups de cópia do VSS, a chave do Registro a seguir precisa ser definida na VM.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>VMs Linux
O Backup do Azure fornece uma estrutura que oferece aos clientes a flexibilidade para controlar seus fluxos de trabalho de backup e ambientes executando pré-script e pós-script personalizados como parte do backup da VM, garantindo a consistência do aplicativo. O pré-script será invocado antes da criação do instantâneo de VM e o pós-script será invocado após a conclusão do instantâneo de VM. Para obter mais detalhes, consulte [Backup de VM Linux consistente com o aplicativo usando pré-script e pós-script](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). 
> [!NOTE]
> O Backup do Azure invoca somente o pré-script e pós-script escritos pelo cliente; a consistência do aplicativo precisa ser garantida por esses scripts controlados pelo cliente. O Backup do Azure marcará o ponto de recuperação como consistente com o aplicativo se o pré-script e o pós-script forem executados com êxito. 
>


Esta tabela explica os tipos de consistência e as condições sob as quais elas ocorrem durante o backup da VM do Azure e os procedimentos de restauração.

| Consistência | Baseado em VSS | Explicação e detalhes |
| --- | --- | --- |
| Consistência de aplicativo |Sim para Windows|A consistência do aplicativo é ideal para cargas de trabalho, pois garante que:<ol><li> a VM *seja iniciada*. <li>Não há *corrupção*. <li>*Não há perda de dados*.<li> Os dados são consistentes com o aplicativo que usa os dados, envolvendo o aplicativo no momento do backup – usando o VSS ou o pré/pós-script.</ol> <li>*VMs Windows* – a maioria das cargas de trabalho da Microsoft têm gravadores VSS que executam ações específicas à carga de trabalho relacionadas à consistência dos dados. Por exemplo, o Microsoft SQL Server tem um gravador VSS que garante que as gravações no arquivo de log de transações e no banco de dados sejam realizadas corretamente. Para backups de VM Windows do Azure, para criar um ponto de recuperação consistente com o aplicativo, a extensão de backup deve invocar o fluxo de trabalho do VSS e concluí-lo antes da criação do instantâneo de VM. Para que o instantâneo de VM do Azure seja preciso, os gravadores VSS de todos os aplicativos de VM do Azure também devem ser concluídos. (Aprenda as [noções básicas do VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) e aprofunde-se nos detalhes de [como ele funciona](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx).) </li> <li> *VMs Linux* – os clientes podem executar o [pré-script e pós-script personalizados para garantir a consistência do aplicativo](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Consistência do sistema de arquivos |Sim - para computadores baseados em Windows |Há dois cenários em que o ponto de recuperação pode ser *consistente com o sistema de arquivos*:<ul><li>Backups de VMs Linux no Azure, sem pré-script/pós-script ou se o pré-script/pós-script falhou. <li>Falha do VSS durante o backup de VMs Windows no Azure.</li></ul> Em ambos os casos, o melhor que se pode fazer é garantir que: <ol><li> a VM *seja iniciada*. <li>Não há *corrupção*.<li>*Não há perda de dados*.</ol> Os aplicativos precisam implementar seu próprio mecanismo de "correção" nos dados restaurados. |
| Consistência de falhas |Não |Essa situação é equivalente a uma máquina virtual tendo uma "falha" (por meio de uma reinicialização forçada ou flexível). A consistência de falhas geralmente ocorre quando a máquina virtual do Azure está desligada no momento do backup. Um ponto de recuperação com controle de falhas não garante a consistência dos dados no meio de armazenamento – tanto da perspectiva do sistema operacional quanto do aplicativo. Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup. <br/> <br/> Embora não haja nenhuma garantia, em geral, o sistema operacional é inicializado, seguido pelo procedimento de verificação de disco, como chkdsk, para corrigir erros de danos. Todos os dados ou gravações na memória que não foram transferidos para o disco serão perdidos. O aplicativo geralmente segue com seu próprio mecanismo de verificação, caso seja necessário realizar reversão de dados. <br><br>Por exemplo, se o log de transações tiver entradas que não estão presentes no banco de dados, o software de banco de dados faz uma reversão até que os dados fiquem consistentes. Quando os dados são distribuídos entre vários discos virtuais (como volumes estendidos), um ponto de recuperação consistente quanto a falhas não garante a exatidão dos dados. |

## <a name="performance-and-resource-utilization"></a>Desempenho e utilização de recursos
Assim como o software de backup que é implantado localmente, você deve se planejar em termos das necessidades de utilização de recursos e capacidade ao fazer o backup de VMs no Azure. Os [Limites de armazenamento do Azure](../azure-subscription-service-limits.md#storage-limits) definem como estruturar as implantações de VM para obter o máximo de desempenho com o mínimo de impacto na execução de cargas de trabalho.

Preste atenção aos seguintes limites de Armazenamento do Azure durante o planejamento de desempenho de backup:

* Egresso máximo por conta de armazenamento
* Taxa de solicitação total por conta de armazenamento

### <a name="storage-account-limits"></a>Limites da conta de armazenamento
Sempre que os dados de backup são copiados de uma conta de armazenamento, eles são contados nas métricas de IOPS (operações de entrada/saída por segundo) e saída (ou taxa de transferência) da conta de armazenamento. Ao mesmo tempo, as máquinas virtuais também consomem IOPS e taxa de transferência. A meta é garantir que o tráfego de backup e de máquina virtual não exceda os limites da conta de armazenamento.

### <a name="number-of-disks"></a>Número de discos
O processo de backup tenta concluir um trabalho de backup o mais rápido possível. Dessa forma, ele consome o mínimo de recursos possível. No entanto, todas as operações de E/S são limitadas pela *Taxa de transferência de destino para blob único*, que tem um limite de 60 MB por segundo. Na tentativa de maximizar sua velocidade, o processo de backup tenta fazer backup de cada um dos discos da VM *em paralelo*. Se uma VM tiver quatro discos, o serviço tentará fazer backup dos quatro discos em paralelo. O fator mais importante para determinar o tráfego de backup que sai de uma conta de armazenamento do cliente é o **número de discos** que são copiados em backup.

### <a name="backup-schedule"></a>Agendamento de backup
Um fator adicional que afeta o desempenho é o **agendamento de backup**. Se configurar as políticas para que seja feito backup de todas as VMs ao mesmo tempo, você terá agendado um congestionamento no tráfego. O processo de backup tentará fazer backup de todos os discos em paralelo. Uma maneira de reduzir o tráfego de backup de uma conta de armazenamento é garantir que o backup das VMs diferentes seja feito em diferentes momentos do dia, sem sobreposição.

## <a name="capacity-planning"></a>Planejamento da capacidade
Reunindo os fatores anteriores, você precisa planejar as necessidades de uso da conta de armazenamento. Baixe a [planilha do Excel de planejamento de capacidade de backup da VM](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver o impacto do disco rígido e as opções de agendamento de backup.

### <a name="backup-throughput"></a>Taxa de transferência de backup
Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados (backup incremental). A tabela a seguir mostra os valores médios da taxa de transferência do serviço de Backup. Ao usar os dados a seguir, é possível estimar o tempo necessário para fazer backup de um disco de um tamanho específico.

| Operação de backup | Melhor taxa de transferência possível |
| --- | --- |
| Backup inicial |160 Mbps |
| Backup incremental (DR) |640 Mbps  <br><br> A taxa de transferência é reduzida consideravelmente se os dados alterados (que precisam ser copiados em backup) são espalhados pelo disco.|

## <a name="total-vm-backup-time"></a>Tempo total de backup da VM
Embora a maioria do tempo de backup seja gasto na leitura e cópia de dados, outras operações contribuem para o tempo total necessário para o backup de uma VM:

* Tempo necessário para [instalar ou atualizar a extensão de backup](backup-azure-vms.md).
* Hora do instantâneo, que é o tempo levado para disparar um instantâneo. Os instantâneos são disparados próximo ao horário de backup agendado.
* Tempo de espera da fila. Quando o serviço de backup estiver processando backups de vários clientes, a cópia de dados de backup do instantâneo para o cofre do backup ou dos Serviços de Recuperação poderá não ser iniciada imediatamente. Em períodos de pico de carga, a espera pode se alongar a até oito horas devido ao número de backups sendo processados. No entanto, o tempo total de backup da VM é inferior a 24 horas para políticas de backup diárias.
* Tempo de transferência de dados, o tempo necessário para o serviço de backup computar as alterações incrementais do backup anterior e transferir essas alterações para o armazenamento de cofre.

### <a name="why-am-i-observing-longer15-hours-backup-time"></a>Por que eu estou observando tempos de backup mais longos (mais de 15 horas)?
O backup consiste em duas fases: criação de instantâneos e transferência dos instantâneos para o cofre. O serviço de Backup otimiza o armazenamento. Ao transferir os dados de instantâneo para um cofre, o serviço transfere apenas as alterações incrementais do instantâneo anterior.  Para determinar as alterações incrementais, o serviço calcula a soma de verificação dos blocos. Se um bloco é alterado, o bloco é identificado como um bloco a ser enviado para o cofre. Em seguida, o serviço faz uma busca mais detalhada em cada um dos blocos identificados, procurando oportunidades para minimizar os dados a serem transferidos. Depois de avaliar todos os blocos alterados, o serviço une as alterações e as envia para o cofre. No caso de alguns aplicativos herdados, gravações pequenas e fragmentadas não são ideais para armazenamento. Se o instantâneo contém várias gravações pequenas e fragmentadas, o serviço gasta mais tempo no processamento dos dados gravados pelos aplicativos. O bloco de gravação do aplicativo recomendado do Azure, para aplicativos executados dentro da VM, é de, no mínimo, 8 KB. Se o aplicativo usar um bloco inferior a 8 KB, o desempenho do backup será afetado. Para obter ajuda com o ajuste do aplicativo para melhorar o desempenho do backup, confira [Ajustando aplicativos para o desempenho ideal com o armazenamento do Azure](../storage/storage-premium-storage-performance.md). Embora o artigo sobre desempenho do backup use exemplos do armazenamento Premium, as diretrizes são aplicáveis a discos de armazenamento Standard.

## <a name="total-restore-time"></a>Tempo total de restauração
Uma operação de restauração consiste em duas subtarefas principais: a cópia de dados do cofre para a conta de armazenamento do cliente escolhida e a criação da máquina virtual. A cópia de dados do cofre depende do local em que os backups estão armazenados internamente no Azure e do local em que a conta de armazenamento do cliente está armazenada. O tempo necessário para copiar os dados depende de:
* Tempo de espera de fila – como o serviço processa trabalhos de restauração de vários clientes ao mesmo tempo, as solicitações de restauração são colocadas em uma fila.
* Tempo de cópia de dados – se necessário, os dados são copiados primeiro do cofre para a conta de armazenamento do cliente. Se os dados precisarem ser copiados para a conta de armazenamento do cliente antes que o serviço de backup grave dados do cofre, o tempo de cópia aumentará. Para reduzir o tempo de cópia durante o processo de restauração, selecione uma conta de armazenamento não carregada com outras leituras e gravações de aplicativo.

## <a name="best-practices"></a>Práticas recomendadas
É recomendável seguir essas práticas ao configurar backups de máquinas virtuais:

* Não agende mais de 10 VMs clássicas do mesmo serviço de nuvem para fazer backup ao mesmo tempo. Sugerimos escalonar os horários de início de backup em uma hora caso você queira fazer backup de várias VMs do mesmo serviço de nuvem.
* Não agende mais de 40 VMs para fazer backup ao mesmo tempo.
* Agende os backups das VMs para horários fora de pico, para que o serviço de backup obtenha IOPS para transferir dados da conta de armazenamento do cliente para o cofre de backup ou dos Serviços de Recuperação.
* Certifique-se de que uma política seja aplicada às VMs distribuídas entre diferentes contas de armazenamento. Sugerimos que não mais do que 20 discos de uma única conta de armazenamento sejam protegidos pelo mesmo agendamento de backup. Se você tiver mais de 20 discos em uma conta de armazenamento, distribua as VMs por várias políticas para obter o IOPS necessário durante a fase de transferência do processo de backup.
* Não restaure uma VM em execução no armazenamento Premium para a mesma conta de armazenamento. Se o processo de operação de restauração coincidir com a operação de backup, ele reduzirá o IOPS disponível para backup.
* Recomendamos a execução de cada VM Premium em uma conta de armazenamento premium distinta para garantir um melhor desempenho de backup.

## <a name="data-encryption"></a>Criptografia de dados
O Backup do Azure não criptografa os dados como parte do processo de backup. No entanto, você pode criptografar os dados dentro da VM e fazer backup dos dados protegidos diretamente (leia mais sobre [backup de dados criptografados](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Calculando o custo de instâncias protegidas
As máquinas virtuais do Azure submetidas a backup por meio do Backup do Azure estão sujeitas aos [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/). O cálculo de Instâncias Protegidas se baseia no tamanho *real* da máquina virtual, que é a soma de todos os dados na máquina virtual – excluindo o “disco de recursos”.

A cobrança para o backup de VMs *não* se baseia no tamanho máximo com suporte para cada disco de dados anexado à máquina virtual. A cobrança baseia-se nos dados reais armazenados no disco de dados. Da mesma forma, a cobrança do armazenamento de backup é baseada na quantidade de dados armazenados com o Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, considere uma máquina virtual de tamanho A2 Standard que tem dois discos de dados adicionais com um tamanho máximo de 1 TB cada. A seguinte tabela fornece os dados reais armazenados em cada um desses discos:

| Tipo de disco | Tamanho máx. | Dados reais presentes |
| --- | --- | --- |
| Disco do sistema operacional |1023 GB |17 GB |
| Disco local/disco de recursos |135 GB |5 GB (não incluído no backup) |
| Disco de dados 1 |1023 GB |30 GB |
| Disco de dados 2 |1023 GB |0 GB |

Neste caso, o tamanho *real* da máquina virtual é de 17 GB + 30 GB + 0 GB = 47 GB. Esse tamanho de Instância Protegida (47 GB) se torna a base para a fatura mensal. Conforme aumenta a quantidade de dados na máquina virtual, o tamanho da Instância Protegida usada para cobrança também será alterado de forma adequada.

A cobrança só é iniciada quando o primeiro backup bem-sucedido é concluído. Neste ponto, a cobrança de Instâncias Protegidas e de Armazenamento é iniciada. A cobrança continuará desde que haja *dados de backup armazenados com o Backup do Microsoft Azure* para a máquina virtual. Executar a operação Parar Proteção não interrompe a cobrança se os dados de backup forem mantidos.

A cobrança para uma máquina virtual especificada será descontinuada somente se a proteção for interrompida *e* os dados de backup forem excluídos. Quando não há nenhum trabalho de backup ativo (quando a proteção tiver sido interrompida), o tamanho da máquina virtual no momento do último backup bem-sucedido torna-se o tamanho da Instância Protegida que é usado como a base para a fatura mensal.

## <a name="questions"></a>Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Próximas etapas
* [Backup de máquinas virtuais](backup-azure-vms.md)
* [Gerenciar o backup de máquinas virtuais](backup-azure-manage-vms.md)
* [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
* [Solucionar problemas de backup da VM](backup-azure-vms-troubleshoot.md)

