<properties
	pageTitle="Proteção do DPM do farm do SharePoint no Azure | Microsoft Azure"
	description="Este artigo fornece uma visão geral da Proteção do DPM do farm do SharePoint no Azure"
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="giridham;jimpark"/>


# Fazendo backup de um farm do SharePoint no Azure
Faça backup de um farm do SharePoint no Azure usando o DPM (System Center Data Protection Manager) da mesma maneira que você fazer backup de outras fontes de dados. O Azure fornece flexibilidade no agendamento de backup para criar pontos de backup diariamente, semanalmente, mensalmente ou anualmente e fornece opções de política de retenção variada para diversos pontos de backup. O DPM fornece a capacidade de armazenar cópias de disco locais para os RTOs (objetivos de tempo de recuperação) mais rápidos e no Azure para um destino de retenção econômica de longo prazo.

## Versões do SharePoint com suporte e cenários de proteção relacionados
O Backup do Azure para DPM dá suporte aos cenários a seguir.

| Carga de trabalho | Versão | Implantação do SharePoint | Tipo de implantação do DPM | DPM - System Center 2012 R2 | Proteção e recuperação |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | SharePoint implantado como um servidor físico ou em uma máquina virtual Hyper-V/VmWare <br> -------------- <br> AlwaysOn do SQL | Servidor físico ou máquina virtual Hyper-V local | Dá suporte ao backup no Azure desde o Pacote Cumulativo de Atualizações 5 | Protege a recuperação do farm do SharePoint: farm, banco de dados, arquivo ou item de lista do disco e banco de dados e farm de recuperação do Azure |

## Antes de começar
Há alguns elementos que você precisa garantir antes de fazer o backup de um farm do SharePoint no Azure.

### Pré-requisitos
Antes de prosseguir, verifique se todos os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites) para usar o Backup do Microsoft Azure para proteger as cargas de trabalho foram atendidos. Os pré-requisitos abrangem tarefas como: criar um cofre de backup, baixar as credenciais do cofre, instalar o Agente de Backup do Azure e registrar o servidor no cofre.

### Agente do DPM
O agente do DPM deve ser instalado no servidor do SharePoint, servidores SQL e outros servidores que fazem parte do farm do SharePoint. Para obter mais informações sobre como configurar o agente de proteção, veja [Configurar o agente de proteção](https://technet.microsoft.com/library/hh758039.aspx). A única exceção é que você instala o agente apenas em um único servidor WFE (Front-end da Web). O DPM precisa do agente apenas em um servidor WFE para atuar como o ponto de entrada para proteção.

### Farm do SharePoint
Para cada 10 milhões de itens no farm, deve haver pelo menos 2 GB de espaço no volume em que a pasta do DPM está localizada. Esse espaço é necessário para a geração de catálogo. Para o DPM recuperar itens específicos (coleções de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração de catálogo cria uma lista das URLs (Uniform Resource Locators) contidas em cada banco de dados de conteúdo. Você pode exibir a lista de URLs no painel de item recuperável na área de tarefa Recuperação do Console do Administrador do DPM.

### SQL Server
O DPM é executado como sistema local e para fazer o backup de bancos de dados do SQL Server, precisa privilégios sysadmin nessa conta do SQL Server. Defina NT AUTHORITY\\SYSTEM como *sysadmin* no SQL Server do qual deseja fazer o backup.

No farm do SharePoint, se você tiver bancos de dados do SQL Server configurados com aliases do SQL Server, instale os componentes de cliente do SQL Server no servidor Web front-end que o DPM protegerá.

### SharePoint Server
Embora o desempenho dependa de muitos fatores, como o tamanho do farm do SharePoint, como orientação geral, um servidor DPM pode ser usado para proteger um farm do SharePoint de 25 TB.

### Pacote Cumulativo de Atualizações 5 do DPM
Para iniciar a proteção de um farm do SharePoint no Azure, você precisa instalar o Pacote Cumulativo de Atualizações 5 ou superior do DPM. O Pacote Cumulativo de Atualizações 5 fornece a capacidade de proteger um farm do SharePoint (configurado usando o AlwaysOn do SQL) no Azure. Para obter mais informações, veja [instalação do Pacote Cumulativo de Atualizações 5 do DPM](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### O que não tem suporte
1. Proteger um farm do SharePoint não protege os índices de pesquisa ou bancos de dados de serviço de aplicativo. Você precisará configurar a proteção desses bancos de dados separadamente.
2. O DPM não fornece o backup dos bancos de dados SQL do SharePoint hospedados em compartilhamento do SOFS (Servidor de Arquivos de Escalabilidade Horizontal).

## Configurar a proteção do SharePoint
Você deve configurar o serviço de Gravador VSS do SharePoint (serviço de Gravador WSS) usando o **ConfigureSharePoint.exe** antes de poder proteger o SharePoint usando o DPM.

Você pode encontrar o **ConfigureSharePoint.exe** na pasta [Caminho de instalação do DPM]\\bin no servidor Web de front-end. Essa ferramenta fornece o agente de proteção com as credenciais do farm do SharePoint. Você a executa em um único servidor WFE. Se você tiver vários servidores WFE, selecione apenas um ao configurar um grupo de proteção.

### Para configurar o serviço de gravador VSS do SharePoint
1. No servidor WFE, em um prompt de comando, vá para [Caminho de instalação do DPM]\\bin\\
2. Execute ConfigureSharePoint -EnableSharePointProtection
3. Insira as credenciais de administrador do farm. Essa conta deve ser um membro do grupo de administradores local no servidor WFE. Se o administrador do farm não for um administrador local, conceda as seguintes permissões no servidor WFE:
  - Conceda o controle total do grupo WSS\_Admin\_WPG para a pasta do DPM (%Arquivos de Programas%\\Microsoft Data Protection Manager\\DPM).
  - Conceda acesso de leitura do grupo WSS\_Admin\_WPG à chave do Registro do DPM (HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager).

>[AZURE.NOTE]Você precisará executar novamente o ConfigureSharePoint.exe sempre que houver uma alteração nas credenciais de administrador de farm do SharePoint.

## Fazer backup de um farm do SharePoint usando o DPM
Depois de configurar o DPM e o farm do SharePoint conforme explicado anteriormente, o SharePoint pode ser protegido pelo DPM.

### Para proteger um farm do SharePoint
1. Na guia **Proteção** do Console do Administrador do DPM, clique em **Novo**. ![Novo Guia Proteção](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. Na tela **Selecionar Tipo do Grupo de Proteção** do assistente **Criar Novo Grupo de Proteção**, selecione **Servidores** e clique em **Avançar**.

    ![Selecionar o tipo de Grupo de Proteção](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. Na tela **Selecionar Membros do Grupo**, marque a caixa de seleção do servidor do SharePoint que deseja proteger e clique em **Avançar**

    ![Selecionar membros do grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE]Com o agente do DPM instalado, você pode ver o servidor no assistente. O DPM também mostra sua estrutura. Como o ConfigureSharePoint.exe foi executado, o DPM se comunica com o gravador VSS do SharePoint e seus bancos de dados SQL correspondentes e reconhece a estrutura de farm do SharePoint (os bancos de dados de conteúdo associados e todos os itens correspondentes).

4. Na tela **Selecionar Método de Proteção de Dados**, insira o nome do *Grupo de Proteção* e selecione seus *métodos de proteção* preferenciais. Clique em **Próximo**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE]Escolher a proteção de disco ajuda a atender aos objetivos de tempo de recuperação com tempos de recuperação curtos. O Azure é um destino de proteção econômica de longo prazo comparado com as fitas. Para obter mais informações, confira [este artigo](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)

5. Na tela **Especificar Objetivos de Curto Prazo**, selecione seu *período de retenção* preferencial e identifique quando deseja que os backups ocorram.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE]Uma vez que a recuperação é necessária mais frequentemente para dados com menos de cinco dias, selecionamos um período de retenção de 5 dias no disco e garantimos que o backup ocorre fora dos horários de produção para esse exemplo.

6. Examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção e clique em **Avançar**.

7. Para cada grupo de proteção, o DPM aloca espaço em disco para armazenar e gerenciar réplicas. Neste ponto, o DPM deve criar uma cópia dos dados selecionados. Selecione como e quando você deseja que a réplica seja criada e clique em **Avançar**.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE]Para garantir que o tráfego de rede não seja afetado, selecione um horário fora do horário de produção.

8. O DPM assegura a integridade de dados executando verificações de consistência na réplica. Há duas opções disponíveis. Você pode definir um agendamento para executar verificações de consistência ou permitir que o DPM execute as verificações de consistência automaticamente na réplica sempre que ela ficar inconsistente. Selecione sua opção preferencial e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. Na tela **Especificar Dados da Proteção Online**, selecione o farm do SharePoint que deseja proteger e clique em **Avançar**.

    ![Proteção do SharePoint do DPM1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. Na tela **Especificar o Agendamento do Backup Online**, selecione seu agendamento preferencial e clique em **Avançar**.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE]O DPM permite 2 backups diários em momentos diferentes no Azure.

11. Dependendo do agendamento de backup selecionado, na tela **Especificar Política de Retenção Online**, selecione a política de retenção para pontos de backup diários, semanais, mensais e anuais.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE]O DPM usa um esquema de retenção de avô-pai-filho em que uma política de retenção diferente pode ser escolhida para diferentes pontos de backup.

12. Semelhante ao disco, uma réplica do ponto de referência inicial precisa ser criado no Azure. Selecione sua opção preferencial para criar a cópia de backup inicial no Azure e clique em **Avançar**.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. Examine as configurações selecionadas na página **Resumo** e clique em **Criar Grupo**. Após a criação do grupo de proteção, você verá uma mensagem de êxito.

    ![Resumo](./media/backup-azure-backup-sharepoint/summary.png)

## Restaurar um item do SharePoint do disco usando o DPM
No exemplo abaixo, o *item Recuperando SharePoint* foi excluído acidentalmente e precisa ser recuperado. ![Proteção do SharePoint do DPM4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra o **Console do Administrador do DPM**. Todos os farms do SharePoint protegidos pelo DPM são mostrados na guia Proteção.

    ![Proteção do SharePoint do DPM3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. Para começar a recuperar o item, selecione a guia **Recuperação**.

    ![Proteção do SharePoint do DPM5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. É possível pesquisar o SharePoint para localizar o *item Recuperando SharePoint* usando uma pesquisa baseada em curinga em um intervalo de ponto de recuperação.

    ![Proteção do SharePoint do DPM6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. Selecione o ponto de recuperação apropriado nos resultados da pesquisa, clique com botão direito do mouse e selecione **Recuperar**

5. Você também pode navegar pelos diversos pontos de recuperação e selecionar um banco de dados ou item para recuperar. Selecione **data > Hora da recuperação** e selecione o **banco de dados > farm do SharePoint > ponto de recuperação > item** correto.

    ![Proteção do SharePoint do DPM7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. Clique com botão direito do mouse no item e selecione **Recuperar** para abrir o **Assistente de Recuperação**. Clique em **Próximo**.

    ![Rever Seleção de Recuperação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. Selecione o tipo de recuperação que deseja executar e clique em **Avançar**.

    ![Tipo de Recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE]O exemplo acima mostra o item de recuperação para o site do SharePoint original.

8. Selecione o **Processo de Recuperação** que deseja usar.
    - Selecione **Recuperar sem usar um farm de recuperação** se o farm do SharePoint não tiver sido alterado e for o mesmo que o ponto de recuperação que está sendo restaurado.
    - Selecione **Recuperar usando um farm de recuperação** se o farm do SharePoint tiver sido alterado desde que o ponto de recuperação foi criado.

    ![Processo de Recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. Forneça um local de preparo da instância SQL para recuperar o banco de dados temporariamente e fornecer um compartilhamento de arquivo de preparo no servidor DPM e no servidor do SharePoint para recuperar o item.

    ![Local de Preparo1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    O DPM anexa o banco de dados de conteúdo, que está hospedando o item do SharePoint, à instância SQL temporária. Do banco de dados de conteúdo, o servidor DPM recupera o item e o coloca no local do arquivo de preparo no servidor DPM. O item recuperado no local de preparo, no servidor DPM, agora precisa ser exportado para o local de preparo no farm do SharePoint.

    ![Local de Preparo2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. Selecione **Especificar opções de recuperação** e aplique as configurações de segurança ao farm do SharePoint ou aplique as configurações de segurança do ponto de recuperação. Clique em **Próximo**.

    ![Opções de Recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE]Você pode optar por limitar o uso de largura de banda da rede. Isso minimiza o impacto no servidor de produção durante o horário de produção.

11. Examine as informações de resumo e clique em **Recuperar** para iniciar a recuperação do arquivo.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. Agora selecione a guia **Monitoramento** no **Console do Administrador do DPM** para exibir o *Status* da recuperação.

    ![Status da Recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE]Agora o arquivo está restaurado. Você pode atualizar o site do SharePoint para verificar o arquivo restaurado.

## Restaurar um banco de dados do SharePoint do Azure usando o DPM

1. Para recuperar um banco de dados de conteúdo do SharePoint, navegue pelos vários pontos de recuperação (conforme mostrado acima) e selecione o ponto de recuperação deseja recuperar.

    ![Proteção do SharePoint do DPM8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

    > [AZURE.NOTE]Como o farm do SharePoint está protegido para retenção de longo prazo no Azure, não há nenhuma informação de catálogo (metadados) disponível no servidor DPM. Como resultado, sempre que um banco de dados de conteúdo do SharePoint pontual precisa ser recuperado, você precisa recatalogar o farm do SharePoint.

3. Clique em **Recatalogar**.

    ![Proteção do SharePoint do DPM10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de status **Recatalogação de Nuvem** é exibida.

    ![Proteção do SharePoint do DPM11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação ser concluída, o status é alterado para *Êxito*. Clique em **fechar**

    ![Proteção do SharePoint do DPM12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. Clique no objeto do SharePoint mostrado na guia **Recuperação** do DPM para obter a estrutura do banco de dados de conteúdo. Clique com botão direito do mouse > **Recuperar**.

    ![Proteção do SharePoint do DPM13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. Nesse ponto, siga as [etapas de recuperação acima](#restore-a-sharepoint-item-from-disk-using-dpm) para recuperar um banco de dados de conteúdo do SharePoint do disco.

## Perguntas frequentes
P: Quais versões do DPM dão suporte ao SQL 2014 e SQL 2012 (SP2)<br> R: DPM 2012 R2 com o Pacote Cumulativo de Atualizações 4 dão suporte a eles

P: Posso recuperar um item do SharePoint no local original se o SharePoint estiver configurado usando o AlwaysOn do SQL (com proteção em disco)?<br> R: Sim, o item pode ser recuperado para o site do SharePoint original

P: Posso recuperar um banco de dados do SharePoint no local original se o SharePoint estiver configurado usando o AlwaysOn do SQL?<br> R: Como os bancos de dados do SharePoint são configurados no AlwaysOn do SQL, eles não podem ser modificados a menos que o AG (grupo de disponibilidade) seja removido. Como resultado, o DPM não pode restaurar o banco de dados no local original. Você pode recuperar o banco de dados SQL em outra instância do SQL.

## Próximas etapas
- Saiba mais sobre a Proteção do DPM do SharePoint: veja a [Série de vídeos - Proteção do DPM do SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
- Examine as [Notas de versão do System Center 2012 – Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
- Examine as [Notas de versão do Data Protection Manager no System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

<!---HONumber=AcomDC_1217_2015-->