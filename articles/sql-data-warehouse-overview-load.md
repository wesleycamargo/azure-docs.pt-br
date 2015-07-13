<properties
   pageTitle="Carregar dados no SQL Data Warehouse | Microsoft Azure"
   description="Aprenda sobre os cenários comuns para carregamento de dados no SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# Carregar dados no SQL Data Warehouse
O SQL Data Warehouse apresenta diversas opções de carregamento de dados para vários cenários. Por exemplo, talvez você queira carregar:

- lotes grandes uma vez por dia,
- lotes menores ao longo do dia ou
- atualizações simples em tabelas menores (dimensão)

As necessidades de cada cenário são tão variadas quanto os tipos de dados que você deseja carregar no SQL Data Warehouse. Neste artigo, listaremos algumas das opções para carregamento de dados.

## Tecnologias
O SQL Data Warehouse oferece suporte a estas ferramentas de carregamento de dados padrão:

- Fábrica de dados do Azure
- utilitários de linha de comando bcp
- PolyBase
- SQL Server Integration Services (SSIS)
- Ferramentas de carregamento de dados de terceiros

### Azure Data Factory (ADF)
O ADF é um serviço totalmente gerenciado para composição de serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificados, escalonáveis e confiáveis. O SQL Data Warehouse é uma [fonte/coletor com suporte][] para [atividade de cópia][] do ADF.

### utilitários de linha de comando bcp
O executável de linha de comando **bcp** é um utilitário da Microsoft que permite o carregamento e extração de dados do SQL Server, do Banco de Dados SQL e do SQL Data Warehouse. Para começar, siga o tutorial [Carregar dados com o bcp][].

### PolyBase
O PolyBase é uma tecnologia da Microsoft que simplifica a análise de dados fornecendo uma maneira de consultar o Hadoop e o armazenamento de blob do Armazenamento do Azure, todos com Transact-SQL padrão e sem usar o MapReduce. O PolyBase também pode carregar dados do armazenamento de blob do Azure no SQL Data Warehouse. Para começar, siga o tutorial [Carregar com o PolyBase][].

### SQL Server Integration Services (SSIS)
O [SSIS][] é uma plataforma para criação de soluções de integração e de transformação de dados em nível corporativo. Para criar pacotes que se conectam ao SQL Data Warehouse, use o [adaptador de destino padrão do SQL Server][].

### Ferramentas de terceiros
O SQL Data Warehouse oferece suporte a soluções líderes do setor para carregamento de dados. Para obter mais detalhes, confira nossa lista de [parceiros de solução][].

## Próximas etapas
Para obter mais dicas de desenvolvimento, confira a [visão geral sobre desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Carregar dados com o bcp]: sql-data-warehouse-load-with-bcp.md
[Carregar com o PolyBase]: sql-data-warehouse-load-with-polybase.md
[parceiros de solução]: sql-data-warehouse-solution-partners.md
[visão geral sobre desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[fonte/coletor com suporte]: https://msdn.microsoft.com/library/dn894007.aspx
[atividade de cópia]: https://msdn.microsoft.com/library/dn835035.aspx
[adaptador de destino padrão do SQL Server]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO1-->