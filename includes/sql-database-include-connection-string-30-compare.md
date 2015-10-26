
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### Comparar a cadeia de conexão


A tabela a seguir compara as cadeias de conexão que o seu programa do C# precisa para se conectar ao SQL Server local versus o Banco de Dados SQL do Azure na nuvem. As diferenças são mostradas em negrito.


| Cadeia de conexão para o<br/>Banco de Dados SQL do Azure | Cadeia de conexão para o<br/>Microsoft SQL Server |
| :-- | :-- |
| Server=**tcp:**{seu\_nomeServidor\_aqui}**.database.windows.net,1433**;<br/>User ID={seu\_nomeLogon\_aqui}**@{seu\_nomeServidor\_aqui}**;<br/>Password={sua\_senha\_aqui};<br/>**Database={seu\_nomeBancoDados\_aqui};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; | Server={seu\_nomeServidor\_aqui};<br/>User ID={seu\_nomeLogon\_aqui};<br/>Password={sua\_senha\_aqui}; |


O campo **Database=** é opcional para o SQL Server, mas é obrigatório para o Banco de Dados SQL.


[Propriedades de SqlConnectionStringBuilder do ADO do .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) – aborda todos os parâmetros em detalhes.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->