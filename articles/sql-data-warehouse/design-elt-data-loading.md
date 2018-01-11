---
title: Projeto ELT para o SQL Data Warehouse do Azure | Microsoft Docs
description: Combine tecnologias para mover dados para o Azure e carregue os dados no SQL Data Warehouse para criar um processo de Extrair, Carregar e Transformar (ELT) para o Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 12/12/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: e94dca69c77c46034e318205279be5188e1371f5
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Criação de Extrair, Carregar e Transformar (ELT) para o Azure SQL Data Warehouse

Combine as tecnologias para descarregar dados no Armazenamento do Azure e carregue os dados no SQL Data Warehouse para criar um processo de Extrair, Carregar e Transformar (ELT) para o Azure SQL Data Warehouse. Este artigo apresenta as tecnologias que dão suporte ao carregamento com o Polybase e, em seguida, se concentra na criação de um processo ELT que usa o PolyBase com o T-SQL para carregar dados no SQL Data Warehouse a partir do Armazenamento do Microsoft Azure.

## <a name="what-is-elt"></a>O que é ELT?

Extrair, Carregar e Transformar (ELT) é um processo pelo qual os dados são movidos de um sistema de origem para um data warehouse de destino. Esse processo é executado regularmente, por exemplo, a cada hora ou diariamente, para obter dados recentemente gerados no data warehouse. A maneira ideal de levar os dados de origem ao data warehouse é desenvolver um processo ELT que usa o PolyBase para carregar dados no SQL Data Warehouse.

O ELT carrega primeiro e, em seguida, transforma os dados, enquanto a Extrair, Transformar e Carregar (ETL) transforma os dados antes de carregá-los. Executar o ELT em vez do ETL economiza o custo de fornecimento dos seus próprios recursos para transformar os dados antes de eles serem carregados. Ao usar o SQL Data Warehouse, o ELT alavanca o sistema MPP para executar as transformações.

Embora existam muitas variações para implementar o ELT para o SQL Data Warehouse, estas são as etapas básicas:  

1. Extrair os dados de origem em arquivos de texto.
2. Descarregar os dados no armazenamento de Blobs do Azure ou no Azure Data Lake Store.
3. Preparar os dados para o carregamento.
2. Carregar os dados na tabela de preparo do SQL Data Warehouse usando o PolyBase.
3. Transformar os dados.
4. Inserir os dados nas tabelas de produção.


Para ver um tutorial de carregamento, consulte [Usar o PolyBase para carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse do Azure](load-data-from-azure-blob-storage-using-polybase.md).

Para obter mais informações, consulte [Blog de padrão de carga](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opções de carregamento com PolyBase

O PolyBase é uma tecnologia que acessa dados fora do banco de dados por meio da linguagem T-SQL. É a melhor forma de carregar dados no SQL Data Warehouse. Com o PolyBase, os dados carregam em paralelo a partir da fonte de dados diretamente nos nós de computação. 

Para carregar dados com o PolyBase, você pode usar qualquer uma dessas opções de carregamento.

- O [PolyBase com o T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bem quando os seus dados estiverem no armazenamento de Blobs do Azure ou no Azure Data Lake Store. Ele oferece mais controle sobre o processo de carregamento, mas também exige que você defina objetos de dados externos. Os outros métodos definem esses objetos em segundo plano, como mapear as tabelas de origem para as tabelas de destino.  Para coordenar as cargas de T-SQL, você pode usar o Azure Data Factory, SSIS ou as funções do Azure. 
- O [PolyBase com o SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md) funciona bem quando os seus dados de origem estiverem no SQL Server, no SQL Server local ou na nuvem. O SSIS define a origem para mapeamentos de tabela de destino e também coordena a carga. Se você já tiver pacotes SSIS, você pode modificar os pacotes para trabalhar com o novo destino do data warehouse. 
- O [PolyBase com o Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) é outra ferramenta de orquestração.  Ele define um pipeline e agenda de trabalhos. 

### <a name="polybase-external-file-formats"></a>Formatos de arquivos externos do PolyBase

O PolyBase carrega dados de arquivos de texto delimitados e codificados de UTF-8 e UTF-16. Além dos arquivos de texto delimitados, ele carrega os formatos de arquivo do Hadoop, o arquivo RC, ORC e Parquet. O PolyBase pode carregar dados de arquivos compactados Gzip e Snappy. Atualmente, o PolyBase não suporta ASCII estendido, formato de largura fixa, e formatos aninhados, como XML, JSON e WinZip.

### <a name="non-polybase-loading-options"></a>Opções de carregamento que não sejam PolyBase
Se os seus dados não forem compatíveis com o PolyBase, você pode usar [bcp](sql-data-warehouse-load-with-bcp.md) ou a [API do SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). O bcp carrega diretamente para o SQL Data Warehouse sem passar pelo armazenamento de Blobs do Azure e é destinado somente para pequenas cargas. Note que o desempenho do carregamento dessas opções é significativamente mais lento do que o PolyBase. 


## <a name="extract-source-data"></a>Extrair dados de origem

Obter dados de fora do seu sistema de origem depende da origem.  A meta é mover os dados para arquivos de texto delimitados. Se você estiver usando o SQL Server, você pode usar a [ferramenta de linha de comando bcp](/sql/tools/bcp-utility) para exportar os dados.  

## <a name="land-data-to-azure-storage"></a>Descarregar dados para o Armazenamento do Azure

Para descarregar dados para o armazenamento do Azure, você pode movê-los para o [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md). Em qualquer local, os dados devem ser armazenados em arquivos de texto. O Polybase pode carregar a partir desses locais.

Essas são as ferramentas e serviços que você pode usar para mover dados para o Armazenamento do Microsoft Azure.

- O serviço [Azure ExpressRoute](../expressroute/expressroute-introduction.md) melhora a taxa de transferência de rede, o desempenho e a previsibilidade. O ExpressRoute é um serviço que encaminha os dados por uma conexão privada dedicada para o Azure. As conexões do ExpressRoute não encaminham dados pela Internet pública. As conexões oferecem mais confiabilidade e velocidade, latências menores e maior segurança do que as conexões comuns pela Internet.
- O [Utilitário AZCopy](../storage/common/storage-use-azcopy.md) move os dados para o Armazenamento do Microsoft Azure pela internet pública. Isso funciona se os tamanhos dos seus dados forem inferiores a 10 TB. Para executar cargas regularmente com AZCopy, teste a velocidade da rede para ver se ela é aceitável. 
- O [Azure Data Factory (ADF)](../data-factory/introduction.md) tem um gateway que você pode instalar no seu servidor local. Em seguida, você pode criar um pipeline para mover os dados do seu servidor local para o Armazenamento do Microsoft Azure.

Para saber mais, consulte [Mover dados de e para o Armazenamento do Microsoft Azure](../storage/common/storage-moving-data.md)


## <a name="prepare-data"></a>Preparar dados

Você pode precisar preparar e limpar os dados na sua conta de armazenamento antes de carregá-los no SQL Data Warehouse. A preparação de dados pode ser executada enquanto seus dados estiverem na origem, conforme você exporta os dados para arquivos de texto ou após os dados no Armazenamento do Microsoft Azure.  É mais fácil trabalhar com os dados o mais precocemente possível no processo.  

### <a name="define-external-tables"></a>Definir tabelas externas
Antes que você possa carregar os seus dados, você precisa definir tabelas externas no seu data warehouse. O PolyBase usa tabelas externas para definir e acessar os dados no Armazenamento do Microsoft Azure. A tabela externa é semelhante a uma tabela normal. A principal diferença são os pontos de tabela externos para os dados armazenados fora do data warehouse. 

Definir tabelas externas envolve a especificação da fonte de dados, o formato dos arquivos de texto e as definições de tabela. Estes são os tópicos de sintaxe do T-SQL que você precisará:
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Para obter um exemplo de criação de objetos externos, consulte a etapa [Criar tabelas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) no tutorial de carregamento.

### <a name="format-text-files"></a>Arquivos de texto formatados

Depois que os objetos externos são definidos, você precisa alinhar as linhas dos arquivos de texto com a tabela externa e a definição de formato de arquivo. Os dados em cada linha do arquivo de texto devem se alinhar com a definição da tabela.

Para formatar os arquivos de texto:

- Se os seus dados forem provenientes de uma fonte não relacional, você precisa transformá-los em linhas e colunas. Se os dados forem de uma fonte relacional ou não, os dados devem ser transformados para se alinharem com as definições de coluna para a tabela na qual você planeja carregar os dados. 
- Formatar dados no arquivo de texto para se alinharem aos tipos de dados e colunas na tabela de destino do SQL Data Warehouse. O desalinhamento entre os tipos de dados nos arquivos de texto externos e a tabela do data warehouse faz com que as linhas a sejam rejeitadas durante o carregamento.
- Separar os campos no arquivo de texto com um terminador.  Certifique-se de usar um caractere ou uma sequência de caracteres que não sejam encontrados na fonte de dados. Use o terminador especificado com [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Carregar para uma tabela de preparo
Para levar os dados para o data warehouse, é uma boa solução carregar primeiro os dados em uma tabela de preparo. Usando uma tabela de preparo, você pode manipular erros sem interferir com as tabelas de produção, e você evita a execução de operações de reversão na tabela de produção. Uma tabela de preparo também oferece a oportunidade de usar o SQL Data Warehouse para executar transformações antes da inserção dos dados em tabelas de produção.

Para carregar com o T-SQL, execute a instrução de T-SQL [CRIAR TABELA COMO SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md). Este comando insere os resultados de uma instrução select em uma nova tabela. Quando a instrução seleciona a partir de uma tabela externa, ela importa dados externos. 

No exemplo a seguir, ext.Date é uma tabela externa. Todas as linhas são importadas para uma nova tabela denominada dbo.Date.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Transformar os dados
Enquanto os dados estão na tabela de preparo, execute as transformações que a sua carga de trabalho exige. Em seguida, mova os dados para uma tabela de produção.

## <a name="insert-data-into-production-table"></a>Insira os dados na tabela de produção

O INSERT INTO... A Instrução SELECT move os dados da tabela de preparo para a tabela permanente. 

Ao criar um processo de ETL, tente executar o processo em uma amostra de teste pequena. Tente extrair 1000 linhas da tabela para um arquivo, movê-lo para o Azure e, em seguida, tente carregá-lo em uma tabela de preparo. 

## <a name="partner-loading-solutions"></a>Soluções de carregamento de parceiros
Muitos de nossos parceiros têm soluções de carregamento. Para saber mais, consulte uma lista dos nossos [parceiros de solução](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Próximas etapas
Para orientações sobre carregamento, consulte as [Diretrizes para carregar dados](guidance-for-loading-data.md).


