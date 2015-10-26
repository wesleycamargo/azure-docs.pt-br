<properties
	pageTitle="Backup do Azure para cargas de trabalho do SQL usando o DPM | Microsoft Azure"
	description="Uma introdução ao backup de servidores de banco de dados SQL usando o serviço de Backup do Azure"
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/09/2015" ms.author="giridham"; "jimpark"/>


# Backup do Azure para cargas de trabalho do SQL usando o DPM

Este artigo o guiará na configuração de backups do SQL usando o Backup do Azure. Para fazer o backup do SQL Azure, é necessário ter uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

O gerenciamento de backup do banco de dados SQL e a recuperação para o Azure envolvem três etapas:

1. Criar política de backup para proteger bancos de dados do SQL Server no Azure.
2. Criar cópias de backup sob demanda no Azure.
3. Recuperar o banco de dados do Azure.

## Antes de começar
Antes de prosseguir, verifique se todos os [pré-requisitos](../backup-azure-dpm-introduction/#prerequisites) para usar o Backup do Microsoft Azure para proteger as cargas de trabalho foram atendidos. Os pré-requisitos abrangem tarefas como: criar um cofre de backup, baixar as credenciais do cofre, instalar o Agente de Backup do Azure e registrar o servidor no cofre.

## Criar uma política de backup para proteger bancos de dados do SQL Azure

1. No servidor DPM, configure uma nova política de backup para bancos de dados SQL criando um novo **Grupo de proteção**. Clique no espaço de trabalho **Proteção**.

2. Clique em **Novo** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)

3. O DPM mostra a tela inicial com a orientação sobre como criar um **Grupo de proteção**. Clique em **Próximo**.

4. Selecionar **Servidores**.

    ![Selecionar o tipo de Grupo de Proteção - ‘Servidores’](./media/backup-azure-backup-sql/pg-servers.png)

5. Expanda o SQL Server onde os bancos de dados SQL para fazer backup estão instalados. O DPM mostra várias fontes de dados cujo backup pode vir desse servidor. Expanda **Todos os Compartilhamentos de SQL** e selecione os bancos de dados SQL (neste caso, selecionamos ReportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB) para fazer backup. Clique em **Próximo**.

    Você encontrará uma tela semelhante à apresentada a seguir.

    ![Selecione o banco de dados SQL](./media/backup-azure-backup-sql/pg-databases.png)

6. Forneça um nome para o grupo de proteção que você pretende criar. Verifique se você selecionou a opção "**Desejo ter proteção online**".

    ![Método de proteção de dados: disco de curto prazo e Azure Online](./media/backup-azure-backup-sql/pg-name.png)

7. Na tela **Especificar objetivos de curto prazo**, inclua as entradas necessárias para criar pontos de backup em disco.

    Aqui podemos ver que a **faixa de retenção** está definida como *5 dias* e a **frequência de sincronização** está definida como uma vez cada *15 minutos*, que é a frequência na qual o backup é feito. O **Backup completo expresso** é definido como *20h*.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

    >[AZURE.NOTE]Às 20:00 (de acordo com a entrada da tela), um ponto de backup é criado diariamente transferindo os dados que foram modificados do ponto de backup das 20:00 do dia anterior. Esse processo é chamado **Backup completo expresso**. Enquanto os logs transacionais do SQL são sincronizados a cada 15 minutos, se houver a necessidade de recuperar o banco de dados às 9:00, o ponto será criado reproduzindo-se novamente os logs do último ponto de backup integral expresso (20:00, neste caso).

8. Clique em **Próximo**.

    O DPM mostra o espaço de armazenamento total disponível e a utilização do espaço de disco potencial.

    ![Alocação de disco](./media/backup-azure-backup-sql/pg-storage.png)

    >[AZURE.NOTE]O DPM cria um volume por fonte de dados (banco de dados SQL) para criar a cópia de backup inicial. Com essa abordagem, o LDM (gerenciador de discos lógicos) limita o DPM para proteger somente até 300 fontes de dados (bancos de dados SQL). Para evitar isso, o DPM implementou outra abordagem que usa um único volume de várias fontes de dados. Isso é habilitado usando o **colocar dados no Pool de armazenamento do DPM**. Com essa abordagem, o DPM pode proteger até 2 mil bancos de dados SQL.

    O DPM pode considerar o aumento do volume de backup conforme os dados de produção aumenta, desde que a opção **Aumentar os volumes automaticamente** seja selecionada. Se você desmarcar **Aumentar os volumes automaticamente**, isso limitará o armazenamento de backup usado para fazer backup de fontes de dados no grupo de proteção.

9. Os administradores recebem a opção de transferir este backup inicial manualmente (fora da rede) para evitar o congestionamento de largura de banda ou pela rede. Eles também podem configurar a hora em que a transferência inicial pode acontecer. Clique em **Próximo**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    >[AZURE.NOTE]A cópia de backup inicial requer a transferência de toda a fonte de dados (banco de dados SQL) do servidor de produção (SQL Server) para o servidor DPM. Esses dados às vezes podem ser muito grandes e a transferência disso pela rede pode exceder a largura de banda. Portanto, administradores podem escolher entre transferir esse backup inicial **manualmente** para evitar a sobrecarga de largura de banda ou deixar ele acontecer **automaticamente** pela rede. Além disso, quando os Administradores escolhem **Rede**, podem optar por criar a cópia de backup inicial **Agora** ou **Depois** em um horário especificado.

    Quando o backup inicial for concluído, os demais backups são backups incrementais além da cópia de backup inicial que, em geral, são muito pequenas e transferidas na rede.

10. Escolha quando deseja que a verificação de consistência seja executada e clique em **Próximo**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    >[AZURE.NOTE]O DPM pode realizar uma verificação de consistência para confirmar a integridade do ponto de backup. Ele calcula a soma de verificação do arquivo de backup no servidor de produção (SQL Server, neste caso) e os dados para esse arquivo no DPM. Em caso de conflito, supõe-se que o arquivo de backup no DPM está corrompido. O DPM corrige os dados dos quais foram feitos backup enviando os blocos correspondentes à soma de verificação sem correspondência. Como a verificação de consistência é uma operação com desempenho intenso, os administradores têm a opção de agendá-la ou executá-la automaticamente.

11. Para especificar a proteção online das fontes de dados, selecione os bancos de dados a serem protegidos no Azure e clique em **Próximo**.

    ![Selecionar fontes de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)

12. Os administradores podem escolher agendamentos de backup e políticas de retenção que atendam às políticas da organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos uma vez por dia às 12h e às 20h (parte inferior da tela)

    >[AZURE.NOTE]É uma prática recomendada ter alguns pontos de recuperação de curto prazo em disco para recuperação rápida. Isso é chamado de "recuperação operacional". O Azure serve como um bom local fora do site com SLAs e garantia de disponibilidade superiores. Até a metade de 2015, o DPM e o Azure Backup terão a capacidade adicional de especificar várias "gerações" (também conhecidas como esquema GFS) de backups.

    **Prática recomendada**: verifique se os Backups do Azure estão agendados após a conclusão de backups em disco local usando o DPM. Isso permite que o último backup de disco seja copiado para o Azure.

13. Escolha o agendamento de política de retenção. Os detalhes sobre como funciona a política de retenção são fornecidos em [Usar o Backup do Azure para substituir a infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    - Os backups são feitos uma vez por dia às 12h e às 20h (parte inferior da tela) e são mantidos por 180 dias.
    - O backup no sábado às 12h é retido por 104 semanas
    - O backup no último sábado às 12h é retido por 60 meses
    - O backup no último sábado de março às 12h é mantido por 10 anos

14. Clique em **Próximo** e selecione a opção apropriada para transferir a cópia do backup inicial para o Azure. Você pode escolher **Automaticamente pela rede** ou **Backup Offline**.

    - **Automaticamente pela rede** transfere os dados de backup para o Azure de acordo com o agendamento escolhido para backup.
    - Como o **Backup Offline** funciona é explicado no [Fluxo de trabalho de backup offline no Backup do Azure](backup-azure-backup-import-export.md).

    Escolha o mecanismo de transferência relevante para enviar a cópia de backup inicial para o Azure e clique em **Próximo**.

15. Depois que você revisar os detalhes da política na tela **Resumo**, clique no botão **Criar grupo** para concluir o fluxo de trabalho. Você pode clicar no botão **Fechar** e monitorar o progresso do trabalho no espaço de trabalho Monitoramento.

    ![Criação de grupo de proteção em andamento](./media/backup-azure-backup-sql/pg-summary.png)

## Backup sob demanda de um banco de dados SQL
Embora as etapas anteriores tenham criado uma política de backup, um "ponto de recuperação" é criado somente quando ocorre o primeiro backup. Em vez de esperar o Agendador ser ativado, as etapas a seguir irão disparar a criação de um ponto de recuperação manualmente.

1. Aguarde até que o status do grupo de proteção mostre **OK** para o banco de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)

2. Clique com o botão direito do mouse no banco de dados e selecione **Criar Ponto de Recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)

3. Escolha **Proteção Online** no menu e clique em **OK**. Isso iniciará a criação de um ponto de recuperação no Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)

4. Você pode exibir o andamento do trabalho no espaço de trabalho **Monitoramento**, onde encontrará um trabalho em andamento como aquele mostrado na figura a seguir.

    ![Console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## Recuperar um banco de dados SQL do Azure
As seguintes etapas são necessárias para recuperar uma entidade protegida (banco de dados SQL) do Azure.

1. Abra o Console de gerenciamento do servidor DPM. Navegue até o espaço de trabalho **Recuperação**, onde você poderá ver os servidores de backup pelo DPM. Procure o banco de dados necessário (nesse caso, ReportServer$MSDPM2012). Selecione uma hora para **Recuperar de** que termine com **Online**.

    ![Selecione um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)

2. Clique com o botão direito do mouse no nome do banco de dados e clique em **Recuperar**.

    ![Recuperar do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)

3. O DPM mostra os detalhes do ponto de recuperação. Clique em **Próximo**. Selecione o tipo de recuperação **Recuperar na instância original do SQL Server**. Isso substituirá o banco de dados. Clique em **Próximo**.

    ![Recuperar no local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite a recuperação do banco de dados para outra instância do SQL Server ou em uma pasta de rede autônoma.

4. Na tela **Especificar opções de recuperação**, você pode selecionar opções de recuperação, como a otimização do uso da largura de banda de rede para reduzir a largura de banda usada pela recuperação. Clique em **Próximo**.

5. Na tela **Resumo**, você verá todas as configurações de recuperação fornecidas até agora. Clique em **Recuperar**.

    O status de recuperação mostra que o banco de dados está sendo recuperado. Você pode clicar em **Fechar** para fechar o assistente e exibir o progresso no espaço de trabalho **Monitoramento**.

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Após a conclusão da recuperação, a cópia do banco de dados restaurada será consistente com o aplicativo.

### Próximas etapas:

• [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->