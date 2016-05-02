<properties
	pageTitle="Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure"
	description="Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artigo fornece instruções para arquivar seu Banco de Dados SQL do Azure para um arquivo BACPAC armazenado no armazenamento de blobs do Azure usando o [portal do Azure](https://portal.azure.com).

Quando você precisa criar um arquivo morto de um Banco de Dados SQL do Azure, pode exportar o esquema de banco de dados e os dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de BACPAC. Um arquivo BACPAC posteriormente pode ser armazenado no armazenamento de blobs do Azure ou no armazenamento local em um local e posteriormente importado novamente no Banco de Dados SQL do Azure ou em uma instalação local do SQL Server.

***Considerações***

- Para um arquivo morto ser transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou exportação de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu Banco de Dados SQL do Azure.
- O tamanho máximo de um arquivo BACPAC arquivado no armazenamento de blobs do Azure é de 200 GB. Use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) para arquivar um arquivo BACPAC maior no armazenamento local. Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.
- Não há suporte para o arquivamento no armazenamento premium do Azure usando um arquivo BACPAC.
- Se a operação de exportação ficar acima de 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumentar temporariamente o nível de serviço 
 - Interromper toda a atividade de leitura e gravação durante a exportação
 - Usar um índice clusterizado em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque os serviços de exportação precisam concluir a verificação de tabela para tentar exportar a tabela inteira

> [AZURE.NOTE] BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure.
- Um banco de dados SQL Azure. 
- Uma [conta de Armazenamento Standard do Azure](../storage/storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC no armazenamento standard.

## Exportar o banco de dados

Abra a folha do Banco de Dados SQL do banco de dados que você deseja exportar.

> [AZURE.IMPORTANT] Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy.md) e, em seguida, exportar essa cópia.

1.	Vá para o [Portal do Azure](https://portal.azure.com).
2.	Clique em **Bancos de dados SQL**.
3.	Clique no banco de dados para arquivar.
4.	Na folha Banco de Dados SQL, clique em **Exportar** para abrir a folha **Exportar banco de dados**:

    ![botão exportar][1]

5.  Clique em **Armazenamento** e selecione a conta de armazenamento o contêiner de blob no qual o BACPAC será armazenado:

    ![exportar banco de dados][2]

6. Selecione seu tipo de autenticação.
7.  Insira as credenciais de autenticação apropriadas para o SQL Server do Azure contendo o banco de dados sendo exportado.
8.  Clique em **OK** para arquivar o banco de dados. Clicar em **OK** cria uma solicitação de exportação de banco de dados e a envia ao serviço. Quanto tempo que a exportação levará depende do tamanho e da complexidade do seu banco de dados e seu nível de serviço. Você receberá uma notificação.

    ![exportar notificação][3]

## Monitorar o progresso da operação de exportação

1.	Clique em **Servidores SQL**.
2.	Clique no servidor que contém o banco de dados original (origem) que você acabou de arquivar.
3.  Role para baixo até Operações.
4.	Na folha SQL Server, clique em **Histórico de importação/exportação**:

    ![histórico de importação exportação][4]

## Verificar se o BACPAC está no seu contêiner de armazenamento

1.	Clique em **Contas de armazenamento**.
2.	Clique na conta de armazenamento em que você armazenou o arquivo morto BACPAC.
3.	Clique em **Contêineres** e selecione o contêiner para o qual você exportou o banco de dados a fim de obter detalhes (aqui é possível baixar e salvar o BACPAC).

    ![detalhes do arquivo .bacpac][5]

## Próximas etapas

- [Importar um BACPCAC para um banco de dados SQL do Azure](sql-database-import.md)
- [Importar um BACPCAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)

## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0420_2016-->