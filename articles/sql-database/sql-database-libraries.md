<properties
	pageTitle="Bibliotecas de conexão para Banco de Dados SQL e SQL Server"
	description="Lista o número de versão mínimo para cada driver que os programas clientes podem usar para se conectarem ao Banco de Dados SQL do Azure ou ao Microsoft SQL Server. Foi fornecido um link para obtenção de informações de versão sobre drivers lançados pela comunidade, e não pela Microsoft."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="pehteh"/>


# Bibliotecas de conexão para Banco de Dados SQL e SQL Server


Este tópico lista o número de versão mínimo para cada biblioteca/driver que os programas clientes podem usar ao se conectarem ao Banco de Dados SQL do Azure ou ao Microsoft SQL Server.


Este tópico é dividido em duas seções:


- *Tabela das bibliotecas de driver lançadas pela Microsoft* -abrange as bibliotecas lançadas pela Microsoft. A Microsoft mantém as informações contidas nesta seção.
- *Bibliotecas de terceiros* -lista as bibliotecas lançadas e mantidas por terceiros, e não pela Microsoft. **Somente a comunidade pública de desenvolvedores mantém esta seção. A Microsoft não manterá esta seção.**


## Tabela de bibliotecas de driver lançadas pela Microsoft


A tabela a seguir exibe as bibliotecas lançadas pela Microsoft. A coluna **Bibliotecas** fornece links que você pode usar para baixar cada biblioteca. A coluna **Versão** lista a versão mínima recomendada para interação com o Banco de Dados SQL do Azure e com o Microsoft SQL Server.


| Plataforma | Sistema operacional | Bibliotecas<br/>para Download | Versão<br/>do Driver | Descrição<br/>do Driver | Mais<br/>informações |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | Plataforma cruzada (.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5+ | Provedor do SQL Server para .NET Framework | . |
| PHP | Windows | [PHP para SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0+ | Driver PHP para SQL Server | [Link](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0+ | Driver JDBC tipo 4 que fornece conectividade de banco de dados por meio da API JDBC padrão | [Link](http://msdn.microsoft.com/library/dn425070.aspx) |
| ODBC | Windows | [ODBC para SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0+ | Microsoft ODBC Driver para SQL Server | [Link](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC para SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0+ | Microsoft ODBC Driver para SQL Server | . |
| ODBC | Redhat Linux | [ODBC para SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0+ | Microsoft ODBC Driver para SQL Server | . |


### OLEDB para DB2 e SQL Server, para design DRDA


O Microsoft OLE DB Provider for DB2 versão 5.0 (Provedor de Dados) permite criar aplicativos distribuídos destinados a bancos de dados IBM DB2. O Provedor de Dados aproveita a arquitetura de acesso aos dados do Microsoft SQL Server junto com um cliente de rede da Microsoft para DB2 que funciona como um solicitante de aplicativo DRDA (Distributed Relational Database Architecture). O Provedor de Dados converte tipos de dados e comandos OLE DB Microsoft COM (Component Object Model) em pontos de código de protocolo DRDA e formatos de dados.


Para obter mais informações, consulte:


- [Microsoft OLE DB Provider para DB2 versão 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Microsoft OLEDB Provider para DB2 v4.0 para Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)


## Bibliotecas de terceiros


> [AZURE.IMPORTANT]A tabela a seguir exibe as bibliotecas lançadas por terceiros sob os termos de licença de terceiros. Você é responsável pela verificação e conformidade com as licenças relevantes de terceiros aplicáveis ao uso dessas bibliotecas. O uso dessas bibliotecas é por sua conta e risco. A Microsoft não oferece quaisquer garantias, expressas ou implícitas, quanto às informações fornecidas aqui, e simplesmente forneceu as informações como uma questão de conveniência para os usuários. Nada contido neste documento indica qualquer tipo de endosso da Microsoft. <br/><br/>Cabe a comunidade pública de desenvolvedores atualizar e manter as informações desta seção "Bibliotecas de terceiros", usando o repositório [azure-content](http://github.com/Azure/azure-content/) pertencentes ao **Azure** em GitHub.com. A Microsoft incentiva os desenvolvedores a atualizar esta seção. A equipe da Microsoft *não* pretende manter as informações contidas nesta seção, em parte porque outras pessoas têm mais experiência e especialização do que nós com relação a cada biblioteca de terceiros específica. Obrigado.


A tabela a seguir exibe as bibliotecas lançadas por terceiros, como outras empresas, ou pela comunidade. As bibliotecas lançadas pela Microsoft estão restritas à seção anterior deste tópico.


| Plataforma | Bibliotecas |
| :-- | :-- |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk, github io)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=August15_HO6-->