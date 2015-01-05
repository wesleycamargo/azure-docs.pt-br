<properties title="Azure Elastic Scale Glossary" pageTitle="Glossário de Escala Elástica do Azure" description="Explicação de termos usados para o recurso de Escala elástica do Banco de dados SQL do Azure" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Glossário de Escala Elástica
Os termos a seguir são definidos para o recurso de Escala Elástica do Banco de Dados SQL do Azure.

![Elastic Scale terms][1]

**Banco de dados**: Um Banco de Dados SQL do Azure. 

**Roteamento dependente de dados**: A funcionalidade que permite a um aplicativo conectar-se a um fragmento dada uma chave de fragmentação específica.Compare com **Consulta de multifragmento**.

**Mapa de fragmento global**: O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos dentro de um **conjunto de fragmentos**. O GSM é armazenado no **gerenciador de mapa de fragmentos**. Compare com **mapa de fragmentos local**.

**Mapa de fragmentos da lista**: Uma mapa de fragmentos em que as chaves de fragmentação são mapeadas individualmente.Compare com **Mapa de fragmentos do intervalo**.   

**Mapa de fragmentos local**:Armazenado em um fragmento, o mapa de fragmentos local contém mapeamentos para os shardlets que residem no fragmento.


**Consulta de multifragmento**: A habilidade de emitir uma consulta com relação a vários fragmentos; os conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecida como "consulta do tipo fan-out").Compare com **Roteamento dependente de dados**.

**Mapa de fragmentos do intervalo**: Um mapa de fragmentos em que a estratégia de distribuição de fragmentos é baseada em vários intervalos de valores contíguos. 


**Tabelas de referência**: Tabelas que não são fragmentadas, mas que são replicadas através de fragmentos. 

**Fragmento**: Um banco de dados SQL do Azure que armazena dados de um conjunto de dados fragmentados. 

**Elasticidade do fragmento** (SE): A habilidade de realizar **colocação em escala horizontal** e **colocação em escala vertical**.

**Tabelas fragmentadas**: Tabelas que são fragmentadas, ou seja, cujos dados são distribuídos através de fragmentos com base nos seus valores de chave de fragmentação. 

**Chave de fragmentação**: Um valor de coluna que determina como os dados são distribuídos através de fragmentos. O tipo do valor pode ser um dos seguintes: int, bigint, varbinary ou uniqueidentifier. 

**Conjunto de fragmentos**:  A coleção de fragmentos que são atribuídos a um mesmo mapa de fragmentos no gerenciador de mapa de fragmentos.  

**Shardlet**: Todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas. 

**Mapa de fragmentos**:O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador do mapa de fragmentos**:Um objeto de gerenciamento e um armazenamento de dados que contém os mapas de fragmentos, locais de fragmentos e mapeamentos para um ou mais conjuntos de fragmentos.

![Mappings][2]


##Verbos

**Colocação em escala horizontal**:O ato de ampliar (ou reduzir) uma coleção de fragmentos adicionando ou removendo fragmentos de um mapa de fragmentos.

**Mesclar**:O ato de mover shardlets de dois fragmentos para um e atualizar o mapa de fragmentos de acordo.

**Movimentação de shardlet**: O ato de mover um único shardlet para um fragmento diferente. 

**Fragmento**:O ato de particionar horizontalmente dados estruturados de modo idêntico através de vários bancos de dados em uma chave de fragmentação.

**Divisão**: O ato de mover vários shardlets de um fragmento para outro (normalmente novo).Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Colocação em escala vertical**: O ato de ampliar (ou reduzir) o nível de desempenho de um fragmento individual. Por exemplo, alterar um fragmento de Standard para Premium (conforme o necessário por questões de desempenho). 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png



<!--HONumber=35.1-->
