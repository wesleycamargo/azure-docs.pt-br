<properties
	pageTitle="Criar e exportar um BACPAC de um banco de dados SQL do Azure"
	description="Criar e exportar um BACPAC de um banco de dados SQL do Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/23/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Criar e exportar um BACPAC de um banco de dados SQL do Azure

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artigo fornece instruções para exportar um BACPAC do seu banco de dados SQL do Azure com o [portal de visualização do Azure](https://portal.azure.com).

Um [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) é um arquivo .bacpac que contém um esquema de banco de dados e dados. O caso de uso primário para um BACPAC é mover um banco de dados de um servidor para outro, [migrar um banco de dados local para a nuvem](sql-database-cloud-migrate.md) e arquivar um banco de dados existente em um formato aberto.

> [AZURE.NOTE]BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).


O BACPAC é exportado para um contêiner de blob de armazenamento do Azure que você poderá baixar depois que a operação for concluída com êxito.

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL Azure. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- Uma [conta de Armazenamento do Azure](storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC. Atualmente, a conta de armazenamento deve usar o modelo de implantação clássica, sendo assim, escolha **Clássico** ao criar uma conta de armazenamento. 


## Exportar o banco de dados

Abra a folha do Banco de Dados SQL do banco de dados que você deseja exportar.

> [AZURE.IMPORTANT]Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy.md) e, em seguida, exportar essa cópia.

1.	Vá para o [Portal de Visualização do Azure](https://portal.azure.com).
2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Banco de Dados SQL**.
2.	Clique no banco de dados que deseja exportar como um BACPAC.
3.	Na folha do Banco de Dados SQL, clique em **Exportar** para abrir a folha **Exportar banco de dados**:

    ![botão exportar][1]

1.  Clique em **Armazenamento** e selecione a conta de armazenamento o contêiner de blob no qual o BACPAC será armazenado:

    ![exportar banco de dados][2]

1.  Insira o **Logon de administrador de servidor** e a **Senha** do servidor SQL do Azure que contém o banco de dados que você está exportando.
1.  Clique em **Criar** para exportar o banco de dados.

Clicar em **Criar** cria uma solicitação de exportação de banco de dados e a envia ao serviço. Dependendo do tamanho do banco de dados, a operação de exportação pode demorar a ser concluída.

## Monitorar o progresso da operação de exportação

2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Servidores SQL**.
2.	Clique no servidor que contém o banco de dados original (fonte) que você acabou de exportar.
3.	Na folha SQL Server, clique em **Histórico de importação/exportação**:

    ![histórico de importação exportação][3] ![histórico de importação exportação][4]

## Verificar se o BACPAC está no seu contêiner de armazenamento

2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Contas de armazenamento (clássico)**.
2.	Clique na conta de armazenamento em que você armazenou o BACPAC.
3.	Clique em **Contêineres** e selecione o contêiner para o qual você exportou o banco de dados a fim de obter detalhes (aqui é possível baixar e salvar o BACPAC).

    ![detalhes do arquivo .bacpac][5]


## Próximas etapas

- [Importar um banco de dados SQL do Azure](sql-database-import.md)



## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Nov15_HO1-->