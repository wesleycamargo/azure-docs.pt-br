<properties
	pageTitle="Backup de máquina virtual do Azure - Backup | Microsoft Azure"
	description="Saiba como fazer backup de uma máquina virtual do Azure após o registro"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="09/01/2015" ms.author="aashishr"; "jimpark"/>


# Fazer backup de máquinas virtuais do Azure
Este artigo é o guia essencial para o backup de máquinas virtuais do Azure. Antes de prosseguir, verifique se todos os [pré-requisitos](backup-azure-vms-introduction.md#prerequisites) foram atendidos.

Fazer o backup de máquinas virtuais do Azure envolve três etapas principais:

![Três etapas para fazer o backup de uma máquina virtual do Azure](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]O backup de máquina virtual é local. O cofre de backup de uma região do Azure especificada não permitirá que você faça o backup de máquinas virtuais de outra região do Azure. Assim, pelo menos 1 cofre de backup deve ser criado para cada região do Azure com VMs que precisam de backup.

## 1\. Descobrir máquinas virtuais do Azure
O processo de descoberta consulta o Azure quanto à lista de máquinas virtuais na assinatura, juntamente com informações adicionais, como o nome do Serviço de Nuvem e a Região. O processo de descoberta sempre deve ser executado como a primeira etapa. Isso é para garantir que quaisquer novas máquinas virtuais adicionadas à assinatura sejam identificadas.

### Para disparar o processo de descoberta

1. Navegue até o cofre de backup, que pode ser encontrado em **Serviços de Recuperação** no portal do Azure e clique na guia **Itens Registrados**.

2. Escolha o tipo de carga de trabalho no menu suspenso como **Máquina Virtual do Azure** e clique no botão **Selecionar**.

    ![selecionar carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)

3. Clique no botão **DESCOBRIR** na parte inferior da página. ![botão descobrir](./media/backup-azure-vms/discover-button-only.png)

4. O processo de descoberta pode ser executado por alguns minutos, enquanto as máquinas virtuais estão sendo tabuladas. Será exibida uma notificação do sistema na parte inferior da tela enquanto o processo de descoberta estiver em execução.

    ![descobrir vms](./media/backup-azure-vms/discovering-vms.png)

5. Quando o processo de detecção for concluído, uma notificação do sistema será exibida.

    ![discover-done](./media/backup-azure-vms/discovery-complete.png)

##  2\. Registre as máquinas virtuais do Azure
Antes que uma máquina virtual possa ser protegida, ela precisa ser registrada com o serviço de Backup do Azure. O objetivo principal do processo de registro é associar a máquina virtual ao de serviço de Backup do Azure. Normalmente, o registro é uma atividade realizada uma única vez.

>[AZURE.NOTE]A extensão de backup não é instalada durante a etapa de Registro. A instalação e atualização do agente de backup agora fazem parte do trabalho de backup agendado.

### Para registrar as máquinas virtuais

1. Navegue até o cofre de backup, que pode ser encontrado em **Serviços de Recuperação** no portal do Azure e clique na guia **Itens Registrados**

2. Escolha o tipo de carga de trabalho no menu suspenso como **Máquina Virtual do Azure** e clique no botão Selecionar.

    ![selecionar carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)

3. Clique no botão **REGISTRAR** na parte inferior da página. ![botão registrar](./media/backup-azure-vms/register-button-only.png)

4. No menu de atalho **Registrar Itens**, escolha as máquinas virtuais que você deseja registrar. Se houver duas ou mais máquinas virtuais com o mesmo nome, use o serviço de nuvem para distinguir entre elas.

    A operação **registrar** pode ser feita em grande escala, o que significa que várias máquinas virtuais podem ser selecionadas ao mesmo tempo para serem registradas. Isso reduz consideravelmente o esforço feito uma única vez na preparação da máquina virtual para backup.


5. Um trabalho é criado para cada máquina virtual que deve ser registrada. A notificação do sistema mostra o status dessa atividade. Clique em **Exibir Trabalho** para ir para a página **Trabalhos**.

    ![trabalho de registro](./media/backup-azure-vms/register-create-job.png)

6. A máquina virtual também aparece na lista de itens registrados e o status da operação de registro é mostrado.

    ![Status de registro 1](./media/backup-azure-vms/register-status01.png)

7. Quando a operação for concluída, o status no portal será alterado para refletir o estado registrado.

    ![Status de registro 2](./media/backup-azure-vms/register-status02.png)

## 3\. Proteger: Fazer backup de máquinas virtuais do Azure
Essa etapa envolve configurar uma política de backup e de retenção para a máquina virtual. Para proteger uma máquina virtual, execute as seguintes etapas:

1. Navegue até o cofre de backup, que pode ser encontrado em **Serviços de Recuperação** no portal do Azure e clique na guia **Itens Registrados**.
2. Escolha o tipo de carga de trabalho no menu suspenso como **Máquina Virtual do Azure** e clique no botão **Selecionar**.

    ![Selecionar a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)

3. Clique no botão **PROTEGER** na parte inferior da página. O assistente **Proteger Itens** é exibido.

4. O assistente **Proteger Itens** é o local onde as máquinas virtuais a serem protegidas podem ser selecionadas. Se houver duas ou mais máquinas virtuais com o mesmo nome, use o serviço de nuvem para distinguir entre elas.

    A operação **Proteger** pode ser feita em grande escala, o que significa que várias máquinas virtuais podem ser selecionadas ao mesmo tempo para serem registradas. Isso reduz consideravelmente o esforço feito para proteger a máquina virtual.

5. Na segunda tela do assistente **Proteger Itens**, escolha uma política de backup e retenção para fazer backup das máquinas virtuais selecionadas. Escolha entre um conjunto existente de políticas ou defina um novo.

    Em cada cofre de backup, você pode ter várias políticas de backup. As políticas refletem os detalhes sobre como o backup deve ser agendado e mantido. Por exemplo, uma política de backup poderia ser um backup diário às 22h, enquanto outra política de backup poderia ser um backup semanal às 6h. Múltiplas políticas de backup permitem flexibilidade ao programar backups para sua infraestrutura de máquina virtual.

    Cada política de backup pode ter várias máquinas virtuais que estão associadas à política. A máquina virtual pode estar associada a apenas uma política em qualquer determinado ponto no tempo.

6. Um trabalho é criado para cada máquina virtual para configurar a política de proteção e associar as máquinas virtuais a essa política. Clique na guia **Trabalhos** e escolha o filtro correto para exibir a lista de trabalhos do tipo **Configurar Proteção**.

    ![Configurar o trabalho de proteção](./media/backup-azure-vms/protect-configureprotection.png)


## Atividades pós-proteção

### Instalação da extensão de backup

O serviço de Backup do Azure manipula perfeitamente a atualização e aplicação de patches da extensão de backup sem a necessidade de nenhuma intervenção complicada pelo usuário. Isso libera o usuário da ï¿½sobrecarga de gerenciamento do agenteï¿½ que é geralmente associada a produtos de backup.

#### VMs offline
A extensão de backup será instalada se a VM estiver em execução. Uma VM em execução também fornece a maior chance de obter um ponto consistente com o aplicativo. O serviço de Backup do Azure continuará a realizar o backup da VM, no entanto, mesmo que a VM esteja desativada e a extensão não possa ser instalada (caso também conhecido como VM Offline). O impacto é visto na consistência - nesse caso, o ponto de recuperação será *consistente com o sistema de arquivos*.

### Backup inicial
Uma vez protegida com uma política, a máquina virtual será exibida na guia **Itens Protegidos** com o status *Protegida (pendente de backup inicial)*. Por padrão, o primeiro backup agendado é o backup inicial. Para disparar o backup inicial imediatamente após a configuração de proteção, use o botão **Fazer Backup Agora** na parte inferior da página Itens Protegidos.

O serviço de Backup do Azure cria um trabalho de backup para a operação de backup inicial. Clique na guia **Trabalhos** para exibir a lista de trabalhos. Como parte da operação de backup, o serviço de Backup do Azure emite um comando para a extensão de backup em cada máquina virtual para limpar todas as gravações e capturar um instantâneo consistente.

![Backup em andamento](./media/backup-azure-vms/protect-inprogress.png)

Uma vez que o backup inicial esteja concluído, o Status de Proteção da máquina virtual na guia **Itens Protegidos** será exibido como *Protegido*.

![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)


### Exibindo detalhes e status do backup
Depois de protegido, a contagem de máquina virtual também aumenta no resumo da página **Painel**. Além disso, a página **Painel** mostra o número de trabalhos das últimas 24 horas que obtiveram êxito, que falharam e que ainda estão em andamento. Clicar em qualquer categoria detalhará tal categoria na página **Trabalhos**.

![Status do backup na página Painel](./media/backup-azure-vms/dashboard-protectedvms.png)


## Consistência de pontos de recuperação
Ao lidar com dados de backup, os clientes se preocupam com o comportamento da VM após ela ser restaurada. Perguntas típicas feitas pelos clientes são:

- A máquina virtual será inicializada corretamente?
- Os dados estarão disponíveis no disco (ou) há alguma perda de dados?
- O aplicativo poderá ler os dados (ou) os dados estão corrompidos?
- Os dados farão sentido para o aplicativo (ou) os dados são internamente consistentes quando lidos pelo aplicativo?

A tabela a seguir explica os tipos de consistência encontrados durante o backup e a restauração da VM do Azure.

| Consistência | Baseado em VSS | Explicação e detalhes |
|-------------|-----------|---------|
| Consistência de aplicativo | Sim | Este é o lugar ideal para cargas de trabalho da Microsoft porque garante:<ol><li> que a VM *será iniciada*. <li>Não há *corrupção*. <li>Não há *perda de dados*.<li> Os dados são consistentes com o aplicativo que usa os dados, envolvendo o aplicativo no momento do backup - usando o VSS.</ol> O VSS (Serviço de Instantâneo de Volume) garante que os dados sejam gravados corretamente para o armazenamento. A maioria das cargas de trabalho Microsoft têm gravadores VSS que executam ações específicas de carga de trabalho relacionadas a consistência dos dados. Por exemplo, o Microsoft SQL Server tem um gravador VSS que garante que as gravações no arquivo de log de transações e no banco de dados sejam realizadas corretamente.<br><br> Para backup da VM do Azure, obter um ponto de recuperação consistente no aplicativo significa que a extensão de backup foi capaz de invocar o fluxo de trabalho do VSS e ser concluída *corretamente* antes que o instantâneo da VM fosse tirado. Naturalmente, isso significa que os gravadores VSS de todos os aplicativos na VM do Azure também são chamados.<br><br>Aprenda as [noções básicas do VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) e aprofunde-se nos detalhes de [como ele funciona](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Consistência do sistema de arquivos | Sim - para computadores executando Windows | Há dois cenários em que o ponto de recuperação pode ser consistente com o sistema de arquivos:<ul><li>Backup de VMs do Linux no Azure, já que o Linux não tem uma plataforma equivalente ao VSS.<li>Falha do VSS durante o backup de máquinas virtuais do Windows no Azure.</li></ul> Em ambos os casos, o melhor que você pode fazer é garantir que: <ol><li> a VM *seja iniciada*. <li>Não haja *corrupção*.<li>Não haja *perda de dados*.</ol> Os aplicativos precisam implementar seu próprio mecanismo de "correção" nos dados restaurados.|
| Consistência de falhas | Não | Essa situação é equivalente a um computador tendo uma "falha" (por meio de uma reinicialização forçada ou flexível). Isso geralmente acontece quando a máquina virtual do Azure está desligada no momento do backup. Para backup da máquina virtual do Azure, obter um ponto de recuperação consistente quanto a falhas significa que o Backup do Azure não dá nenhuma garantia de consistência dos dados no meio de armazenamento - seja da perspectiva do sistema operacional ou da perspectiva do aplicativo. Apenas os dados que já existem no disco no momento do backup são capturados e copiados em backup. <br/> <br/> Embora não haja garantia, na maioria dos casos o sistema operacional será inicializado. Normalmente, isso é seguido por um procedimento de verificação de disco, como chkdsk, para corrigir qualquer erro de corrupção. Quaisquer dados na memória ou gravações que não tenham sido totalmente liberadas para o disco serão perdidas. O aplicativo geralmente segue com seu próprio mecanismo de verificação, caso seja necessário realizar reversão de dados. Para backup de máquina virtual do Azure, obter um ponto de recuperação consistente quanto a falhas significa que o Backup do Azure não dá nenhuma garantia de consistência dos dados no armazenamento - seja da perspectiva do sistema operacional ou da perspectiva do aplicativo. Isso geralmente acontece quando a VM do Azure é desligada no momento do backup.<br><br>Por exemplo, se o log de transações tiver entradas que não estejam presentes no banco de dados, o software de banco de dados fará uma reversão até que os dados estejam consistentes. Ao lidar com dados distribuídos em vários discos virtuais (como volumes estendidos), um ponto de recuperação consistente quanto a falhas não garante a exatidão dos dados.|


## Desempenho e utilização de recursos
Como o software de backup que é implantado localmente, o backup de VMs no Azure também precisa ser planejado quanto à capacidade e à utilização de recursos. Os [limites de Armazenamento do Azure](azure-subscription-service-limits.md#storage-limits) definirão como as implantações de VM serão estruturadas para obter o máximo de desempenho com o mínimo de impacto para executar cargas de trabalho. Existem dois limites principais de Armazenamento do Azure que têm impacto sobre o desempenho do backup:

+ Egresso máximo por conta de armazenamento
+ Taxa de solicitação total por conta de armazenamento

Quando os dados de backup são copiados da conta de armazenamento do cliente, eles contam na direção das métricas de IOPS e Egresso (taxa de transferência de armazenamento) da conta de armazenamento. Ao mesmo tempo, as máquinas virtuais também estão em execução e consumindo IOPS e taxa de transferência. O objetivo é garantir que o tráfego total - backup e máquina virtual - não exceda os limites da conta de armazenamento.

O backup é ávido e tenta consumir tantos recursos quanto possível, com o objetivo de concluir o backup o mais rápido que puder. No entanto, todas as operações de E/S são limitadas pelo *Rendimento de destino por Blob único*, que tem um limite de *60 MB por segundo*. Para acelerar o processo de backup, tenta-se realizar o backup de cada disco da VM *em paralelo*. Portanto, se uma VM tem 4 discos, o Azure Backup tentará fazer o backup de todos os 4 discos em paralelo. Assim, o único fator mais importante que determina o tráfego de backup em saída de uma conta de armazenamento do cliente é o **número de discos** cujo backup está sendo feito da conta de armazenamento.

Um outro fator que afeta o desempenho é o **agendamento de backup**. Se você configurar todas as VMs para backup simultâneo, o número de discos cujo backup está sendo feito *em paralelo* aumentará - já que o Backup do Azure tenta realizar o backup do máximo possível de discos. Portanto, uma maneira de reduzir o tráfego de backup de uma conta de armazenamento é garantir que o backup das VMs diferentes seja feito em diferentes momentos do dia, sem sobreposição.

### Planejamento da capacidade
Reunir todos esses fatores significa que o uso da conta de armazenamento precisa ser planejado corretamente. Baixe a [planilha do Excel para planejamento de capacidade de backup de VMs](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) para ver o impacto de suas escolhas relativas a disco e agendamento de backup.

### Taxa de transferência de backup
Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados (backup incremental). A tabela a seguir mostra os valores de taxa de transferência média que podem ser esperados do Backup do Azure:

| Operação de backup | Melhor taxa de transferência possível |
| ---------------- | ---------- |
| Backup inicial | 160 Mbps |
| Backup incremental (DR) | 640 Mbps <br><br> Essa taxa de transferência pode cair significativamente se houver muita variação dispersa no disco cujo backup precisa ser feito. |

Usando isso, você pode estimar a quantidade de tempo que leva para fazer o backup de um disco de um determinado tamanho.

### Tempo total de backup da VM
Embora a maioria do tempo é gasto na leitura e cópia de dados, existem outras operações que contribuem para o tempo total gasto para fazer o backup de uma VM:

1. Tempo necessário para [instalar ou atualizar a extensão de backup](backup-azure-vms.md#offline-vms)
2. Tempo de espera de fila: uma vez que o serviço estiver processando backups de vários clientes, a operação de backup não será iniciada imediatamente. O tempo de espera médio de uma VM é de 15 a 30 minutos.


## Solucionar erros
Obtenha uma lista completa das soluções alternativas para os erros enfrentados durante o backup de máquinas virtuais: [Solução de problemas em backup de máquinas virtuais](backup-azure-vms-troubleshoot.md)


## Próximas etapas

Para saber mais sobre a introdução ao Backup do Azure, consulte:

- [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
- [Gerenciar máquinas virtuais](backup-azure-manage-vms.md)

<!---HONumber=September15_HO1-->