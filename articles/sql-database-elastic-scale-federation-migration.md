<properties title="Federations Migration" pageTitle="Migração de Federações" description="Descreve as etapas para migrar um aplicativo existente compilado com o recurso Federações para o modelo de Escala Elástica." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/30/2014" ms.author="sidneyh" />

#Migração de federações 

O recurso de Federações do Banco de dados SQL do Azure será descontinuado junto com as edições Web/Business em setembro de 2015. Então, os aplicativos que utilizem o recurso Federações pararão de executar. Para garantir uma migração bem-sucedida, é altamente incentivado que os esforços de migração comecem assim que possível para permitir planejamento e execução suficientes. Este documento fornece o contexto, os exemplos e uma introdução ao Utilitário de Migração de Federações que ilustra como migrar com sucesso um aplicativo de Federações atual sem interrupção para as [APIs de visualização da Escala Elástica do Banco de dados SQL do Azure](http://go.microsoft.com/?linkid=9862592). O objetivo do documento é apresentar as etapas sugeridas para migrar um aplicativo de Federações sem qualquer movimentação de dados.

Há três etapas principais para migrar um aplicativo de Federações existente para um que use APIs da Escala Elástica.

1. [Criar Gerenciador de mapa de fragmento por meio de uma raiz da federação] 
2. [Modificar o aplicativo existente]
3. [Desativar membros da federação existentes]
    

### Exemplo de ferramenta migração
Para auxiliar nesse processo, um [Utilitário de migração de federações](http://go.microsoft.com/?linkid=9862613) foi criado. O utilitário realiza as etapas 1 e 3. 

## <a name="create-shard-map-manager"></a>Criar Gerenciador de mapa de fragmento por meio de uma raiz da federação
A primeira etapa para migrar um aplicativo de Federações é clonar os metadados de uma raiz de federação para as construções de um gerenciador de mapa de fragmento. 

![Clone the federation root to the shard map manager][1]
 
Comece com um aplicativo de Federações existente em um ambiente de teste.
 
Use o **Utilitário de migração de federações** para clonar os metadados raiz da federação para as construções do [Gerenciador de mapa de fragmento] da Escala Elástica(http://go.microsoft.com/?linkid=9862595). De maneira análoga à raiz da federação, o banco de dados do Gerenciador do Mapa de Fragmentos é um banco de dados independente que contém os mapas de fragmentos (ou seja, federações), referências a fragmentos (ou seja, membros da federação) e os respectivos mapeamentos de intervalo. 

A clonagem da raiz da federação para o Gerenciador do mapa de fragmentos é uma cópia e uma tradução dos metadados. Nenhum metadado é alterado na raiz da federação. Observe que a clonagem da raiz de federação com o Utilitário de Migração de Federações é uma operação de ponto no tempo, e quaisquer alterações à raiz da federação ou aos mapas de fragmentos não se refletirão no outro armazenamento de dados respectivo. Se forem feitas alterações à raiz de federação durante o teste das novas APIs, o Utilitário de Migração de Federações pode ser usado para atualizar os mapas de fragmentos para representar o estado atual. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## Modificar o aplicativo existente 

Com o Gerenciador do Mapa de Fragmentos estabelecido e os membros da federação e intervalos registrados com o Gerenciador do Mapa de Fragmentos (feito por meio do utilitário de migração), é possível modificar o aplicativo de Federações existente para utilizar as APIs de Escala Elástica. Como mostra a figura acima, as conexões do aplicativo por meio das APIs de Escala Elástica serão feitas através do Gerenciador do Mapa de Fragmentos para os membros adequados da federação (agora também um fragmento). Mapear os membros da federação para o Gerenciador do Mapa de Fragmentos permite que duas versões de um aplicativo - uma que usa Federações e outra que usa Escala Elástica - sejam executadas lado a lado para verificar a funcionalidade.   

Durante a migração do aplicativo, haverá duas modificações centrais ao aplicativo existente que precisarão ser feitas.


#### Alteração 1: Instanciar um objeto de Gerenciador do mapa de fragmentos: 

Diferente de Federações, as APIs de Escala Elástica interagem com o Gerenciador de mapa de fragmentos por meio da classe **ShardMapManager**. A instanciação de um objeto **ShardMapManager** e um mapa de fragmentos pode ser feita da seguinte maneira:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Alteração 2: Rotear conexões para o fragmento adequado 

Com federações, uma conexão é estabelecida com um membro específico da federação com o comando USE FEDERATION da seguinte maneira:  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Com APIs da Escala Elástica, é estabelecida uma conexão com um fragmento em particular por meio de um [roteamento dependente de dados](./sql-database-elastic-scale-data-dependent-routing.md) com o método **OpenConnectionForKey** na classe **RangeShardMap**. 

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

As etapas nesta seção são necessárias, mas podem não abordar todos os cenários de migração que surgirem. Para mais informações, consulte a [visão geral conceitual da Escala Elástica](./sql-database-elastic-scale-introduction.md) e a [referência de API](http://go.microsoft.com/?linkid=9862604).

## Desativar membros da federação existentes 

![Switch out the federation members for the shards][3]

Quando o aplicativo tiver sido modificado com a inclusão de APIs da Escala Elástica, a última etapa na migração de um aplicativo de Federações é **SWITCH OUT** os membros da federação (para mais informações, consulte a referência do MSDN para [ALTER FEDERATION (Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx)). O resultado final de emitir um **SWITCH OUT** com relação a um membro da federação em particular é a remoção de todas as restrições da federação e metadados renderizando o membro da federação como um Banco de Dados SQL do Azure normal, não diferente de qualquer outro Banco de Dados SQL do Azure.  

Observe que emitir **SWITCH OUT** com relação a um membro da federação é uma operação unidirecional e não pode ser desfeita. Depois de realizadas, o banco de dados resultante não pode ser adicionado de volta a uma federação, e os comandos USE FEDERATION não funcionarão mais para esse banco de dados. 

Para realizar a comutação, um argumento extra foi adicionado ao comando ALTER FEDERATION para o SWITCH OUT de um membro da federação.  Embora o comando possa ser emitido com relação a membros individuais da Federação, o Utilitário de Migração de Federações fornece funcionalidade para iterar programaticamente através de cada membro da federação e realizar a operação de comutação. 

Quando a comutação tiver sido realizada em todos os membros da federação existentes, a migração do aplicativo está concluída.  
O Utilitário de Migração de Federações fornece as habilidades para: 

1.    Realizar uma clonagem da raiz da federação para um Gerenciador de mapa de fragmentos.  É possível escolher colocar o Gerenciador do mapa de fragmentos existente em um novo banco de dados SQL do Azure (recomendado) ou no banco de dados raiz da federação existente.
2.    Emita SWITCH OUT com relação a todos os membros da federação em uma federação.


##Comparação de recursos  
Embora a Escala Elástica ofereça muitos recursos adicionais (por exemplo, [consulta de multifragmento](./sql-database-elastic-scale-multishard-querying.md), [dividir e mesclar fragmentos](./sql-database-elastic-scale-overview-split-and-merge.md), [elasticidade de fragmentos](./sql-database-elastic-scale-elasticity.md), [cache no cliente](./sql-database-elastic-scale-shard-map-management.md)e mais), há alguns recursos de Federações dignos de nota que não têm mais suporte na Escala Elástica.
  

- O uso de **FILTERING=ON**. A escala elástica não tem suporte para filtragem em nível de coluna no momento. Uma mitigação é integrar a lógica de filtragem na consulta emitida com relação ao fragmento da seguinte maneira: 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

Produz o mesmo resultado que:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- O recurso de **Divisão** da Escala elástica não está totalmente online.Durante uma operação de divisão, cada shardlet individual é colocado offline durante a movimentação.
- O recurso de divisão da Escala elástica requer operações manuais de provisionamento do banco de dados e gerenciamento de esquema.

## Considerações adicionais

* Tanto a Web e Business Edition quanto Federações estão sendo preteridas no segundo trimestre de 2015.  Como parte da migração de um aplicativo de Federações, é altamente recomendado realizar um teste de desempenho nas edições Basic, Standard e Premium. 

* Realizar a instrução SWITCH OUT em um membro da federação possibilita ao banco de dados resultante a aproveitar todos os recursos do banco de dados SQL do Azure (ou seja, novas edições, backup, PITR, auditoria, etc.) 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[Criar Gerenciador de mapa de fragmento por meio de uma raiz da federação]:#create-shard-map-manager
[Modificar o aplicativo existente]:#Modify-the-Existing-Application
[Desativar membros da federação existentes]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png

<!--HONumber=35.1-->
