<properties 
    pageTitle="Migração de federações" 
    description="Descreve as etapas para migrar um aplicativo existente criado com o recurso de Federações para o modelo de banco de dados elástico." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/14/2015" 
    ms.author="sidneyh"/>

# Migração de federações 

O recurso de Federações do Banco de dados SQL do Azure será descontinuado junto com as edições Web/Business em setembro de 2015. Então, os aplicativos que utilizem o recurso Federações pararão de executar. Para garantir uma migração bem-sucedida, é altamente incentivado que os esforços de migração comecem assim que possível para permitir planejamento e execução suficientes.

Se seu aplicativo ainda não estiver pronto para funcionar sem federações, contate o Suporte da Microsoft usando as instruções mencionadas [aqui](https://support.microsoft.com/kb/3087180).

Este documento fornece o contexto, exemplos e introdução ao Utilitário de emigração Federações que ilustra como migrar um aplicativo federações atual diretamente para as APIs da [Biblioteca de cliente do banco de dados elástico](http://go.microsoft.com/?linkid=9862592) para fragmentação. O objetivo do documento é apresentar as etapas sugeridas para migrar um aplicativo de Federações sem qualquer movimentação de dados.

Há três etapas principais para migrar um aplicativo de Federações existente para um que use ferramentas de banco de dados elástico.

1. [Criar Gerenciador de mapa de fragmento por meio de uma raiz da federação](#create-a-shard-map-manager-from-a-federation-root) 
2. [Modificar o aplicativo existente](#modify-the-existing-application)
3. [Desativar membros da federação existentes](#switch-out-existing-federation-members)
    

### Exemplo de ferramenta migração
Para ajudar nesse processo, um [Utilitário de migração de Federações](http://go.microsoft.com/?linkid=9862613) foi criado. O utilitário realiza as etapas 1 e 3.

## Criar Gerenciador de mapa de fragmento por meio de uma Raiz da Federação
A primeira etapa para migrar um aplicativo de Federações é clonar os metadados de uma raiz de federação para as construções de um gerenciador de mapa de fragmento.

![Clone a raiz da federação para o gerenciador do mapa de fragmentos][1]
 
Comece com um aplicativo de Federações existente em um ambiente de teste.
 
Use o **Utilitário de Migração de Federações** para clonar os metadados da raiz da federação nas construções do [Gerenciador de mapa do fragmento](http://go.microsoft.com/?linkid=9862595) da biblioteca do banco de dados elástico do cliente. Semelhante a uma raiz de federação, o banco de dados do Gerenciador de mapa do fragmento é um banco de dados autônomo que contém os mapeamentos de fragmento (ou seja, federações), referências a fragmentos (ou seja, membros da federação) e respectivos faixa mapeamentos.

A clonagem da raiz da federação para o Gerenciador do mapa de fragmentos é uma cópia e uma tradução dos metadados. Nenhum metadado é alterado na raiz da federação. Observe que a clonagem de raiz da federação com o Utilitário de migração de Federações é uma operação de point-in-time, e todas as alterações para a raiz da federação ou mapas de fragmento não serão refletidas no armazenamento de dados respectivo. Se forem feitas alterações à raiz de federação durante o teste das novas APIs, o Utilitário de Migração de Federações pode ser usado para atualizar os mapas de fragmentos para representar o estado atual.

![Migre o aplicativo existente para usar as APIs das ferramentas de banco de dados elástico][2]

## Modificar o aplicativo existente 

Com o Gerenciador do Mapa de Fragmentos estabelecido e os membros da federação e intervalos registrados com o Gerenciador do Mapa de Fragmentos (feito por meio do utilitário de migração), é possível modificar o aplicativo de Federações existente para utilizar a biblioteca de cliente do banco de dado elástico. Como mostra a figura acima, as conexões do aplicativo por meio das APIs serão feitas através do Gerenciador do Mapa de Fragmentos para os membros adequados da federação (agora também um fragmento). Mapear os membros da federação para o Gerenciador do Mapa de Fragmentos permite que duas versões de um aplicativo - uma que usa Federações e outra que usa a biblioteca de cliente do banco de dados elástico - sejam executadas lado a lado para verificar a funcionalidade.

Durante a migração do aplicativo, haverá duas modificações centrais ao aplicativo existente que precisarão ser feitas.


#### Alteração 1: instanciar um objeto de Gerenciador do mapa de fragmentos: 

Diferente de Federações, as APIs de ferramentas de banco de dados elástico interagem com o Gerenciador de mapa de fragmentos por meio da classe **ShardMapManager**. A instanciação de um objeto **ShardMapManager** e um mapa de fragmentos pode ser feita da seguinte maneira:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Alteração 2: rotear conexões para o fragmento adequado 

Com federações, uma conexão é estabelecida com um membro específico da federação com o comando USE FEDERATION da seguinte maneira:

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Com as APIs de ferramentas de bancos de dados elástico, é estabelecida uma conexão com um fragmento específico por meio de [roteamento depende dos dados](sql-database-elastic-scale-data-dependent-routing.md) com o método **OpenConnectionForKey** na classe **RangeShardMap**.

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

As etapas nesta seção são necessárias, mas podem não abordar todos os cenários de migração que surgirem. Para obter mais informações, consulte a [visão geral conceitual de ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md) e a [referência de API](http://go.microsoft.com/?linkid=9862604).

## Desativar membros da federação existentes 

![Desative os membros da federação de fragmentos][3]

Quando o aplicativo tiver sido modificado com a inclusão de APIs da ferramenta de banco de dados elástico, a última etapa na migração de um aplicativo de Federações é **SWITCH OUT** os membros da federação (para mais informações, consulte a referência do MSDN para [ALTER FEDERATION (Banco de Dados SQL do Azure]http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx)). O resultado final de emitir um **SWITCH OUT** com relação a um membro da federação em particular é a remoção de todas as restrições da federação e metadados renderizando o membro da federação como um Banco de Dados SQL do Azure normal, não diferente de qualquer outro Banco de Dados SQL do Azure.

Observe que a emissão de uma operação de **SWITCH OUT** em relação a um membro da federação é uma operação unidirecional e não pode ser desfeita. Depois de realizadas, o banco de dados resultante não pode ser adicionado de volta a uma federação, e os comandos USE FEDERATION não funcionarão mais para esse banco de dados.

Para realizar a comutação, um argumento extra foi adicionado ao comando ALTER FEDERATION para o SWITCH OUT de um membro da federação. Embora o comando possa ser emitido com relação a membros individuais da Federação, o Utilitário de Migração de Federações fornece funcionalidade para iterar programaticamente através de cada membro da federação e realizar a operação de comutação.

Quando a comutação tiver sido realizada em todos os membros da federação existentes, a migração do aplicativo está concluída. Quando todos os membros da federação forem desativados, é recomendável executar uma operação DROP FEDERATION na raiz. Isso não afetará nenhum dos membros anteriores, mas permitirá que o banco de dados raiz migre facilmente das Edições Web e Business.
  
O Utilitário de Migração de Federações fornece as habilidades para:

1.    Realizar uma clonagem da raiz da federação para um Gerenciador de mapa de fragmentos. É possível escolher colocar o Gerenciador do mapa de fragmentos existente em um novo banco de dados SQL do Azure (recomendado) ou no banco de dados raiz da federação existente.
2.    Emita SWITCH OUT com relação a todos os membros da federação em uma federação.


## Comparação de recursos

Embora as ferramentas de banco de dados elástico ofereçam muitos recursos adicionais (por exemplo, [consultando vários fragmentos](sql-database-elastic-scale-multishard-querying.md), [dividindo e mesclando fragmentos](sql-database-elastic-scale-overview-split-and-merge.md),[ elasticidade de fragmento](sql-database-elastic-scale-elasticity.md), [cache no cliente](sql-database-elastic-scale-shard-map-management.md) e muito mais), há alguns recursos Federations importantes para os quais não há suporte em ferramentas de banco de dados elástico.
  
- O uso de **FILTERING=ON**. Em vez disso, é recomendável que você use segurança em nível de linha (RLS) para filtragem de linha. Assim como a filtragem em federações, a RLS adiciona automaticamente um predicado para todas as consultas em uma tabela fragmentada. Para obter detalhes, consulte [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). 
 
 Como alternativa, você pode criar a lógica de filtragem na consulta emitida para o fragmento. Por exemplo:

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 Produz o mesmo resultado que:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- As ferramentas de banco de dados elástico, o recurso **Divisão** não está totalmente online. Durante uma operação de divisão, cada shardlet individual é colocado offline durante a movimentação.
- O recurso de divisão da requer operações manuais de provisionamento do banco de dados e gerenciamento de esquema.

## Considerações adicionais

* Tanto as edições Web e Business Edition quanto Federações estão sendo substituídas no segundo trimestre de 2015. Como parte da migração de um aplicativo de Federações, é altamente recomendável realizar um teste de desempenho nas edições Basic, Standard e Premium. 

* Realizar a instrução SWITCH OUT em um membro da federação possibilita ao banco de dados resultante aproveitar todos os recursos do banco de dados SQL do Azure (ou seja, novas edições, backup, PITR, auditoria, etc.)

##Precisa de mais tempo para a migração? 
Se seu aplicativo ainda não estiver pronto para funcionar sem federações, contate o Suporte da Microsoft usando as instruções mencionadas [aqui](https://support.microsoft.com/kb/3087180).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!---HONumber=Oct15_HO3-->