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
	ms.date="08/15/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artigo fornece instruções para arquivar seu Banco de Dados SQL Azure em um arquivo BACPAC armazenado no armazenamento de blobs do Azure usando o [portal do Azure](https://portal.azure.com).

Quando você precisa criar um arquivo morto de um Banco de Dados SQL do Azure, pode exportar o esquema de banco de dados e os dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de BACPAC. Posteriormente, um arquivo BACPAC poderá ser colocado no armazenamento de blobs do Azure ou no armazenamento local em um local e depois importado novamente para o Banco de Dados SQL do Azure ou uma instalação local do SQL Server.

***Considerações***

- Para um arquivo morto ser transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu Banco de Dados SQL Azure.
- O tamanho máximo de um arquivo BACPAC arquivado no Armazenamento de Blobs do Azure é de 200 GB. Para arquivar um arquivo BACPAC maior no armazenamento local, use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.
- Não há suporte para o arquivamento no armazenamento premium do Azure usando um arquivo BACPAC.
- Se a operação de exportação exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumentar temporariamente seu nível de serviço.
 - Interromper toda a atividade de leitura e gravação durante a exportação.
 - Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW\_STATISTICS** e certificar-se de que o *RANGE\_HI\_KEY* não é nulo e seu valor tem boa distribuição. Para obter mais detalhes, consulte [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx) no MSDN.


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
7.  Insira as credenciais de autenticação apropriadas para o SQL Server do Azure contendo o banco de dados que está sendo exportado.
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

- Para saber mais sobre como importar um BACPAC para um Banco de Dados SQL do Azure, confira [Importar um BACPAC para um banco de dados SQL do Azure](sql-database-import.md)
- Para saber mais sobre como importar um BACPAC para um banco de dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0817_2016-->