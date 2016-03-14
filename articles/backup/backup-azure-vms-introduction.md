<properties
	pageTitle="Planejar sua infraestrutura de backup da VM no azure | Microsoft Azure"
	description="Considerações importantes ao planejar o backup de máquinas virtuais no Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="backup de vms, backup de máquinas virtuais"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Planejar sua infraestrutura de backup da VM no Azure
Este artigo aborda as coisas mais importantes para se lembrar ao planejar o backup de máquinas virtuais no Azure. Se você [preparou seu ambiente](backup-azure-vms-prepare.md), esta é a próxima etapa antes de começar [a fazer backup de VMs](backup-azure-vms.md). Se você precisa de mais informações sobre máquinas virtuais do Azure, confira a [documentação da Máquina Virtual](https://azure.microsoft.com/documentation/services/virtual-machines/)

## Como o Azure faz backup de máquinas virtuais?
Quando o serviço de Backup do Azure inicia um trabalho de backup no horário agendado, ele dispara a extensão de backup para obter um instantâneo pontual. Esse instantâneo é feito em conjunto com o VSS (Serviço de Cópia de Sombreamento de Volume) para obter um instantâneo consistente dos discos na máquina virtual sem ter que fechá-la.

Depois que o instantâneo é criado, os dados são transferidos pelo serviço do Backup do Azure para o cofre de backup. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que foram alterados desde o último backup.

![Arquitetura de backup de máquinas virtuais do Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Quando a transferência de dados é concluída, o instantâneo é removido e um ponto de recuperação é criado.

### Consistência de dados
Fazer backup e restaurar dados corporativos críticos é complicado pelo fato de que é necessário ser feito o backup enquanto os aplicativos que produzem os dados estão em execução. Para resolver isso, o Backup do Azure fornece backup consistente com o aplicativo para cargas de trabalho da Microsoft usando o VSS para garantir que os dados sejam gravados corretamente para armazenamento.

>[AZURE.NOTE] Em máquinas virtuais Linux, apenas os backups consistentes de arquivos são possíveis, já que o Linux não tem uma plataforma equivalente ao VSS.

O Backup do Azure realiza backups completos de VSS em VMs do Windows (leia mais sobre [backup completo de VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Para permitir backups de cópia de VSS, a chave de registro abaixo deve ser inserida na VM.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


Esta tabela explica os tipos de consistência e as condições sob as quais elas ocorrem durante o backup da VM do Azure e os procedimentos de restauração.

| Consistência | Baseado em VSS | Explicação e detalhes |
|-------------|-----------|---------|
| Consistência de aplicativo | Sim | Esse é o tipo de consistência ideal para cargas de trabalho da Microsoft porque garante que:<ol><li> a VM *seja iniciada*. <li>Não há *corrupção*. <li>Não há *perda de dados*.<li> Os dados são consistentes com o aplicativo que usa os dados, envolvendo o aplicativo no momento do backup e usando o VSS.</ol> A maioria das cargas de trabalho Microsoft tem gravadores VSS que executam ações específicas de carga de trabalho relacionadas a consistência dos dados. Por exemplo, o Microsoft SQL Server tem um gravador VSS que garante que as gravações no arquivo de log de transações e no banco de dados sejam realizadas corretamente.<br><br> Para backups da VM do Azure, obter um ponto de recuperação consistente no aplicativo significa que a extensão de backup foi capaz de invocar o fluxo de trabalho do VSS e ser concluída *corretamente* antes que o instantâneo da VM fosse tirado. Naturalmente, isso significa que os gravadores VSS de todos os aplicativos na VM do Azure também são chamados.<br><br>Aprenda as [noções básicas do VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) e aprofunde-se nos detalhes de [como ele funciona](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Consistência do sistema de arquivos | Sim - para computadores baseados em Windows | Há dois cenários em que o ponto de recuperação pode ser *consistente com o sistema de arquivos*:<ul><li>Backups de VMs do Linux no Azure, já que o Linux não tem uma plataforma equivalente ao VSS.<li>Falha do VSS durante o backup de máquinas virtuais do Windows no Azure.</li></ul> Em ambos os casos, o melhor que se pode fazer é garantir que: <ol><li> a VM seja *iniciada*. <li>Não haja *corrupção*.<li>Não haja *perda de dados*.</ol> Os aplicativos precisam implementar seu próprio mecanismo de "correção" nos dados restaurados.|
| Consistência de falhas | Não | Essa situação é equivalente a uma máquina virtual tendo uma "falha" (por meio de uma reinicialização forçada ou flexível). Isso geralmente acontece quando a máquina virtual do Azure está desligada no momento do backup. Para backup da máquina virtual do Azure, obter um ponto de recuperação consistente quanto a falhas significa que o Backup do Azure não dá nenhuma garantia de consistência dos dados no meio de armazenamento, seja da perspectiva do sistema operacional ou da perspectiva do aplicativo. Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup. <br/> <br/> Embora não haja garantia, na maioria dos casos, o sistema operacional será inicializado. Normalmente, isso é seguido por um procedimento de verificação de disco, como chkdsk, para corrigir qualquer erro de corrupção. Quaisquer dados na memória ou gravações que não tenham sido totalmente liberadas para o disco serão perdidas. O aplicativo geralmente segue com seu próprio mecanismo de verificação, caso seja necessário realizar reversão de dados. <br><br>Por exemplo, se o log de transações tiver entradas que não estão presentes no banco de dados, o software de banco de dados faz uma reversão até que os dados fiquem consistentes. Quando os dados são distribuídos entre vários discos virtuais (como volumes estendidos), um ponto de recuperação consistente quanto a falhas não garante a exatidão dos dados.|


## Desempenho e utilização de recursos
Como o software de backup que é implantado localmente, o backup de VMs no Azure também precisa ser planejado quanto à capacidade e à utilização de recursos. Os [Limites de armazenamento do Azure](azure-subscription-service-limits.md#storage-limits) definem como estruturar as implantações de VM para obter o máximo de desempenho com o mínimo de impacto na execução de cargas de trabalho.

Existem dois limites principais de Armazenamento do Azure que têm impacto sobre o desempenho do backup:

- Egresso máximo por conta de armazenamento
- Taxa de solicitação total por conta de armazenamento

### Limites da conta de armazenamento
Quando os dados de backup são copiados da conta de armazenamento do cliente, eles contam na direção das métricas de IOPS (operações de entrada/saída por segundo) e egresso (taxa de transferência de armazenamento) da conta de armazenamento. Ao mesmo tempo, as máquinas virtuais também estão em execução e consumindo (IOPS) e taxa de transferência. O objetivo é garantir que o tráfego total, de backup e de máquina virtual, não exceda os limites da conta de armazenamento.

### Número de discos
O processo de backup é ávido e tenta consumir tantos recursos quanto possível, com o objetivo de concluir o backup o mais rápido que puder. No entanto, todas as operações de E/S são limitadas pela *Taxa de transferência de destino para blob único*, que tem um limite de 60 MB por segundo. Para acelerar o processo de backup, são feitas tentativas de backup de cada disco da VM *paralelamente*. Portanto, se uma VM tem quatro discos, o Backup do Azure tentará fazer o backup de todos os quatro discos em paralelo. Devido a isso, o fator mais importante que determina o tráfego de backup de saída de uma conta de armazenamento do cliente é o **número de discos** cujo backup está sendo feito da conta de armazenamento.

### Agendamento de backup
Um fator adicional que afeta o desempenho é o **agendamento de backup**. Se você configurar todas as VMs para backup simultâneo, o número de discos cujo backup está sendo feito *em paralelo* aumentará, já que o Backup do Azure tenta realizar o backup do máximo possível de discos. Portanto, uma maneira de reduzir o tráfego de backup de uma conta de armazenamento é garantir que o backup das VMs diferentes seja feito em diferentes momentos do dia, sem sobreposição.

## Planejamento da capacidade
Reunir todos esses fatores significa que o uso da conta de armazenamento precisa ser planejado corretamente. Baixe a [planilha do Excel de planejamento de capacidade de backup da VM](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver o impacto do disco rígido e as opções de agendamento de backup.

### Taxa de transferência de backup
Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados (backup incremental). Essa tabela mostra os valores de taxa de transferência média que você pode esperar do Backup do Azure. Usando isso, você pode estimar a quantidade de tempo que leva para fazer o backup de um disco de um determinado tamanho.

| Operação de backup | Melhor taxa de transferência possível |
| ---------------- | ---------- |
| Backup inicial | 160 Mbps |
| Backup incremental (DR) | 640 Mbps <br><br> Essa taxa de transferência pode cair significativamente se houver muita variação dispersa no disco cujo backup precisa ser feito. |

### Tempo total de backup da VM
Embora a maioria do tempo seja gasto com a leitura e a cópia de dados, existem outras operações que contribuem para o tempo total gasto no backup de uma VM:

- Tempo necessário para [instalar ou atualizar a extensão de backup](backup-azure-vms.md#offline-vms).
- Hora do instantâneo, que é o tempo levado para disparar um instantâneo. Os instantâneos são disparados próximo ao horário de backup agendado.
- Tempo de espera da fila. Quando o serviço de backup estiver processando backups de vários clientes, a cópia de dados de backup do instantâneo para o Azure não será iniciada imediatamente. Em períodos de pico de carga, os tempos de espera podem se estender a até 8 horas devido ao número de backups sendo processados. No entanto, o tempo total de backup da VM será de menos de 24 horas para políticas de backup diárias.

## Criptografia de dados

O Backup do Azure não criptografa os dados como parte do processo de backup. No entanto, você pode criptografar os dados dentro da VM e fazer backup dos dados protegidos diretamente (leia mais sobre [backup de dados criptografados](backup-azure-vms-encryption.md)).


## Como as instâncias protegidas são calculadas?
As máquinas virtuais do Azure submetidas a backup por meio do Backup do Azure estão sujeitas aos [preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/). O cálculo de instâncias protegidas se baseia no tamanho *real* da máquina virtual, que é a soma de todos os dados na máquina virtual, exceto o "disco de recursos".

Você *não* é cobrado com base no tamanho máximo com suporte para cada disco de dados anexado à máquina virtual, mas sim nos dados reais armazenados no disco de dados. Da mesma forma, a cobrança do armazenamento de backup é baseada na quantidade de dados armazenados com o Backup do Azure, que é a soma dos dados reais em cada ponto de recuperação.

Por exemplo, considere uma máquina virtual de tamanho A2 Standard que tem dois discos de dados adicionais com um tamanho máximo de 1 TB cada. A tabela a seguir fornece os dados reais armazenados em cada um desses discos:

|Tipo de disco|Tamanho máx.|Dados reais presentes|
|---------|--------|------|
| Disco do sistema operacional | 1023 GB | 17 GB |
| Disco local/disco de recursos | 135 GB | 5 GB (não incluído no backup) |
| Disco de dados 1 |	1023 GB | 30 GB |
| Disco de dados 2 | 1023 GB | 0 GB |

Neste caso, o tamanho *real* da máquina virtual é de 17 GB + 30 GB + 0 GB = 47 GB. Isso se torna o tamanho da Instância Protegida em que a fatura mensal se baseia. Conforme aumenta a quantidade de dados na máquina virtual, o tamanho da Instância Protegida usada para cobrança também será alterado de forma adequada.

A cobrança não é iniciada até que o primeiro backup bem-sucedido seja concluído. Neste ponto, a cobrança de Instâncias Protegidas e de Armazenamento se inicia. A cobrança continuará desde que haja *dados de backup armazenados com o Backup do Microsoft Azure* para a máquina virtual. Executar a operação Parar Proteção não interrompe a cobrança se os dados de backup forem mantidos.

A cobrança para uma máquina virtual especificada será descontinuada somente se a proteção for interrompida *e* os dados de backup forem excluídos. Quando não há nenhum trabalho de backup ativo (quando a proteção tiver sido interrompida), o tamanho da máquina virtual no momento do último backup bem-sucedido torna-se o tamanho da instância protegida em que a fatura mensal se baseia.

## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas

- [Backup de máquinas virtuais](backup-azure-vms.md)
- [Gerenciar o backup de máquinas virtuais](backup-azure-manage-vms.md)
- [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
- [Solucionar problemas de backup da VM](backup-azure-vms-troubleshoot.md)

<!---HONumber=AcomDC_0302_2016-->