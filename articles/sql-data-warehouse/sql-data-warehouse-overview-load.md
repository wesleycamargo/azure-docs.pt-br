   <properties
   pageTitle="Carregar dados no SQL Data Warehouse | Microsoft Azure"
   description="Aprenda sobre os cenários comuns para carregamento de dados no SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="lodipalm;barbkess"/>

# Carregar dados no SQL Data Warehouse
O SQL Data Warehouse apresenta diversas opções de carregamento de dados, incluindo:

- Fábrica de dados do Azure
- Utilitário de linha de comando BCP
- PolyBase
- SQL Server Integration Services (SSIS)
- Ferramentas de carregamento de dados de terceiros

Embora todos os métodos acima possam ser usados com o SQL Data Warehouse. Muitos de nossos usuários estão obtendo cargas iniciais de centenas de Gigabytes até dezenas de Terabytes. Nas seções abaixo, fornecemos algumas orientações sobre o carregamento de dados inicial.

## Carregamento inicial no SQL Data Warehouse do SQL Server 
Ao carregar no SQL Data Warehouse a partir de uma instância do SQL Server local, recomendamos as seguintes etapas:

1. **BCP** Dados do SQL Server em arquivos simples 
2. Use **AZCopy** ou **Importar/Exportar** (para conjuntos de dados maiores) para mover os arquivos para o Azure
3. Configurar PolyBase para ler os arquivos de sua conta de armazenamento
4. Criar novas tabelas e carregar dados com **PolyBase**

As seções a seguir analisarão cada etapa com mais detalhes e fornecerão exemplos do processo.

> [AZURE.NOTE]Antes de mover dados de um sistema, como o SQL Server, é recomendável revisar os artigos [Esquema de migração][] e o [Código de migração][] da nossa documentação.

## Exportando arquivos com BCP

Para preparar os arquivos para a movimentação para o Azure, você precisará exportá-los para arquivos simples. A melhor maneira de fazer isso é usar o Utilitário de Linha de Comando BCP. Se você ainda não tiver o utilitário, ele poderá ser baixado com os [Utilitários de linha de comando da Microsoft para SQL Server][]. Veja a seguir um exemplo parecido de comando BCP:

```
bcp "<Directory><File>" -c -T -S <Server Name> -d <Database Name>
```

Esse comando pegará os resultados de uma consulta e os exportará para um arquivo no diretório de sua escolha. Você consegue paralelizar o processo executando ao mesmo tempo vários comandos BCP para tabelas separadas. Isso permitirá a execução de até um processo BCP por núcleo do servidor, nosso conselho é testar algumas operações menores em configurações diferentes para ver o que funciona melhor para seu ambiente.

Além disso, como carregaremos usando PolyBase, observe que PolyBase ainda não dá suporte a UTF-16, e todos os arquivos devem estar em UTF-8. Isso pode ser feito facilmente, incluindo o sinalizador '-c' em seu comando BCP, ou você também pode converter arquivos simples de UTF-16 para UTF-8 com o código abaixo:

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
Depois de exportar os dados para os arquivos, é hora de movê-los para o Azure. Isso pode ser feito com o AZCopy ou com o serviço de Importação/Exportação, conforme descrito na próxima seção.

## Carregando no Azure com AZCopy ou Importação/Exportação
Se você estiver movendo dados no intervalo de 5 a 10 terabytes ou mais, recomendamos o uso do serviço de envio de disco [Importação/Exportação][] para realizar a movimentação. No entanto, em nossos estudos, fomos capazes de mover confortavelmente os dados no intervalo de TB com um único dígito usando a Internet pública com AZCopy. Esse processo também pode ser acelerado ou estendido com o ExpressRoute.

As etapas a seguir detalharão como mover dados do local para uma conta de armazenamento do Azure usando AZCopy. Se você não tiver uma conta de armazenamento do Azure na mesma região, poderá criar uma seguindo a [Documentação do armazenamento do Azure][]. Você também pode carregar dados de uma conta de armazenamento em uma região diferente, mas o desempenho nesse caso não será o melhor.

> [AZURE.NOTE]Esta documentação pressupõe que você instalou o utilitário de linha de comando AZCopy e é capaz de executá-lo com o Powershell. Se não for o caso, siga as [Instruções de instalação do AZCopy][].

Agora, com base em um conjunto de arquivos criados usando o BCP, o AzCopy pode simplesmente ser executado do powershell do Azure ou por meio da execução de um script do powershell. Em um alto nível, o prompt necessário para executar o AZCopy assumirá o formato:

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

Além do básico, recomendamos as seguintes práticas para carregar com o AZCopy:


+ **Conexões simultâneas**: além de aumentar o número de operações de AZCopy executadas simultaneamente, a própria operação do AZCopy pode ser paralelizada definindo o parâmetro /NC, que abre um número de conexões simultâneas para o destino. Embora o parâmetro possa ser definido como 512, descobrimos que a transferência de dados ideal ocorreu a 256, e recomendamos o teste de diversos valores para descobrir o que é ideal para sua configuração.

+ **Rota Expressa**: conforme mencionado anteriormente, esse processo pode ser acelerado se a rota expressa estiver habilitada. Encontre uma visão geral sobre a rota expressa e as etapas para sua configuração na [Documentação da Rota Expressa][].

+ **Estrutura de pastas**: para facilitar a transferência com o PolyBase, certifique-se de que cada tabela esteja mapeada para sua própria pasta. Isso minimizará e simplificará as etapas ao carregar posteriormente com o PolyBase. Dito isso, não há qualquer impacto se uma tabela for dividida em vários arquivos ou mesmo em subdiretórios dentro da pasta.
	 

## Configurando o PolyBase 

Agora que os dados residem em blobs de armazenamento do Azure, importaremos esses dados para sua instância de SQL Data Warehouse usando o PolyBase. As etapas a seguir servem apenas para configuração, e muitas delas só precisarão ser concluídas uma vez por instância do SQL Data Warehouse, usuário ou conta de armazenamento. Essas etapas também foram descritas com mais detalhes em nossa documentação [Carregar com PolyBase][].

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
                    ,   USE_TYPE_DEFAULT = TRUE
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
	CLUSTERED COLUMNSTORE INDEX
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

Observe que você também pode carregar uma subseção das linhas de uma tabela usando uma instrução SELECT mais detalhada. No entanto, como o PolyBase não envia computações adicionais às contas de armazenamento no momento, se você carregar uma subseção com uma instrução SELECT isso não será mais rápido do que carregar todo o conjunto de dados.

Além da instrução `CREATE TABLE...AS SELECT`, você também pode carregar dados de tabelas externas em tabelas preexistentes com uma instrução 'INSERT...INTO'.

## Próximas etapas
Para obter mais dicas de desenvolvimento, confira a [visão geral sobre desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Carregar com PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[visão geral sobre desenvolvimento]: sql-data-warehouse-overview-develop.md
[Esquema de migração]: sql-data-warehouse-migrate-schema.md
[Código de migração]: sql-data-warehouse-migrate-code.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[Instruções de instalação do AZCopy]: https://azure.microsoft.com/pt-br/documentation/articles/storage-use-azcopy/
[Utilitários de linha de comando da Microsoft para SQL Server]: http://www.microsoft.com/pt-br/download/details.aspx?id=36433
[Importação/Exportação]: https://azure.microsoft.com/pt-br/documentation/articles/storage-import-export-service/
[Documentação do armazenamento do Azure]: https://azure.microsoft.com/pt-br/documentation/articles/storage-create-storage-account/
[Documentação da Rota Expressa]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=August15_HO8-->