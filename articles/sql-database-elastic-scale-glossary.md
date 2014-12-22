<properties title="Azure Elastic Scale Glossary" pageTitle="Glossário de Escala Elástica do Azure" description="Explanation of terms used for Elastic Scale feature of Azure SQL Database" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Glossário de Escala Elástica
Os seguintes termos são definidos para o recurso de Escala Elástica do Banco de Dados SQL do Azure.

![Elastic Scale terms][1]

**Banco de dados**: Um banco de dados SQL do Azure. 

**Roteamento dependente de dados**: A funcionalidade que permite que um aplicativo se conecte a um fragmento de acordo com uma chave específica de fragmentação. Compare a **Consulta de vários fragmentos**.

**Mapa do fragmento global**: O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos em um **conjunto de fragmentos**. O GSM é armazenado no **gerenciador de mapa do fragmento**. Compare com **o mapa do fragmento local**.

**Mapa de fragmento de lista**: Um mapa de fragmento no qual chaves de fragmentação são mapeadas individualmente. Compare com o **Mapa do fragmento de intervalo**.   

**Mapa do fragmento local**: Armazenado em um fragmento, o mapa do fragmento local contém mapeamentos para o shardlets que residem no fragmento.

**Consulta de vários fragmentos**: A capacidade de emitir uma consulta em vários fragmentos; conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecido como "consulta do tipo fan-out"). Compare com **Roteamento dependente de dados**.

**Mapa de fragmento de intervalo**: Um mapa de fragmentos em que a estratégia de distribuição de fragmentação se baseia em vários intervalos de valores contíguos. 

**Tabelas de referência**: Tabelas não fragmentadas, mas replicadas em fragmentos. 

**Fragmento**: Um banco de dados SQL do Azure que armazena dados de um conjunto de dados fragmentado. 

**Elasticidade de fragmento** (SE): A capacidade de realizar tanto a **escala horizontal** quanto a **vertical**.

**Tabelas fragmentadas**: Tabelas que estão fragmentados, ou seja, cujos dados são distribuídos em fragmentos com base nos valores de chave de fragmentação. 

**Chave de fragmentação**: Um valor de coluna que determina como os dados são distribuídos em fragmentos. O tipo de valor pode ser uma das seguintes opções: int, bigint, varbinary ou uniqueidentifier. 

**Conjunto de fragmentos**: A coleção de fragmentos atribuídos ao mesmo mapa de fragmentos no gerenciador de mapa do fragmento.  

**Shardlet**: Todos os dados associados com um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas. 

**Mapa do fragmento**: O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador de mapa de fragmentos**: Um objeto e dados de repositório de gerenciamento que contém o mapa(s) do fragmento, locais de fragmento e mapeamentos para um ou mais conjuntos de fragmentos.


##Verbos

**Escala horizontal**: O ato de escalar (ou reduzir) horizontalmente conjunto de fragmentos, adicionando ou removendo fragmentos para um mapa do fragmento.

**Mesclagem**: O ato de mover shardlets de dois fragmentos para um fragmento e atualizar o mapa do fragmento adequadamente.

**Movimentação do shardlet**: O ato de mover um único shardlet para um fragmento diferente. 

**Fragmento**: O ato de particionamento horizontal de dados estruturados de forma idêntica em vários bancos de dados com base em uma chave de fragmentação.

**Divisão**: O ato de mover várias shardlets de um fragmento para outro (normalmente novo) fragmento. Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Escala vertical**: O ato de escalar (ou reduzir) verticalmente o nível de desempenho de um fragmento, individualmente. Por exemplo, alterar um fragmento do padrão para Premium (conforme exigido por motivos de desempenho). 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
