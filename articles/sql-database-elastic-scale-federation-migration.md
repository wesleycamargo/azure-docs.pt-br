<properties title="Federations Migration" pageTitle="Migração de federações" description="Outlines the steps to migrate an existing app built with Federations feature to Elastic Scale model." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

#Migração de federações 

O recurso Federações de Banco de Dados SQL do Azure está sendo desativado junto com as edições Web/Business em setembro de 2015. Nesse momento, os aplicativos que utilizam o recurso Federações vai parar de executar. Para garantir uma migração bem-sucedida, é altamente incentivado que os esforços de migração começam assim que possível a permitir p planejamento suficiente e a execução. Este documento fornece o contexto, exemplos e introdução ao Utilitário d emigração Federações que ilustra como migrar um aplicativo federações atual diretamente para as [APIs de visualização de ﻿Escala Elástica do Banco de Dados SQL do Azure](http://go.microsoft.com/?linkid=9862592) com êxito. O objetivo do documento é para você percorrer as etapas sugeridas para migrar um aplicativo Federações sem nenhuma movimentação de dados.

Há três etapas principais para migrar um aplicativo federações existente que usa as APIs de ﻿Escala Elástica.

1. [Criar Gerenciador do mapa do fragmento de uma raiz de Federação] 
2. [Modificar o aplicativo existente]
3. [Alternar membros da federação existente]
    

### A ferramenta de migração de exemplo
Para ajudar nesse processo, um [Utilitário de migração de Federações](http://go.microsoft.com/?linkid=9862613) foi criado. O utilitário realiza as etapas 1 e 3.

## <a name="create-shard-map-manager"></a>Criar Gerenciador do mapa do fragmento de uma raiz de Federação 
A primeira etapa da migração de um aplicativo Federações é clonar os metadados de uma raiz de federação para a construção de um Gerenciador de mapa do fragmento. 

![Clone the federation root to the shard map manager][1]
 
Comece com um aplicativo Federações existente em um ambiente de teste.
 
Use o **Utilitário de migração de Federações** para clonar os metadados da raiz da federação nas construções do [Gerenciador de mapa do fragmento](http://go.microsoft.com/?linkid=9862595) de ﻿Escala Elástica. Semelhante a uma raiz de federação, o banco de dados do Gerenciador de mapa do fragmento é um banco de dados autônomo que contém os mapeamentos de fragmento (ou seja, federações), referências a fragmentos (ou seja, membros da federação) e respectivos faixa mapeamentos. 

A clonagem de raiz da federação no Gerenciador de mapa do fragmento é uma cópia e conversão de metadados. Nenhum metadado é alterado na raiz da federação. Observe que a clonagem de raiz da federação com o Utilitário de migração de Federações é uma operação de point-in-time, e todas as alterações para a raiz da federação ou mapas de fragmento não serão refletidas no armazenamento de dados respectivo. Se forem feitas alterações à raiz da federação durante o teste das novas APIs, o Utilitário de migração de Federações pode ser usado para atualizar os mapeamentos de fragmento para representar o estado atual. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## Modificar o aplicativo existente 

Com o Gerenciador de mapa do fragmento no local e os membros da federação e intervalos registrados com o Gerenciador de mapa do fragmento (feito por meio do utilitário de migração), um pode modificar o aplicativo Federações existente para utilizar as APIs de ﻿Escala Elástica. Conforme mostrado na figura acima, as conexões de aplicativo por meio das APIs de ﻿Escala Elástica serão roteadas por meio do Gerenciador de mapa do fragmento para membros da federação apropriado (agora também um fragmento). Membros da federação de mapeamento para o Gerenciador de mapa do fragmento permite duas versões de um aplicativo - um que usa federações e outro que usa a ﻿Escala Elástica - o para ser executado lado a lado para verificar a funcionalidade.   

Durante a migração do aplicativo, haverá duas modificações de núcleo do aplicativo existente que precisam ser feitas.


#### Alterar 1: Instanciar um objeto do Gerenciador de mapa do fragmento: 

Ao contrário de federações, APIs de ﻿Escala Elástica interagem com o Gerenciador de mapa do fragmento por meio da classe **ShardMapManager**. A instanciação de um objeto de **ShardMapManager** e um mapa do fragmento pode ser feita da seguinte maneira:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Alterar 2: Conexões de rota para o fragmento adequado 

Com federações, uma conexão é estabelecida com um membro da federação específico com o comando USE FEDERATION, da seguinte maneira:  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Com as APIs de ﻿Escala Elástica, é estabelecer uma conexão com um fragmento específico por meio de [roteamento depende dos dados](./sql-database-elastic-scale-data-dependent-routing.md) com o método **OpenConnectionForKey** na classe **RangeShardMap**. 

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

As etapas nesta seção são necessárias, mas talvez não lidem com todos os cenários de migração que surgem. Para obter mais informações, consulte a visão geral [conceitual](./sql-database-elastic-scale-introduction.md) de ﻿Escala Elástica e a [referência de API](http://go.microsoft.com/?linkid=9862604).

## Alternar membros da federação existente 

![Switch out the federation members for the shards][3]

Depois que o aplicativo tiver sido modificado com a inclusão das APIs de ﻿Escala Elástica, a última etapa da migração de um aplicativo Federações é a operação de **SWITCH OUT** os membros da federação (para obter mais informações, consulte a referência do MSDN para [ALTER FEDERATION (banco de dados SQL do Azure](http://msdn.microsoft.com/pt-br/library/azure/hh597475.aspx)). O resultado final da emissão de uma operação de **SWITCH OUT** em relação a um membro de federação específico é a remoção de todas as restrições de federação e metadados, indicando o membro da federação como um Banco de Dados SQL do Azure regular, que não é diferente de qualquer outro Banco de Dados SQL do Azure. 

Observe que a emissão de uma operação de **SWITCH OUT** em relação a um membro da federação é uma operação unidirecional e não pode ser desfeita. Quando executada, o banco de dados resultante não pode ser adicionado novamente a uma federação e os comandos USE FEDERATION deixarão de funcionar para esse banco de dados. 

Para executar o switch, um argumento adicional foi adicionado ao comando ALTER FEDERATION em ordem para alternar o um membro da federação.  Enquanto o comando pode ser emitido para membros individuais da Federação, o Utilitário de migração Federações fornece a funcionalidade para iterar por meio de programação em cada membro da federação e realizar a operação de switch. 

Depois que a opção tiver sido executada em todos os membros da federação existente, é feita a migração do aplicativo.  
O Utilitário de migração de Federações fornece a capacidade de: 

1.    Executar um clone de raiz da federação a um Gerente de mapa do fragmento.  Você pode escolher colocar o Gerenciador de mapa do fragmento existente em um novo banco de dados SQL do Azure (recomendado) ou no banco de dados de raiz de federação existente.
2.    Execute a operação de SWITCH OUT em relação a todos os membros da federação em uma federação.


##Comparação de Recursos  
Embora a escala elástica oferece muitos recursos adicionais (por exemplo, [consultando vários fragmentos](./sql-database-elastic-scale-multishard-querying.md), [dividindo e mesclando fragmentos](./sql-database-elastic-scale-overview-split-and-merge.md), [elasticidade de fragmento](./sql-database-elastic-scale-elasticity.md), [cache no cliente](./sql-database-elastic-scale-shard-map-management.md) e muito mais), há alguns recursos Federations importantes para os quais não há suporte em ﻿Escala Elástica.
  

- O uso de **FILTERING=ON**. A Escala Elástica atualmente não dá suporte à filtragem em nível de linha. Uma atenuação é criar a lógica de filtragem na consulta emitida para o fragmento da seguinte maneira: 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

Produz o mesmo resultado:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- O recurso de **Divisão** de ﻿Escala Elástica não está totalmente online. Durante uma operação de divisão, cada shardlet individual fica offline durante a duração da mudança.
- O recurso de divisão de dimensionamento Elástico requer gerenciamento de provisionamento e o esquema de banco de dados manual.

## Considerações adicionais

* A Web e a Business Edition e as Federações estão sendo preteridas no segundo semestre de 2015. Como parte de migração de um aplicativo Federações, é altamente recomendável executar o teste de desempenho nas edições Básica, Padrão e Premium. 

* Executar a instrução SWITCH OUT em um membro da federação permite que o banco de dados resultante aproveitar todos os recursos de banco de dados SQL do Azure (ex.: novas edições, backup, PITR, auditoria, etc.) 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[Criar Gerenciador do mapa do fragmento de uma raiz de Federação ]:#create-shard-map-manager
[Modificar o aplicativo existente]:#Modify-the-Existing-Application
[Alternar membros da federação existente]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
