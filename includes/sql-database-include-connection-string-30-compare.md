
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparar a cadeia de conexão
A tabela a seguir compara as cadeias de conexão que o seu programa do C# precisa para se conectar ao SQL Server local versus o Banco de Dados SQL do Azure na nuvem. As diferenças são mostradas em negrito.

| Cadeia de conexão para o<br/>Banco de Dados SQL do Azure | Cadeia de conexão para o<br/>Microsoft SQL Server |
|:--- |:--- |
| Server=**tcp:**{nome_doServidor_aqui}**.database.windows.net,1433**;<br/>User ID={seu_nomeDeLogon_aqui}**@{your_serverName_here}**;<br/>Password={sua_senha_aqui};<br/>**Database={nome_doBancoDeDados_aqui};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; |Server={nome_doServidor_aqui};<br/>ID de usuário = {seu_nomeDeLogon_aqui};<br/>Password={sua_senha_aqui}; |

O campo **Database=** é opcional para o SQL Server, mas é obrigatório para o Banco de Dados SQL.

[Propriedades de SqlConnectionStringBuilder do ADO do .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) – aborda todos os parâmetros em detalhes.

<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Jan17_HO3-->


