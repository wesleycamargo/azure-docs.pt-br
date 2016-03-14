   <properties
   pageTitle="Carregar dados no SQL Data Warehouse | Microsoft Azure"
   description="Aprenda sobre os cenários comuns para carregamento de dados no SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Carregar dados no SQL Data Warehouse
O SQL Data Warehouse apresenta diversas opções de carregamento de dados, incluindo:

- PolyBase
- Fábrica de dados do Azure
- Utilitário de linha de comando BCP
- SQL Server Integration Services (SSIS)
- Ferramentas de carregamento de dados de terceiros

Embora todos os métodos acima possam ser usados com o SQL Data Warehouse, a capacidade do PolyBase de paralelizar de forma transparente as cargas do Armazenamento de Blob do Azure tornará a ferramenta a mais rápida para o carregamento de dados. Confira mais informações sobre como [carregar com o PolyBase][]. Além disso, como muitos dos nossos usuários estão procurando cargas iniciais que vão desde centenas de gigabytes até dezenas de terabytes das fontes locais, nas seções abaixo fornecemos algumas orientações sobre o carregamento de dados inicial.

## Carregamento inicial no SQL Data Warehouse do SQL Server 
Ao carregar no SQL Data Warehouse a partir de uma instância do SQL Server local, recomendamos as seguintes etapas:

1. **BCP** Dados do SQL Server em arquivos simples 
2. Use **AZCopy** ou **Importar/Exportar** (para conjuntos de dados maiores) para mover seus arquivos para o Azure
3. Configurar PolyBase para ler os arquivos de sua conta de armazenamento
4. Criar novas tabelas e carregar dados com o **PolyBase**

As seções a seguir analisarão cada etapa com mais detalhes e fornecerão exemplos do processo.

> [AZURE.NOTE] Antes de mover dados de um sistema como o SQL Server, recomendamos que você leia os artigos [Migrar esquema][] e [Migrar código][] de nossa documentação.

## Exportando arquivos com BCP

Para preparar os arquivos para a movimentação para o Azure, você precisará exportá-los para arquivos simples. A melhor maneira de fazer isso é usar o Utilitário de Linha de Comando BCP. Se você ainda não tiver o utilitário, baixe-o com os [Utilitários de Linha de Comando da Microsoft para SQL Server][]. Veja a seguir um exemplo parecido de comando BCP:

```
bcp "select top 10 * from <table>" queryout "<Directory><File>" -c -T -S <Server Name> -d <Database Name> -- Export Query
or
bcp <table> out "<Directory><File>" -c -T -S <Server Name> -d <Database Name> -- Export Table
```

Para maximizar a taxa de transferência, você pode tentar paralelizar o processo executando vários comandos BCP simultâneos para tabelas separadas ou partições separadas em uma única tabela. Isso permitirá que você distribua o consumo da CPU por BCP ao longo de vários núcleos do servidor em que o BCP está em execução. Se você está extraindo de um sistema PDW ou SQL DW, precisará adicionar -q, argumento identificador entre aspas ao seu comando BCP. Você também pode precisar adicionar -U e -P para especificar o nome de usuário e a senha, se o seu ambiente não usar o Active Directory.

Além disso, como carregaremos usando PolyBase, observe que PolyBase ainda não dá suporte a UTF-16, e todos os arquivos devem estar em UTF-8. Isso pode ser feito facilmente, incluindo o sinalizador '-c' em seu comando BCP, ou você também pode converter arquivos simples de UTF-16 para UTF-8 com o código abaixo:

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
Depois de exportar os dados para os arquivos, é hora de movê-los para o Azure. Isso pode ser feito com o AZCopy ou com o serviço de Importação/Exportação, conforme descrito na próxima seção.

## Carregando no Azure com AZCopy ou Importação/Exportação
Se estiver movendo dados no intervalo de 5 a 10 terabytes ou mais, recomendamos o uso do serviço de envio de disco [Importação/Exportação][] para realizar a movimentação. No entanto, em nossos estudos, fomos capazes de mover confortavelmente os dados no intervalo de TB com um único dígito usando a Internet pública com AZCopy. Esse processo também pode ser acelerado ou estendido com o ExpressRoute.

As etapas a seguir detalharão como mover dados do local para uma conta de armazenamento do Azure usando AZCopy. Se você não tiver uma conta de armazenamento do Azure na mesma região, poderá criar uma seguindo a [Documentação do armazenamento do Azure][]. Você também pode carregar dados de uma conta de armazenamento em uma região diferente, mas o desempenho nesse caso não será o melhor.

> [AZURE.NOTE] Esta documentação pressupõe que você instalou o utilitário de linha de comando AZCopy e é capaz de executá-lo com o Powershell. Se este não for o caso, siga as [Instruções de instalação do AZCopy][].

Agora, com base em um conjunto de arquivos criados usando o BCP, o AzCopy pode simplesmente ser executado do powershell do Azure ou por meio da execução de um script do powershell. Em um alto nível, o prompt necessário para executar o AZCopy assumirá o formato:

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

Além do básico, recomendamos as seguintes práticas para carregar com o AZCopy:


+ **Conexões simultâneas**: além de aumentar o número das operações do AZCopy executadas simultaneamente, a própria operação do AZCopy pode ser paralelizada definindo o parâmetro /NC, que abre um número de conexões simultâneas para o destino. Embora o parâmetro possa ser definido como 512, descobrimos que a transferência de dados ideal ocorreu a 256, e recomendamos o teste de diversos valores para descobrir o que é ideal para sua configuração.

+ **Rota Expressa**: como mencionado anteriormente, esse processo pode ser acelerado se a rota expressa estiver habilitada. Encontre uma visão geral sobre a Rota Expressa e as etapas para sua configuração na [Documentação da Rota Expressa][].

+ **Estrutura de Pastas**: para facilitar a transferência com o PolyBase, certifique-se de que cada tabela esteja mapeada para sua própria pasta. Isso minimizará e simplificará as etapas ao carregar posteriormente com o PolyBase. Dito isso, não há qualquer impacto se uma tabela for dividida em vários arquivos ou mesmo em subdiretórios dentro da pasta.
	 

## Configurando o PolyBase 

Agora que os dados residem em blobs de armazenamento do Azure, importaremos esses dados para sua instância de SQL Data Warehouse usando o PolyBase. As etapas a seguir servem apenas para configuração, e muitas delas só precisarão ser concluídas uma vez por instância do SQL Data Warehouse, usuário ou conta de armazenamento. Estas etapas também foram descritas mais detalhadamente em nossa documentação [Carregar com o PolyBase][].

1. **Criar chave mestra do banco de dados.** Essa operação só precisará ser concluída uma vez para cada banco de dados. 

2. **Criar uma credencial com escopo de banco de dados.** Essa operação só precisará ser criada se você quiser criar uma nova credencial/usuário, caso contrário, use uma credencial criada anteriormente.

3. **Criar um formato de arquivo externo.** Formatos de arquivo externos também são reutilizáveis, você só precisará criar um se estiver carregando um novo tipo de arquivo.

4. **Criar uma fonte de dados externa.** Ao apontar para uma conta de armazenamento, será possível usar uma fonte de dados externa durante o carregamento do mesmo contêiner. Para o parâmetro 'LOCATION', use um local no formato: 'wasbs://meucontêiner@ test.blob.core.windows.net/caminho'.

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

Agora que sua conta de armazenamento foi configurada corretamente, é possível continuar a carregar os dados no SQL Data Warehouse.

## Carregando dados com o PolyBase 
Depois de configurar o PolyBase, você pode carregar dados diretamente no SQL Data Warehouse, simplesmente criando uma tabela externa que aponta para os dados no armazenamento e, em seguida, mapear dados para uma nova tabela no SQL Data Warehouse. Isso pode ser feito com os dois comandos simples abaixo.

1. Use o comando 'CRIAR TABELA EXTERNA' para definir a estrutura dos dados. Para capturar o estado dos dados de forma rápida e eficiente, recomendamos a criação de um script da tabela do SQL Server no SSMS, e o ajuste manual para considerar as diferenças da tabela externa. Depois de criar uma tabela externa no Azure, ele continuará a apontar para o mesmo local, mesmo se os dados forem atualizados ou outros dados forem adicionados.  

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. Carregar dados com uma instrução 'CREATE TABLE... AS SELECT'. 

```
CREATE TABLE <Table Name> 
WITH 
(
	CLUSTERED COLUMNSTORE INDEX,
	DISTRIBUTION = <HASH(<Column Name>)>/<ROUND_ROBIN>
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

Observe que você também pode carregar uma subseção das linhas de uma tabela usando uma instrução SELECT mais detalhada. No entanto, como o PolyBase não envia computações adicionais às contas de armazenamento no momento, se você carregar uma subseção com uma instrução SELECT isso não será mais rápido do que carregar todo o conjunto de dados.

Além da instrução `CREATE TABLE...AS SELECT`, você também pode carregar dados de tabelas externas em tabelas preexistentes com uma instrução 'INSERT...INTO'.

##  Criar estatísticas sobre os dados recém-carregados 

O SQL Data Warehouse do Azure ainda não dá suporte a estatísticas de criação ou atualização automática. Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou após uma alteração significativa nos dados. Para obter uma explicação detalhada das estatísticas, confira o tópico [Estatísticas][] no grupo de tópicos Desenvolver. Veja abaixo um exemplo de como criar estatísticas na tabela carregada neste exemplo.


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## Próximas etapas
Para obter mais dicas de desenvolvimento, confira a [visão geral sobre desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[carregar com o PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[visão geral sobre desenvolvimento]: sql-data-warehouse-overview-develop.md
[Migrar esquema]: sql-data-warehouse-migrate-schema.md
[Migrar código]: sql-data-warehouse-migrate-code.md
[Estatísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[Instruções de instalação do AZCopy]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-use-azcopy/
[Utilitários de Linha de Comando da Microsoft para SQL Server]: http://www.microsoft.com/pt-BR/download/details.aspx?id=36433
[Importação/Exportação]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-import-export-service/
[Documentação do armazenamento do Azure]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-create-storage-account/
[Documentação da Rota Expressa]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=AcomDC_0302_2016-->