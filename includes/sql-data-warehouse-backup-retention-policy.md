
<!--
includes/sql-data-warehouse-backup-retention-policies.md

Latest Freshness check:  2016-05-05 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-overview-expectations.md
articles/sql-data-warehouse/sql-data-warehouse-overview-backup-and-restore.md
-->
O SQL Data Warehouse faz cópias instantâneas de todos os dados dinâmicos pelo menos a cada 8 horas usando Instantâneos de Armazenamento do Azure. Esses instantâneos são mantidos por 7 dias. Isso permite restaurar os dados para pelo menos 21 pontos no tempo nos últimos 7 dias até o momento em que o último instantâneo foi capturado.

O SQL Data Warehouse captura um instantâneo do banco de dados antes de um banco de dados ser removido e o retém por sete dias. Quando isso ocorre, ele não retém instantâneos do banco de dados dinâmico. Isso permite que você restaure um banco de dados excluído para o ponto quando ele foi excluído.

O SQL Data Warehouse copia instantâneos assincronamente para uma região geográfica do Azure diferente para capacidade de recuperação adicionada em caso de falha regional. Se você não conseguir acessar o banco de dados devido a uma falha em uma região do Azure, você poderá restaurar seu banco de dados para um dos instantâneos com redundância geográfica.

<!---HONumber=AcomDC_0525_2016-->