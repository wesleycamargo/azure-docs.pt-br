---
title: Notas de versão do Azure SQL Data Warehouse julho de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 123198b21122a23d81794db0a5ca2051b15ee2e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476112"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Julho de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidos em julho de 2018.

## <a name="lightning-fast-query-performance"></a>Desempenho de consulta rápida
[O Azure SQL Data Warehouse](https://aka.ms/sqldw) define novos padrões de desempenho com a introdução do Instant Data Access, que melhora as operações de reprodução aleatória. O Acesso Instantâneo a Dados reduz a sobrecarga para operações de movimentação de dados usando operações de dados nativas diretas do SQL Server para o SQL Server. A integração com o mecanismo do SQL Server diretamente para movimentação de dados significa que o SQL Data Warehouse é **67% mais rápido do que o Amazon Redshift** usando uma carga de trabalho derivada do reconhecido padrão da indústria [TPC Benchmark ™ H (TPC -H)](http://www.tpc.org/tpch/).

![O SQL Data Warehouse do Azure é mais rápido e mais barato do que o Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>Fonte: [Relatório de analistas de pesquisa da Gigaom: Data Warehouse no parâmetro de comparação de nuvem](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Além do desempenho em tempo de execução, o relatório [Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) também mediu a relação preço-desempenho para quantificar o custo em USD de cargas de trabalho específicas. O SQL Data Warehouse era **pelo menos 23% mais barato** do que o Redshift para cargas de trabalho de 30TB. Com a capacidade do SQL Data Warehouse de dimensionar a computação de forma elástica, bem como pausar e retomar cargas de trabalho, os clientes pagam apenas quando o serviço está em uso, diminuindo ainda mais os custos.
![O SQL Data Warehouse do Azure é mais rápido e mais barato do que o Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>Fonte: [Relatório de analistas de pesquisa da Gigaom: Data Warehouse no parâmetro de comparação de nuvem](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Simultaneidade de consulta
O SQL Data Warehouse também garante que os dados estejam acessíveis em todas as suas organizações. A Microsoft aprimorou o serviço para oferecer suporte a 128 consultas simultâneas para que mais usuários possam consultar o mesmo banco de dados e não serem bloqueados por outras solicitações. Em comparação, o Amazon Redshift restringe o máximo de consultas simultâneas a 50, limitando o acesso a dados dentro da organização.

O SQL Data Warehouse fornece esses ganhos de concorrência de desempenho e consulta de consulta sem qualquer aumento de preço e com base em sua arquitetura exclusiva com armazenamento e computação desacoplados.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Granularidade mais refinada para restaurações entre regiões e servidores
Agora, é possível restaurar regiões e servidores usando qualquer ponto de restauração, em vez de selecionar backups com redundância geográfica feitos a cada 24 horas. A restauração de servidor e entre regiões tem suporte para ambos os pontos de restauração automática ou definidos pelo usuário, permitindo granularidade mais refinada para proteção de dados adicional. Com mais pontos de restauração disponíveis, é possível certificar-se de que o data warehouse será logicamente consistente ao restaurar entre regiões.

![Restaurar Comando - SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Opções de Restauração - SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Para obter mais informações, consulte a postagem no blog [Pontos de restauração acelerados e flexíveis](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/).

## <a name="20-minute-restorations"></a>Restaurações de 20 minutos
O SQL Data Warehouse agora oferece restauração de qualquer data warehouse **em menos de 20 minutos** dentro da mesma região, independentemente do tamanho do banco de dados. O tempo de restauração será aplicável, se a restauração for para o mesmo servidor lógico ou para um diferente na mesma região. Além disso, o processo de instantâneo foi aprimorado para reduzir o tempo necessário para criar um ponto de restauração. Em níveis de desempenho inferiores (configurações de DWU mais baixas), a melhoria é uma *redução de 2x* no tempo para criação de instantâneos.

Para obter mais informações, consulte a postagem no blog [Pontos de restauração acelerados e flexíveis](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/).

## <a name="custom-restoration-configurations"></a>Configurações de restauração personalizadas
Agora, é possível alterar o nível de desempenho (DWU) ao restaurar no portal do Azure. Adicionalmente, você pode restaurar para um data warehouse Gen2 atualizado. Ao restaurar para uma instância Gen 2, será possível avaliar o impacto da Gen2 antes de [atualizar o data warehouse Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configuração de restauração personalizada - SQL Data Warehouse do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
O procedimento armazenado [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) geralmente é usado por ferramentas para obter metadados sobre parâmetros no lote Transact-SQL. O procedimento retorna uma linha para cada parâmetro no lote com as informações de tipo deduzidas para esse parâmetro. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

O conjunto de resultados inclui metadados sobre o parâmetro `@id` (resultados parciais mostrados)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
O procedimento armazenado [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) atualiza os metadados para um objeto de banco de dados se os metadados subjacentes estiverem desatualizados devido a alterações dos objetos subjacentes. Isso pode ocorrer se as tabelas base de uma exibição forem alteradas e a visualização não tiver sido recriada. Isso economiza a etapa de eliminar e recriar objetos dependentes.

O exemplo abaixo mostra uma visão que se torna obsoleta devido à alteração da tabela subjacente. Você notará que os dados estão corretos para a primeira alteração de coluna (1 para Mollie), mas o nome da coluna é inválido e a segunda coluna não está presente. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco do SQL Data Warehouse, aprenda como [criar um SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
