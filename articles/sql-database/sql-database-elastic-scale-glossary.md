<properties 
    pageTitle="Glossário de ferramentas de Banco de Dados Elástico | Microsoft Azure" 
    description="Explicação dos termos usados para as ferramentas de banco de dados elástico" 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>

# Glossário de ferramentas do banco de dados elástico
Os termos a seguir são definidos para as [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-introduction.md), um recurso do Banco de Dados SQL do Azure. As ferramentas incluem a [biblioteca de cliente](sql-database-elastic-database-client-library.md), a [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md), [pools elásticos](sql-database-elastic-pool.md) e [consultas](sql-database-elastic-query-overview.md).

![Termos de escala elástica][1]

**Banco de dados**: um Banco de Dados SQL do Azure.

**Roteamento dependente de dados**: a funcionalidade que permite que um aplicativo se conecte a um fragmento dada uma chave de fragmentação específica. Compare com a **Consulta de vários fragmentos**.

**Mapa de fragmentos global**: o mapa entre chaves de fragmentação e seus respectivos fragmentos em um **conjunto de fragmentos**. O mapa de fragmentos global é armazenado no **gerenciador do mapa de fragmentos**. Compare com o **mapa de fragmentos local**.

**Mapa de fragmentos de lista**: um mapa de fragmentos no qual as chaves de fragmentação são mapeadas individualmente. Compare com o **Mapa de fragmentos de intervalo**.

**Mapa de fragmentos local**: armazenado em um fragmento, o mapa de fragmentos local contém mapeamentos para os shardlets que residem no fragmento.

**Consulta de vários fragmentos**: a capacidade de executar uma consulta em vários fragmentos; os conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecida como “consulta do tipo fan-out”). Compare com o **roteamento dependente de dados**.

**Mapa de fragmentos de intervalo**: um mapa de fragmentos em que a estratégia de distribuição de fragmentos é baseada em vários intervalos de valores contíguos.

**Tabelas de referência**: tabelas que não são fragmentadas, mas replicadas nos fragmentos. Por exemplo, códigos postais podem ser armazenados em uma tabela de referência.

**Fragmento**: um banco de dados SQL do Azure que armazena dados de um conjunto de dados fragmentados.

**Elasticidade de fragmento**: a capacidade de executar a **escala horizontal** e a **escala vertical**.

**Tabelas fragmentadas**: tabelas que são fragmentadas, ou seja, cujos dados são distribuídos por meio de fragmentos com base em seus valores de chave de fragmentação.

**Chave de fragmentação**: um valor de coluna que determina como os dados são distribuídos nos fragmentos. O tipo do valor pode ser um dos seguintes: **int**, **bigint**, **varbinary** ou **uniqueidentifier**.

**Conjunto de fragmentos**: a coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no gerenciador de mapa de fragmentos.

**Shardlet**: todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas.

**Mapa de fragmentos**: o conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador de mapa de fragmentos**: um objeto de gerenciamento e um repositório de dados que contém o(s) mapa(s) de fragmentos, locais de fragmentos e mapeamentos para um ou mais conjuntos de fragmentos.

![Mapeamentos][2]


##Verbos

**Escala horizontal**: o ato de escalar horizontal (ou verticalmente) uma coleção de fragmentos adicionando ou removendo fragmentos de um mapa de fragmentos, como mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Mesclar**: o ato de mover shardlets de dois fragmentos para um e atualizar o mapa de fragmentos de acordo.

**Mover shardlet**: o ato de mover um único shardlet para um fragmento diferente.

**Fragmentar**: o ato de particionar horizontalmente dados estruturados de modo idêntico em vários bancos de dados com base em uma chave de fragmentação.

**Dividir**: o ato de mover vários shardlets de um fragmento para outro (normalmente novo). Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Escala vertical**: o ato de escalar vertical (ou horizontalmente) o nível de desempenho de um fragmento individual. Por exemplo, alterar um fragmento de Standard para Premium (o que resulta em mais recursos de computação).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png
 

<!---HONumber=Nov15_HO2-->