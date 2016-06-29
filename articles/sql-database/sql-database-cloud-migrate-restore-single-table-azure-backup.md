<properties
	pageTitle="Restaurar uma única tabela do backup do Banco de Dados SQL do Azure | Microsoft Azure"
	description="Saiba como restaurar uma única tabela do backup do Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="daleche"/>


# Como restaurar uma única tabela de um backup do Banco de Dados SQL do Azure

É possível se deparar com uma situação na qual você acidentalmente modificou alguns dados no banco de dados SQL e agora deseja recuperar a única tabela afetada. Este artigo descreve como restaurar uma única tabela em um banco de dados de um dos [backups que são realizados automaticamente](sql-database-automated-backups.md) pelo Banco de Dados SQL do Azure, com base no nível de desempenho selecionado.

## Etapas de preparação: renomear a tabela e restaurar uma cópia do banco de dados
1. Identifique a tabela no banco de dados SQL do Azure que deseja substituir pela cópia restaurada. Use o Microsoft SQL Management Studio para renomear a tabela. Por exemplo, renomeie a tabela como &lt;table name&gt;\_old.

	**Observação** Para evitar o bloqueio, certifique-se de que não há nenhuma atividade em execução na tabela que está sendo renomeada. Se tiver problemas, lembre-se de executar esse procedimento durante uma janela de manutenção.

2. Restaure um backup do banco de dados para um estado no qual deseja recuperar. Para fazer isso, veja as etapas em [Recuperar um Banco de Dados SQL do Azure de um erro de usuário](sql-database-user-error-recovery.md).

	**Observações**:
	- O nome do banco de dados restaurado estará no formato DBName+TimeStamp; por exemplo, **Adventureworks2012\_2016-01-01T22-12Z**. Essa etapa não substituirá o nome do banco de dados existente no servidor. Esta é uma medida de segurança, e destina-se a permitir que o usuário verifique o banco de dados restaurado antes de remover seu banco de dados atual e renomear o banco de dados restaurado para uso em produção.
	- Todas as camadas de desempenho de Básico a Premium são copiadas em backup automaticamente pelo serviço, com diferentes métricas de retenção de backup, dependendo da camada:

| Restauração de banco de dados | Camada básica | Camadas Standard | Camadas Premium |
| :-- | :-- | :-- | :-- |
| Restauração Pontual | Qualquer ponto de restauração dentro de 7 dias|Qualquer ponto de restauração dentro de 14 dias| Qualquer ponto de restauração dentro de 35 dias|

## Copiando a tabela do banco de dados restaurado usando a Ferramenta de migração do Banco de Dados SQL
1. Baixe e instale o [SQL Database Migration Wizard](https://sqlazuremw.codeplex.com).

2. Abra o SQL Database Migration Wizard e, na página **Selecionar Processo**, selecione **Banco de Dados em Analisar/Migrar** e clique em **Avançar**.![SQL Database Migration Wizard – Selecionar processo](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Na caixa de diálogo **Conectar ao Servidor**, aplique as seguintes configurações:
 - **Nome do servidor**: a instância do SQL Azure
 - **Autenticação**: **autenticação do SQL Server**. Insira suas credenciais de logon.
 - **Banco de dados**: **banco de dados mestre (listar todos os bancos de dados)**.
 - **Observação** Por padrão, o assistente salva as informações de logon. Se não desejar que essas informações sejam salvas, selecione **Esquecer Informações de Logon**.![SQL Database Migration Wizard – Selecionar origem – etapa 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Na caixa de diálogo **Selecionar Origem**, selecione o nome do banco de dados restaurado na seção **Etapas de preparação** como a origem e clique em **Avançar**.

	![SQL Database Migration Wizard – Selecionar origem – etapa 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Na caixa de diálogo **Escolher Objetos**, selecione a opção **Selecionar objetos de bancos de dados específicos** e a tabela (ou as tabelas) que deseja migrar para o servidor de destino.![SQL Database Migration Wizard – Escolher objetos](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Na página **Resumo do Assistente para criação de script**, clique em **Sim** quando um diálogo for aberto perguntado se você está pronto para gerar um script SQL. Você também tem a opção de salvar o Script TSQL para uso posterior.![SQL Database Migration Wizard – Resumo do Assistente para criação de script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Na página **Resumo dos Resultados**, clique em **Avançar**.![SQL Database Migration Wizard – Resumo dos resultados](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Na página **Configurar Conexão do Servidor de Destino**, clique em **Conectar ao Servidor** e insira os detalhes da seguinte maneira:
	- **Nome do Servidor**: instância do servidor de destino
	- **Autenticação**: **autenticação do SQL Server**. Insira suas credenciais de logon.
	- **Banco de dados**: **banco de dados mestre (listar todos os bancos de dados)**. Esta opção lista todos os bancos de dados no servidor de destino.

	![SQL Database Migration Wizard – Configurar conexão do servidor de destino](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Clique em **Conectar**, selecione o banco de dados de destino para o qual deseja mover a tabela e clique em **Avançar**. Isso deverá terminar a execução do script gerado anteriormente e você deverá ver a tabela recém-movida copiada no banco de dados de destino.

## Etapa de verificação
1. Consulte e teste a tabela recém-copiada para certificar-se de que os dados estão intactos. Após a confirmação, é possível remover a tabela renomeada na seção **Etapas de preparação** (por exemplo, &lt;table name&gt;\_old).

## Próximas etapas

[Backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md)

<!---HONumber=AcomDC_0615_2016-->