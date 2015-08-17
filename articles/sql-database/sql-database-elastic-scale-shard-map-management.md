<properties 
	pageTitle="Gerenciamento de mapa de fragmentos" 
	description="Como usar o ShardMapManager, a biblioteca de cliente do banco de dados elástico" 
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
	ms.date="07/24/2015" 
	ms.author="sidneyh"/>

# Gerenciamento de mapa de fragmentos
Em um ambiente de banco de dados fragmentado, um **mapa de fragmentos** mantém informações que permitem que um aplicativo se conecte ao banco de dados correto com base no valor da **chave de fragmentação**. Ter noções básicas sobre como esses mapas são construídos é crucial para gerenciar fragmentos com a biblioteca de cliente do banco de dados elástico.

## Mapas de fragmentos e mapeamentos de fragmentos
 
### Tipos .Net com suporte para chaves de fragmentação

A Escala Elástica dá suporte aos seguintes tipos de .Net Framework como chaves de fragmentação:

* inteiro
* longo
* guid
* byte  
* datetime
* timespan
* datetimeoffset

### Mapas de fragmentos de lista e intervalo
Mapas de fragmentos podem ser construídos usando **listas de valores de chave de fragmentação individuais** ou usando **intervalos de valores de chave de fragmentação**.

###Mapas de fragmentos de lista
**Fragmentos** contêm **shardlets** e o mapeamento de shardlets para fragmentos é mantido por um mapa de fragmentos. Uma **lista de mapas de fragmento** é uma associação entre os valores de chave individuais, que identificam os shardlets e os bancos de dados que servem como fragmentos. **Mapeamentos de lista** são explícitos (ex.: a chave 1 é mapeada para o Banco de Dados A) e valores de chave diferentes podem ser mapeados para o mesmo banco de dados (valores de chave 3 e 6 ambos se referem ao Banco de Dados B).

| Chave | Local de fragmento |
|-----|----------------|
| 1 | Database\_A |
| 3 | Database\_B |
| 4 | Database\_C |
| 6 | Database\_B |
| ... | ... |
 

### Mapas de fragmentos de intervalo 
Em um **intervalo de mapas de fragmentos**, o intervalo de chaves é descrito por um par **[Valor baixo, Valor alto)**, em que o *Valor baixo* é a chave mínima no intervalo e *Valor alto* é o primeiro valor maior que o intervalo.

Por exemplo, **[0, 100)** inclui todos os números inteiros iguais ou maiores que 0 e menores que 100. Observe que vários intervalos podem apontar para o mesmo banco de dados e intervalos separados tem suporte (por exemplo, ambos [100,200) e [400,600) apontam para o banco de dados C no exemplo abaixo.)

| Chave | Local de fragmento |
|-----------|----------------|
| [1,50) | Database\_A |
| [50,100) | Database\_B |
| [100,200) | Database\_C |
| [400,600) | Database\_C |
| ... | ...            

Cada uma das tabelas mostradas acima é um exemplo conceitual de um objeto **ShardMap**. Cada linha é um exemplo simplificado de um objeto **RangeMapping** (para o mapa do fragmento de lista) ou **PointMapping individual** (para o mapa do fragmento de lista).

## Gerenciador de mapa de fragmentos 

Na biblioteca do cliente, o gerenciador de mapa de fragmentos é uma coleção de mapas de fragmentos. Os dados gerenciados por um objeto .NET **ShardMapManager** são mantidos em três locais:

1. **GSM (Mapa de Fragmentos Global)**: ao criar um **ShardMapManager**, você pode especificar um banco de dados para servir como repositório para todos os mapas de fragmento e mapeamentos. Procedimentos armazenados e tabelas especiais são criados automaticamente para gerenciar as informações. Isso geralmente é um banco de dados pequeno e pouco acessados, mas ele não deve ser usado para outras necessidades do aplicativo. As tabelas estão em um esquema especial chamado **\_\_ShardManagement**.

2. **LSM (Mapa de Fragmentos Local)**: cada banco de dados que você especificar para ser um fragmento dentro de um mapa de fragmentos será modificado para conter várias pequenas tabelas e procedimentos armazenados especiais que contêm e gerenciam informações do mapa de fragmentos específicas para esse fragmento. Essas informações são redundantes nas informações de GSM, mas permite que o aplicativo validar informações de mapa do fragmento em cache sem colocar nenhuma carga no GSM. O aplicativo usa o LSM para determinar se um mapeamento em cache ainda é válido. As tabelas correspondentes ao LSM em cada fragmento estão no esquema * * \_\_ShardManagement * *.

3. **Cache do aplicativo**: cada instância do aplicativo que acessa um objeto **ShardMapManager** mantém um cache na memória local dos seus mapeamentos. Ele armazena informações de roteamento que recentemente foi recuperadas.

## Construindo um ShardMapManager
Um objeto **ShardMapManager** no aplicativo é instanciado usando um padrão de fábrica. O método **ShardMapManagerFactory.GetSqlShardMapManager** usa credenciais (incluindo o nome do servidor e o nome do banco de dados contendo o GSM) na forma de uma **ConnectionString** e retorna uma instância de um **ShardMapManager**.

O**ShardMapManager**deve ser instanciado apenas uma vez por domínio de aplicativo dentro do código de inicialização para um aplicativo. O**ShardMapManager**pode conter qualquer número de mapas de fragmento. Enquanto um mapa do fragmento único pode ser suficiente para muitos aplicativos, há vezes quando conjuntos diferentes de bancos de dados são usados para esquemas diferentes ou para fins exclusivos e, nesses casos, vários mapas de fragmentação podem ser preferíveis.

Nesse código, um aplicativo tenta abrir um existente**ShardMapManager**. Se objetos que representam um **ShardMapManager**Global (GSM) ainda não existirem no banco de dados, a biblioteca do cliente os cria lá.

    // Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 

### Credenciais de administração de mapa de fragmentos

Normalmente, os aplicativos que administrar e manipulam mapas de fragmento são diferentes daqueles que utilizam os mapas de fragmento para conexões de rota.

Para aplicativos que administrem mapas de fragmento (adição ou alteração de fragmentos, mapas de fragmento, mapeamentos de fragmento, etc.) você deve instanciar o **ShardMapManager** usando **credenciais que tenham privilégios de leitura/gravação em banco de dados GSM e em cada banco de dados que sirva como um fragmento**. As credenciais devem permitir gravações nas tabelas no GSM e LSM como informações de mapa do fragmento são inseridas ou alteradas, bem como para criar tabelas LSM em novos fragmentos.

### Apenas os metadados afetados 

Métodos usados para preencher ou alterar o**ShardMapManager**dados não alteram os dados de usuário armazenados nos próprios fragmentos. Por exemplo, os métodos como**CreateShard**, **DeleteShard**, **UpdateMapping** etc, afetam apenas os metadados do mapa de fragmento. Não remova, adicione ou altere dados de usuário contidos nos fragmentos. Em vez disso, esses métodos destinam-se a ser usado em conjunto com operações separadas, que você pode executar para criar ou remover bancos de dados real ou que move linhas de um fragmento para outro para reequilibrar um ambiente fragmentado. (A ferramenta de **divisão/mesclagem** incluída com as ferramentas de banco de dados elástico faz uso dessas APIs além de orquestrar a movimentação de dados real entre fragmentos.)

## Popular um mapa de fragmentos: exemplo
 
Uma sequência de operações para preencher um mapa de fragmento específicos exemplo é mostrada abaixo. O código executa estas etapas:

1. Um novo mapa de fragmento é criado dentro de um Gerenciador de mapa do fragmento. 
2. Os metadados para dois fragmentos diferentes é adicionado ao mapa de fragmento. 
3. Uma variedade de mapeamentos de intervalo de chave são adicionados e o conteúdo geral do mapa do fragmento é exibido. 

O código é escrito de forma que todo o método pode ser novamente com segurança no caso de um erro inesperado – cada solicitação testa se um fragmento ou mapeando já existe, antes de tentar criá-lo. O código a seguir pressupõe que bancos de dados chamados **sample\_shard\_0**, **sample\_shard\_1** e **sample\_shard\_2** já tenham sido criados no servidor referido pela cadeia de caracteres **shardServer**.

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_0"), out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_1"), out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(0, 50), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(50, 100), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(100, 150), shard0, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                    (new Range<long>(150, 200), shard1, MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                   (new Range<long>(200, 300), shard0, MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                                    .OrderBy(s => s.Location.DataSource)
                                    .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
Como alternativa, você pode usar scripts do PowerShell para alcançar o mesmo resultado.

Depois de mapas de fragmento são preenchidos, aplicativos de acesso de dados podem ser criados ou adaptados para funcionar com os mapas. Não será necessário preencher ou manipular os mapas novamente até que o **layout do mapa** precise ser alterado.

## Roteamento dependente de dados 

Melhor uso do Gerenciador de mapa de fragmento virão os aplicativos que exigem conexões de banco de dados para executar as operações de dados específicos do aplicativo. Em um aplicativo fragmentado, essas conexões agora devem estar associadas com o banco de dados de destino correto. Isso é conhecido como **Roteamento dependente de dados**. Para esses aplicativos, instancie um objeto do Gerenciador do mapa de fragmento de fábrica usando as credenciais que têm acesso somente leitura no banco de dados GSM. As solicitações de conexões individuais posteriormente fornecerá credenciais necessárias para conectar-se ao banco de dados apropriado do fragmento.

Observe que esses aplicativos (usando **ShardMapManager** aberto com as credenciais somente leitura) não poderão alterar mapeamentos ou mapas. Para essas necessidades, crie aplicativos administrativos específicos ou scripts do PowerShell que fornecem credenciais de privilégios mais altos, conforme discutido anteriormente.

Para obter mais detalhes, consulte [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).

## Modificar um mapa de fragmentos 

Um mapa do fragmento pode ser alterado de diferentes maneiras. Todos os seguintes métodos de modificar os metadados que descrevem os fragmentos e seus mapeamentos, mas eles não modificam dados dentro de fragmentos fisicamente, nem que criar ou excluir os bancos de dados reais. Algumas das operações no mapa de fragmento descrito abaixo talvez precisem ser coordenada com ações administrativas que mover fisicamente os dados ou que adicionar e remover bancos de dados que serve como fragmentos.

Esses métodos funcionam juntos como blocos de construção disponíveis para modificar a distribuição global de dados em seu ambiente de banco de dados fragmentado.

* Para adicionar ou remover fragmentos: use **CreateShard** e **DeleteShard**. 
    
    O servidor e o banco de dados que representa o fragmento de destino já devem existir para executar essas operações. Esses métodos não têm qualquer impacto nos bancos de dados, apenas nos metadados no mapa do fragmento.

* Para criar ou remover pontos ou intervalos mapeados para os fragmentos: use **CreateRangeMapping**, **DeleteMapping**, **CreatePointMapping**.
    
    Vários pontos diferentes ou intervalos podem ser mapeados para o mesmo fragmento. Esses métodos afetam somente metadados – elas não afetam todos os dados que podem já estar presentes em fragmentos. Se houver necessidade de remover os dados do banco de dados para que sejam consistentes com as operações **DeleteMapping**, você precisará realizar essas operações separadamente, mas usando esses métodos.

* Para dividir intervalos existentes em dois ou mesclar intervalos adjacentes em um: use **SplitMapping** e **MergeMappings**.

    Observe que as operações de divisão/mesclagem **não alteram o fragmento para o qual os valores de chave são mapeados**. Uma divisão divide um intervalo existente em duas partes, mas deixa ambos como mapeada para o mesmo fragmento. Uma mesclagem opera em dois intervalos adjacentes que já são mapeados para o mesmo fragmento, juntando-os em um único intervalo. A movimentação de pontos ou intervalos em si entre fragmentos precisa ser coordenada usando **UpdateMapping** junto com a movimentação de dados real. Você pode usar o serviço de **Divisão/Mesclagem** que faz parte das ferramentas de banco de dados elástico para coordenar as alterações de mapa de fragmentos com a movimentação de dados, quando houver necessidade de movimentação.

* Para remapear (ou mover) pontos individuais ou intervalos de fragmentos diferentes: use **UpdateMapping**.

    Uma vez que pode haver necessidade de mover os dados de um fragmento para outro para que sejam consistentes com as operações de **UpdateMapping**, será preciso executar essa movimentação separadamente, mas usando esses métodos.

* Para realizar mapeamentos online e offline: use **MarkMappingOffline** e **MarkMappingOnline** para controlar o estado online de um mapeamento.

    Algumas operações nos mapeamentos de fragmentos são permitidas apenas quando um mapeamento está em um estado “offline”, incluindo **UpdateMapping** e **DeleteMapping**. Quando um mapeamento estiver offline, uma solicitação dependente de dados com base em uma chave incluída nesse mapeamento retornará um erro. Além disso, quando um intervalo primeiro fica offline, todas as conexões para o fragmento afetado serão eliminadas automaticamente para evitar resultados inconsistentes ou incompletos para consultas direcionadas com intervalos que está sendo alterados.

Mapeamentos são objetos imutáveis no .Net. Todos os métodos acima que alteram mapeamentos também invalidam todas as referências a eles em seu código. Para facilitar a execução de sequências de operações que alteram o estado do mapeamento, todos os métodos que alteram um mapeamento retornam uma nova referência de mapeamento, para que as operações possam ser encadeadas. Por exemplo, para excluir um mapeamento existente em sm shardmap que contém a chave 25, você pode executar o seguinte:

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## Adicionar um fragmento 

Geralmente, os aplicativos precisam simplesmente adicionar novos fragmentos para lidar com dados que são esperados de novas chaves ou intervalos de chaves para um mapa do fragmento que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário talvez tenha provisionar um novo fragmento para um novo locatário ou dados mensalmente fragmentados talvez precisem de um novo fragmento provisionado antes do início de cada novo mês.

Se o novo intervalo de valores de chave já não é parte de um mapeamento existente e nenhuma movimentação de dados é necessária, é muito simples adicionar o novo fragmento e associar a nova chave ou o intervalo para esse fragmento. Para obter detalhes sobre como adicionar novos fragmentos, consulte [Adicionar um novo fragmento](sql-database-elastic-scale-add-a-shard.md).

Para cenários que exigem a movimentação de dados, no entanto, a ferramenta de divisão/mesclagem é necessária para orquestrar a movimentação dos dados entre os fragmentos em conjunto com as atualizações necessárias do mapa de fragmentos. Para obter detalhes sobre como usar a ferramenta de divisão/mesclagem, consulte [Visão geral de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=August15_HO6-->