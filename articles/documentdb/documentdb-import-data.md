<properties 
	pageTitle="Importar dados no Banco de Dados de Documentos | Azure" 
	description="Saiba como usar a ferramenta de migração de dados de software livre do Banco de Dados de Documentos para importar dados no Banco de Dados de Documentos de várias fontes, incluindo arquivos JSON, arquivos CSV, coleções de SQL, MongoDB, Armazenamento da Tabela do Azure e do Banco de Dados de Documentos." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="stbaro"/>

# Importar dados no Banco de Dados de Documentos #

Este artigo mostra como usar a ferramenta de migração de dados de software livre do Banco de Dados de Documentos para importar dados no [Banco de Dados de Documentos do Microsoft Azure](http://azure.microsoft.com/services/documentdb/) de várias fontes, incluindo arquivos JSON, arquivos CSV, coleções de SQL, MongoDB, Armazenamento da Tabela do Azure e do Banco de Dados de Documentos.

Após ler este artigo, você poderá responder as perguntas a seguir:

-	Como importar dados do arquivo JSON para o Banco de Dados de Documentos?
-	Como importar dados do arquivo CSV para o Banco de Dados de Documentos?
-	Como importar dados do SQL Server para o Banco de Dados de Documentos?
-	Como importar dados do MongoDB para o Banco de Dados de Documentos?
-	Como importar dados do armazenamento de tabela do Azure para o Banco de Dados de Documentos?
-	Como migrar dados entre coleções do Banco de Dados de Documentos?

##<a id="Prerequisites"></a>Pré-requisitos ##

Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

- [Microsoft .NET Framework 4.51](http://www.microsoft.com/net/downloads) ou superior.

##<a id="Overviewl"></a>Visão geral da ferramenta de migração de dados do Banco de Dados de Documentos ##

A ferramenta de migração de dados do Banco de Dados de Documentos é uma solução de software livre que importa dados para o Banco de Dados de Documentos de uma variedade de fontes, incluindo:

- Arquivos JSON
- MongoDB
- SQL Server
- Arquivos CSV
- Armazenamento da tabela do Azure
- Coleções do Banco de Dados de Documentos

Embora a ferramenta de importação inclua uma interface gráfica do usuário (dtui.exe), ela também pode ser controlada pela linha de comando (dt.exe). Na verdade, há uma opção de extrair o comando associado depois de configurar uma importação por meio da interface do usuário. Dados de origem em tabela (por exemplo, arquivos do SQL Server ou CSV) podem ser transformados, de forma que relações hierárquicas (subdocumentos) podem ser criadas durante a importação. Continue lendo para saber mais sobre as opções de origem, linhas de comando de exemplo para importar de cada origem, opções de destino e resultados de importação de visualização.


##<a id="Install"></a>Instalando a ferramenta de migração de dados do Banco de Dados de Documentos ##

O código-fonte da ferramenta de migração está disponível no GitHub [nesse repositório](https://github.com/azure/azure-documentdb-datamigrationtool) e há uma versão compilada no [Centro de Download da Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Você pode compilar a solução ou simplesmente baixar e extrair a versão compilada em um diretório de sua escolha. Em seguida, execute um:

- Dtui.exe: Versão da interface gráfica da ferramenta
- Dt.exe: Versão de linha de comando da ferramenta

##<a id="JSON"></a>Importar arquivos JSON ##

A opção de importador de origem de arquivo JSON permite importar um ou mais arquivos JSON de documento único ou arquivos JSON que contêm uma matriz de documentos JSON. Ao adicionar pastas que contêm arquivos JSON para importar, você tem a opção de pesquisar recursivamente arquivos em subpastas.

![Captura de tela das opções de origem do arquivo JSON](./media/documentdb-import-data/jsonsource.png)

Aqui estão alguns exemplos de linha de comando para importar os arquivos JSON:

	#Import a single JSON file	
	dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory of JSON files
	dt.exe /s:JsonFile /s.Files:C:\TESessions*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory (including sub-directories) of JSON files
	dt.exe /s:JsonFile /s.Files:C:\LastFMMusic***.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionTier:S3

	#Import a directory (single), directory (recursive), and individual JSON files
	dt.exe /s:JsonFile /s.Files:C:\Tweets*.*;C:\LargeDocs***.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionTier:S3

	#Import a single JSON file and partition the data across 4 collections
	dt.exe /s:JsonFile /s.Files:D:\CompanyData\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionTier:S3

##<a id="MongoDB"></a>Importar do MongoDB ##

A opção de importador de origem do MongoDB permite importar de uma coleção do MongoDB individual e opcionalmente filtrar documentos usando uma consulta e/ou modificar a estrutura do documento usando uma projeção.

![Captura de tela das opções de origem do MongoDB](./media/documentdb-import-data/mongodbsource.png)

A cadeia de conexão está no formato padrão do MongoDB:

	mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE]Use o comando Verify para garantir que a instância do MongoDB especificada no campo de cadeia de conexão pode ser acessada.

Digite o nome da coleção por meio da qual os dados serão importados. Você pode opcionalmente especificar ou fornecer um arquivo para uma consulta (por exemplo, {pop: {$gt: 5000}}) e/ou uma projeção (por exemplo, {loc:0}) para filtrar e moldar os dados a serem importados.

Aqui estão alguns exemplos de linha de comando para importar por meio do MongoDB:

	#Import all documents from a MongoDB collection
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionTier:S3

	#Import documents from a MongoDB collection which match the query and exclude the loc field
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionTier:S3

##<a id="MongoDBExport"></a>Importar arquivos de exportação do MongoDB ##

A opção do importador de origem de arquivo JSON de exportação do MongoDB permite que você importe um ou mais arquivos JSON produzidos por meio do utilitário mongoexport.

![Captura de tela das opções de origem de exportação do MongoDB](./media/documentdb-import-data/mongodbexportsource.png)

Ao adicionar pastas que contêm arquivos JSON de exportação do MongoDB, você tem a opção de pesquisar recursivamente arquivos em subpastas.

Aqui está um exemplo de linha de comando para importar de arquivos de JSON de exportação do MongoDB:

	dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionTier:S3

##<a id="SQL"></a>Importar do SQL Server ##

A opção do importador de origem do SQL permite importar de um banco de dados do SQL Server individual e, opcionalmente, filtrar os registros a serem importados usando uma consulta. Além disso, você pode modificar a estrutura do documento, especificando um separador de aninhamento (falaremos mais sobre isso em instantes).

![Captura de tela das opções de origem do SQL](./media/documentdb-import-data/sqlexportsource.png)

O formato da cadeia de conexão é o formato da cadeia de conexão SQL padrão.

> [AZURE.NOTE]Use o comando Verify para garantir que a instância do SQL Server especificada no campo Cadeia de conexão pode ser acessada.

A propriedade de separador de aninhamento é usada para criar relacionamentos hierárquicos (sub-documentos) durante a importação. Considere a seguinte consulta SQL:

*Selecione CAST (BusinessEntityID como varchar) como ID, Nome, AddressType como [Address.AddressType], AddressLine1 como [Address.AddressLine1], City como [Address.Location.City], StateProvinceName como [Address.Location.StateProvinceName], PostalCode como [Address.PostalCode], CountryRegionName como [Address.CountryRegionName] de Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Que retorna os seguintes resultados (parciais):

![Captura de tela dos resultados de consulta do SQL](./media/documentdb-import-data/sqlqueryresults.png)

Observe os aliases como Address.AddressType e Address.Location.StateProvinceName. Especificando um separador de aninhamento de “.”, a ferramenta de importação cria os sub-documentos Address e Address.Location durante a importação. Aqui está um exemplo de um documento resultante no Banco de Dados de Documentos:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*
 
Aqui estão alguns exemplos de linha de comando para importar do SQL Server:

	#Import records from SQL which match a query	
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionTier:S3

	#Import records from sql which match a query and create hierarchical relationships
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionTier:S3

##<a id="CSV"></a>Importar arquivos CSV ##

A opção de importador de origem de arquivo CSV permite que você importe um ou mais arquivos CSV. Ao adicionar pastas que contêm arquivos CSV para importar, você tem a opção de pesquisar recursivamente arquivos em subpastas.

![Captura de tela das opções de origem do CSV](media/documentdb-import-data/csvsource.png)

De forma semelhante à origem de SQL, a propriedade de separador de aninhamento pode ser usada para criar relacionamentos hierárquicos (sub-documentos) durante a importação. Considere a seguinte linha de cabeçalho CSV e linhas de dados:

![Captura de tela dos registros de exemplo do CSV](./media/documentdb-import-data/csvsample.png)

Observe os aliases como DomainInfo.Domain_Name e RedirectInfo.Redirecting. Especificando um separador de aninhamento de “.”, a ferramenta de importação cria os sub-documentos DomainInfo e RedirectInfo durante a importação. Aqui está um exemplo de um documento resultante no Banco de Dados de Documentos:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

A ferramenta de importação tentará deduzir informações de tipo para valores sem aspas em arquivos CSV (valores entre aspas são tratados sempre como cadeias de caracteres). Os tipos são identificados na seguinte ordem: número, datetime, booliano.

Há duas outras coisas a observar sobre a importação de CSV:

1.	Por padrão, os valores sem aspas sempre são cortados para tabulações e espaços, enquanto os valores entre aspas são preservados como estão. Esse comportamento pode ser substituído por a caixa de seleção com valores entre aspas de corte ou pela opção de linha de comando /s.TrimQuoted.

2.	Por padrão, um nulo sem aspas é tratado como um valor nulo. Esse comportamento pode ser substituído (isto é, trate um nulo sem aspas como uma cadeia de caracteres "null") com o NULL de tratamento sem aspas como caixa de seleção da cadeia de caracteres ou a opção de linha de comando /s.NoUnquotedNulls.


Aqui está um exemplo de linha de comando para importação de CSV:

	dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionTier:S3

##<a id="AzureTableSource"></a>Importar por meio do armazenamento de tabela do Azure ##

A opção de importador de origem de armazenamento de tabela do Azure permite importar de uma tabela de armazenamento de uma tabela individual do Azure e filtrar opcionalmente as entidades da tabela a serem importadas.

![Captura de tela das opções de origem de armazenamento da tabela do Azure](./media/documentdb-import-data/azuretablesource.png)

O formato da cadeia de conexão de armazenamento de tabela do Azure é:

	DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE]Use o comando Verify para garantir que a instância de armazenamento da tabela do Azure especificada no campo de cadeia de conexão pode ser acessada.

Digite o nome da tabela do Azure por meio da qual os dados serão importados. Opcionalmente, você pode especificar um [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

A opção de importador de origem de armazenamento de tabela do Azure tem as seguintes opções adicionais:

1. Incluir campos internos 
	2. Todos - incluir todos os campos internos (PartitionKey, RowKey e Timestamp)
	3. Nenhum - excluir todos os campos internos
	4. RowKey - incluir somente o campo RowKey
3. Selecionar colunas
	1. Filtros de armazenamento de tabela do Azure não dão suporte a projeções. Se você quiser importar somente propriedades específicas de entidade da tabela do Azure, adicione-as à lista Selecionar colunas. Todas as outras propriedades da entidade serão ignoradas.

Aqui está um exemplo de linha de comando para importar por meio do armazenamento de tabela do Azure:

	dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionTier:S3

##<a id="DocumentDBSource"></a>Importar por meio do Banco de Dados de Documentos ##

A opção de importador de origem do Banco de Dados de Documentos permite importar dados de uma ou mais coleções do Banco de Dados de Documentos e, opcionalmente, filtrar documentos usando uma consulta.

![Captura de tela das opções de origem do Banco de Dados de Documentos](./media/documentdb-import-data/documentdbsource.png)

O formato da cadeia de conexão do Banco de Dados de Documentos é:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]Use o comando Verify para garantir que a instância do Banco de Dados de Documentos especificada no campo de cadeia de conexão pode ser acessada.

Para importar de uma única coleção de Banco de Dados de Documentos, digite o nome da coleção na qual os dados serão importados. Para importar de várias coleções de Banco de Dados de Documentos, forneça uma expressão regular para corresponder um ou mais nomes de coleção (por exemplo, collection01 | collection02 | collection03). Você pode opcionalmente especificar ou fornecer um arquivo para uma consulta para filtrar e moldar os dados a serem importados.

> [AZURE.NOTE]Uma vez que o campo de coleção aceita expressões regulares, se você estiver importando de uma única coleção cujo nome contém caracteres de expressão regular, esses caracteres devem ser substituídos adequadamente.

A opção de importador de origem do Banco de Dados de Documentos tem as seguintes opções avançadas:

1. Incluir campos internos: especifica se as propriedades do sistema de documentos do Banco de Dados de Documentos devem ser incluídas na exportação (por exemplo, _rid, _ts). 2. Número de novas tentativas em caso de falha: Especifica o número de vezes para tentar se conectar novamente com o Banco de Dados de Documentos em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
3. Intervalo de novas tentativas: Especifica quanto tempo esperar para tentar se conectar novamente com o Banco de Dados de Documentos em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
4. Modo de conexão: Especifica o modo de conexão a ser usado com o Banco de Dados de Documentos. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

![Captura de tela das opções avançadas de origem do Banco de Dados de Documentos](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP]A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.


Aqui estão alguns exemplos de linha de comando para importar por meio do Banco de Dados de Documentos:

	#Migrate data from one DocumentDB collection to another DocumentDB collections
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionTier:S3

	#Migrate data from multiple DocumentDB collections to a single DocumentDB collection
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionTier:S3

	#Export a DocumentDB collection to a JSON file
	dt.exe /s:DocumentDB /s.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionTier:S3

##<a id="DocumentDBBulkTarget"></a>Importar para o Banco de Dados de Documentos (importação em massa) ##

O importador em massa do Banco de Dados de Documentos permite importar de qualquer uma das opções de origem disponíveis, usando um procedimento armazenado do Banco de Dados de Documentos para maior eficiência. A ferramenta dá suporte à importação para uma única coleção de Banco de Dados de Documento, bem como importação fragmentada na qual os dados são particionados em várias coleções de Banco de Dados de Documentos. Leia mais sobre o particionamento de dados em Banco de Dados de Documentos [aqui](documentdb-partition-data.md). A ferramenta vai criar, executar e, em seguida, excluir o procedimento armazenado da(s) coleção(ões) de destino.

![Captura de tela das opções em massa do Banco de Dados de Documentos](./media/documentdb-import-data/documentdbbulk.png)

O formato da cadeia de conexão do Banco de Dados de Documentos é:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]Use o comando Verify para garantir que a instância do Banco de Dados de Documentos especificada no campo de cadeia de conexão pode ser acessada.

Para importar para uma única coleção, digite o nome da coleção à qual os dados serão importados e clique no botão Adicionar. Para importar para várias coleções, insira o nome de cada coleção individualmente ou use a seguinte sintaxe para especificar várias coleções: *collection_prefix*[start index - end index]. Ao especificar várias coleções por meio da sintaxe mencionada anteriormente, lembre-se do seguinte:

1. Somente padrões de nome de intervalo inteiro têm suporte. Por exemplo, a especificação de coleção [0-3] produzirá as seguintes coleções: collection0, collection1, collection2, collection3.
2. Você pode usar uma sintaxe abreviada: collection[3] emitirá o mesmo conjunto de coleções mencionado na etapa 1.
3. Mais de uma substituição pode ser fornecida. Por exemplo, a coleção [0-1] [0-9] gerará 20 nomes de coleção com zeros à esquerda (collection01... 02,... 03).

Depois de especificar o(s) nome(s) da coleção, escolha a camada de preços desejada da(s) coleção(ões) (S1, S2 ou S3). Para melhor desempenho de importação, escolha S3. Leia mais sobre os níveis de desempenho do Banco de Dados de Documentos [aqui](documentdb-performance-levels.md).

> [AZURE.NOTE]A configuração de camada de desempenho só se aplica à criação da coleção. Se a coleção especificada já existir, sua camada de preço não será modificada.

Ao importar para várias coleções, a ferramenta de importação dá suporte a hash baseado em fragmentação. Neste cenário, especifique a propriedade do documento que deseja usar como a Chave de partição (se a Chave de partição for deixada em branco, os documentos serão fragmentados aleatoriamente em coleções de destino).

Opcionalmente, você pode especificar qual campo na origem de importação deve ser usado como a propriedade de ID do documento do Banco de Dados de Documentos durante a importação (observe que se documentos não contiverem essa propriedade, a ferramenta de importação vai gerar um GUID como o valor da propriedade de ID).

Há uma série de opções avançadas disponíveis durante a importação. Em primeiro lugar, embora a ferramenta inclua um procedimento armazenado de importação em massa padrão (BulkInsert.js), você pode optar por especificar seu próprio procedimento armazenado de importação:

 ![Captura de tela da opção sproc de inserção em massa do Banco de Dados de Documentos](./media/documentdb-import-data/bulkinsertsp.png)

Adicionalmente, ao importar tipos de dados (por exemplo, do SQL Server ou do MongoDB), você pode escolher entre três opções de importação:

 ![Captura de tela das opções de importação de data e horário do Banco de Dados de Documentos](./media/documentdb-import-data/datetimeoptions.png)

-	Cadeia de caracteres: Persistir como um valor de cadeia de caracteres
-	Época: Persistir como um valor de número de época
-	Ambos: Persistir com os valores de número de cadeia de caracteres e de época. Essa opção criará um sub-documento, por exemplo: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245} 


O importador em massa do Banco de Dados de Documentos tem as seguintes opções avançadas adicionais:

1. Tamanho do lote: A ferramenta usa como padrão um tamanho de lote de 50. Se os documentos a serem importados forem grandes, considere reduzir o tamanho do lote. Da mesma forma, se os documentos a serem importados forem pequenos, considere aumentar o tamanho do lote.
2. Tamanho máximo de script (bytes): A ferramenta usa como padrão a um tamanho máximo de script de 960KB
3. Desabilitar a geração automática de ID: Se todos os documentos a serem importados contiverem um campo de identificação, selecionar essa opção pode aumentar o desempenho. Documentos com um campo de ID exclusiva ausente não serão importados.
4. Número de novas tentativas em caso de falha: Especifica o número de vezes para tentar se conectar novamente com o Banco de Dados de Documentos em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
5. Intervalo de novas tentativas: Especifica quanto tempo esperar para tentar se conectar novamente com o Banco de Dados de Documentos em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
6. Modo de conexão: Especifica o modo de conexão a ser usado com o Banco de Dados de Documentos. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

![Captura de tela das opções avançadas de importação em massa do Banco de Dados de Documentos](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP]A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.

##<a id="DocumentDBSeqTarget"></a>Importar para o Banco de Dados de Documentos (importação de registros sequenciais) ##

O importador de registro sequencial do Banco de Dados de Documentos permite que você importe por meio de qualquer uma das opções de origem disponíveis com base em cada registro. Você pode escolher esta opção se estiver importando para uma coleção existente que já atingiu a cota de procedimentos armazenados. A ferramenta dá suporte à importação para uma única coleção de Banco de Dados de Documento, bem como importação fragmentada na qual os dados são particionados em várias coleções de Banco de Dados de Documentos. Leia mais sobre o particionamento de dados em Banco de Dados de Documentos [aqui](documentdb-partition-data.md).

![Captura de tela das opções de importação de registro sequencial do Banco de Dados de Documentos](./media/documentdb-import-data/documentdbsequential.png)

O formato da cadeia de conexão do Banco de Dados de Documentos é:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE]Use o comando Verify para garantir que a instância do Banco de Dados de Documentos especificada no campo de cadeia de conexão pode ser acessada.

Para importar para uma única coleção, digite o nome da coleção à qual os dados serão importados e clique no botão Adicionar. Para importar para várias coleções, insira o nome de cada coleção individualmente ou use a seguinte sintaxe para especificar várias coleções: *collection_prefix*[start index - end index]. Ao especificar várias coleções por meio da sintaxe mencionada anteriormente, lembre-se do seguinte:

1. Somente padrões de nome de intervalo inteiro têm suporte. Por exemplo, a especificação de coleção [0-3] produzirá as seguintes coleções: collection0, collection1, collection2, collection3.
2. Você pode usar uma sintaxe abreviada: collection[3] emitirá o mesmo conjunto de coleções mencionado na etapa 1.
3. Mais de uma substituição pode ser fornecida. Por exemplo, a coleção [0-1] [0-9] gerará 20 nomes de coleção com zeros à esquerda (collection01... 02,... 03).

Depois de especificar o(s) nome(s) da coleção, escolha a camada de preços desejada da(s) coleção(ões) (S1, S2 ou S3). Para melhor desempenho de importação, escolha S3. Leia mais sobre os níveis de desempenho do Banco de Dados de Documentos [aqui](documentdb-performance-levels.md).

> [AZURE.NOTE]A configuração de camada de desempenho só se aplica à criação da coleção. Se a coleção especificada já existir, sua camada de preço não será modificada.

Ao importar para várias coleções, a ferramenta de importação dá suporte a hash baseado em fragmentação. Neste cenário, especifique a propriedade do documento que deseja usar como a Chave de partição (se a Chave de partição for deixada em branco, os documentos serão fragmentados aleatoriamente em coleções de destino).

Opcionalmente, você pode especificar qual campo na origem de importação deve ser usado como a propriedade de ID do documento do Banco de Dados de Documentos durante a importação (observe que se documentos não contiverem essa propriedade, a ferramenta de importação vai gerar um GUID como o valor da propriedade de ID).

Há uma série de opções avançadas disponíveis durante a importação. Primeiro, ao importar tipos de dados (por exemplo, do SQL Server ou do MongoDB), você pode escolher entre três opções de importação:

 ![Captura de tela das opções de importação de data e horário do Banco de Dados de Documentos](./media/documentdb-import-data/datetimeoptions.png)

-	Cadeia de caracteres: Persistir como um valor de cadeia de caracteres
-	Época: Persistir como um valor de número de época
-	Ambos: Persistir com os valores de número de cadeia de caracteres e de época. Essa opção criará um sub-documento, por exemplo: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245} 

O importador de registros sequenciais do Banco de Dados de Documentos tem as seguintes opções avançadas adicionais:

1. Número de solicitações paralelas: A ferramenta usa como padrão 2 solicitações paralelas. Se os documentos a serem importados forem pequenos, considere aumentar o número de solicitações paralelas. Observe que se esse número for muito elevado, a importação poderá sofrer limitação.
2. Desabilitar a geração automática de ID: Se todos os documentos a serem importados contiverem um campo de identificação, selecionar essa opção pode aumentar o desempenho. Documentos com um campo de ID exclusiva ausente não serão importados.
3. Número de novas tentativas em caso de falha: Especifica o número de vezes para tentar se conectar novamente com o Banco de Dados de Documentos em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
4. Intervalo de novas tentativas: Especifica quanto tempo esperar para tentar se conectar novamente com o Banco de Dados de Documentos em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
5. Modo de conexão: Especifica o modo de conexão a ser usado com o Banco de Dados de Documentos. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

![Captura de tela das opções avançadas de importação de registro sequencial do Banco de Dados de Documentos](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP]A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.

## Exportar para arquivo JSON

O exportador de JSON do Banco de Dados de Documentos permite exportar qualquer uma das opções de origem disponíveis para um arquivo JSON que contém uma matriz de documentos JSON. A ferramenta manipulará a exportação por você, ou você pode optar por exibir o comando de migração resultante e executar o comando por conta própria.

![Captura de tela da opção de exportação de JSON no Banco de Dados de Documentos](./media/documentdb-import-data/jsontarget.png)

Você pode optar por melhorar a aparência do JSON resultante, o que aumentará o tamanho do documento resultante, tornando o conteúdo mais legível.

	Standard JSON export
	[{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
	
	Prettified JSON export
	[
 	{
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
	}]
	
## Configuração avançada

Na tela de Configuração avançada, especifique a localização do arquivo de log do qual você gostaria que os erros fossem gravados. As seguintes regras se aplicam a esta página:

1.	Se não for fornecido um nome de arquivo, todos os erros serão retornados na página Resultados.
2.	Se for fornecido um nome de arquivo sem um diretório, o arquivo será ser criado (ou substituído) no diretório atual do ambiente.
3.	Se você selecionar um arquivo existente, o arquivo será substituído, não há nenhuma opção de acréscimo.

	![Tela de configuração de captura de tela avançada](./media/documentdb-import-data/AdvancedConfiguration.png)

## Confirme as configurações de importação e de linha de comando de exibição

1. Depois de especificar as informações de origem, as informações de destino e a configuração avançada, examine a resumo de migração e, opcionalmente, examine/copie o comando resultante da migração (copiar o comando é útil para automatizar as operações de importação):

	![Captura de tela da tela de resumo](./media/documentdb-import-data/summary.png)

	![Captura de tela da tela de resumo](./media/documentdb-import-data/summarycommand.png)

2. Quando estiver satisfeito com suas opções de origem e de destino, clique em **Importar**. O tempo decorrido, a contagem transferida e as informações de falha (se você não fornecer um nome de arquivo na Configuração avançada) serão atualizados à medida que a importação está em andamento. Uma vez concluído, você pode exportar os resultados (por exemplo, para lidar com as falhas de importação).

	![Captura de tela da opção de exportação de JSON no Banco de Dados de Documentos](./media/documentdb-import-data/viewresults.png)

3. Você também pode iniciar uma nova importação, mantendo as configurações existentes (por exemplo, informações da cadeia de conexão, escolha de origem e destino, etc.) ou redefinindo todos os valores.

	![Captura de tela da opção de exportação de JSON no Banco de Dados de Documentos](./media/documentdb-import-data/newimport.png)

## Próximas etapas

- Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).


 

<!---HONumber=58_postMigration-->