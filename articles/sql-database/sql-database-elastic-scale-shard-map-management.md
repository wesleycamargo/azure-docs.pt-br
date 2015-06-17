<properties 
	pageTitle="Gerenciamento de mapa de fragmentos" 
	description="Como usar o ShardMapManager, a API de escala elástica para .NET" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Gerenciamento de mapa de fragmentos 
Em um ambiente de banco de dados fragmentado, um **mapa do fragmento** mantém informações permitindo que um aplicativo se conecte ao banco de dados correto com base no valor da **chave de fragmentação**. Noções básicas sobre como esses mapas são construídos é crucial para gerenciar fragmentos na visualização da Escala Elástica.

## Mapas de fragmento e mapeamentos de fragmento 
### Tipos do .Net com suporte para Chaves de fragmentação
A Escala Elástica dá suporte aos seguintes tipos de .Net Framework como chaves de fragmentação:

* inteiro
* longo
* guid
* byte  

### Lista e mapas de fragmento de intervalo
Fragmento mapas podem ser construídos usando **listas de valores de chave de fragmentação individuais**, ou eles podem ser construídos usando **intervalos de valores de chave de fragmentação**. 

###Mapas de fragmento de lista
**Fragmentos** contêm **shardlets** e o mapeamento de shardlets para fragmentos é mantido por um mapa do fragmento. Um **mapa de fragmento de lista** é uma associação entre os valores de chave individuais, que identificam os shardlets e os bancos de dados que servem como fragmentos.  **Mapeamentos de lista** são explícitos (ex.: chave 1 é mapeada para o Banco de dados A) e valores de chave diferentes podem ser mapeados para o mesmo banco de dados (valores-chave 3 e 6 ambos se referem ao Banco de dados B).
<table>
   <tr>
    <td>Chave</td>
     <td>Local de fragmento</td>
   </tr>
   <tr>
    <td>1</td>
     <td>Database_A</td>
   </tr>
  <tr>
    <td>3</td>
     <td>Database_B</td>
   </tr>
  <tr>
    <td>4</td>
     <td>Database_C</td>
   </tr>
  <tr>
    <td>6</td>
     <td>Database_B</td>
   </tr>
  <tr>
    <td>...</td>
     <td>...</td>
   </tr>
</table> 

### Mapas de fragmento de intervalo 
Em um **mapa do fragmento intervalo**, o intervalo de chave é descrito por um par **[baixo valor, valor alto)** onde o *Low Value* é a chave mínima no intervalo e o *High Value* é o primeiro valor maior do que o intervalo. 

Por exemplo, **[0, 100)** inclui todos os números inteiros iguais ou maiores que 0 e menores que 100. Observe que vários intervalos podem apontar para o mesmo banco de dados e intervalos separados são suportados (por exemplo, [100,200) e [400,600) apontam para o banco de dados C no exemplo abaixo.)
<table>
   <tr>
    <td><b>Intervalo de chaves</b></td>
     <td><b>Local de fragmento</b></td>
   </tr>
   <tr>
    <td>[1, 50)</td>
     <td>Database_A</td>
   </tr>
  <tr>
    <td>[50, 100)</td>
     <td>Database_B</td>
   </tr>
  <tr>
    <td>[100, 200)</td>
     <td>Database_C</td>
   </tr>
  <tr>
    <td>[400, 600)</td>
     <td>Database_C</td>
   </tr>
  <tr>
    <td>...</td>
     <td>...</td>
   </tr>
</table> 

Cada uma das tabelas acima mostra é um exemplo conceitual de um objeto **ShardMap**.  Cada linha é um exemplo simplificado de um objeto de **RangeMapping** (para o mapa do fragmento de intervalo) ou **PointMapping** individual (para o mapa do fragmento lista).

## Gerenciador de mapa de fragmentos 

Nas APIs escala elástica, o Gerenciador de mapa do fragmento é uma coleção de mapas de fragmento. Os dados gerenciados por um objeto **ShardMapManager** .Net são mantidos em três locais: 

1. **Mapa do fragmento global (GSM)**: Quando você cria um **ShardMapManager**, você pode especificar um banco de dados para servir como o repositório para todos os seus mapas de fragmento e mapeamentos. Procedimentos armazenados e tabelas especiais são criados automaticamente para gerenciar as informações. Isso geralmente é um banco de dados pequeno e pouco acessados, mas ele não deve ser usado para outras necessidades do aplicativo. As tabelas estiverem em um esquema especial chamado **__ShardManagement**. 

2. **Mapa do fragmento local (LSM)**: Cada banco de dados que você especificar para ser um fragmento dentro de um mapa de fragmento será modificado para conter várias pequenas tabelas e procedimentos armazenados especiais que contêm e gerenciem informações de mapa de fragmento específicas para esse fragmento. Essas informações são redundantes nas informações de GSM, mas permite que o aplicativo validar informações de mapa do fragmento em cache sem colocar nenhuma carga no GSM. O aplicativo usa o LSM para determinar se um mapeamento em cache ainda é válido. As tabelas correspondentes LSM em cada fragmento estão no esquema **__ShardManagement**.

3. **Cache de aplicativo**: Cada instância do aplicativo acessa um objeto **ShardMapManager** mantém um cache na memória local de seus mapeamentos. Ele armazena informações de roteamento que recentemente foi recuperadas. 

## Construindo um ShardMapManager
Um objeto de **ShardMapManager** no aplicativo é instanciado usando um padrão de fábrica. O método **ShardMapManagerFactory.GetSqlShardMapManager** usa credenciais (incluindo o nome do servidor e o nome do banco de dados contendo o GSM) na forma de um **ConnectionString** e retorna uma instância de um **ShardMapManager**.  

O **ShardMapManager** deve ser inicializado apenas uma vez por domínio de aplicativo dentro do código de inicialização para um aplicativo. Um **ShardMapManager** pode conter qualquer número de mapas de fragmento. Enquanto um mapa do fragmento único pode ser suficiente para muitos aplicativos, há vezes quando conjuntos diferentes de bancos de dados são usados para esquemas diferentes ou para fins exclusivos e, nesses casos, vários mapas de fragmentação podem ser preferíveis. 

Nesse código, um aplicativo tenta abrir um **ShardMapManager** existente.  Se os objetos que representam um **ShardMapManager** Global (GSM) ainda não existir no banco de dados, a biblioteca de cliente cria lá.

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
 

### Credenciais de administração de mapa do fragmento

Normalmente, os aplicativos que administrar e manipulam mapas de fragmento são diferentes daqueles que utilizam os mapas de fragmento para conexões de rota. 

Para aplicativos que administrar mapas de fragmento (adição ou alteração de fragmentos, mapas de fragmento, mapeamentos de fragmento, etc.) você deve instanciar o **ShardMapManager** usando as **credenciais que tenham privilégios de leitura/gravação em GSM banco de dados e em cada banco de dados que serve como um fragmento**. As credenciais devem permitir gravações nas tabelas no GSM e LSM como informações de mapa do fragmento são inseridas ou alteradas, bem como para criar tabelas LSM em novos fragmentos.  

### Apenas os metadados afetados 

Métodos usados para preencher ou alterar os dados **ShardMapManager** não alteram os dados de usuário armazenados em fragmentos de si. Por exemplo, métodos como **CreateShard****, DeleteShard**, **UpdateMapping**, etc. afetam somente os metadados de mapa fragmento, não remover, adicionar ou alterar dados de usuário contidos nos fragmentos. Em vez disso, esses métodos destinam-se a ser usado em conjunto com operações separadas, que você pode executar para criar ou remover bancos de dados real ou que move linhas de um fragmento para outro para reequilibrar um ambiente fragmentado.  (O serviço de **﻿Divisão/Mesclagem** incluído com a visualização de escala elástica faz uso dessas APIs além de orquestrar a movimentação de dados real entre fragmentos.) 

## Preenchendo um mapa do fragmento: Exemplo 
Uma sequência de operações para preencher um mapa de fragmento específicos exemplo é mostrada abaixo. O código executa estas etapas: 

1. Um novo mapa de fragmento é criado dentro de um Gerenciador de mapa do fragmento. 
2. Os metadados para dois fragmentos diferentes é adicionado ao mapa de fragmento. 
3. Uma variedade de mapeamentos de intervalo de chave são adicionados e o conteúdo geral do mapa do fragmento é exibido. 

O código é escrito de forma que todo o método pode ser novamente com segurança no caso de um erro inesperado - cada solicitação testa se um fragmento ou mapeando já existe, antes de tentar criá-lo. O código a seguir pressupõe que bancos de dados denominado **sample_shard_0****, sample_shard_1** e **sample_shard_2** já foram criados no servidor referenciado pela cadeia de caracteres **shardServer**. 

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

Depois de mapas de fragmento são preenchidos, aplicativos de acesso de dados podem ser criados ou adaptados para funcionar com os mapas. Preencher ou manipular os mapas não precisa ocorrer novamente até que o **layout do mapa** precise ser alterado.  

## Roteamento dependente de dados 

Melhor uso do Gerenciador de mapa de fragmento virão os aplicativos que exigem conexões de banco de dados para executar as operações de dados específicos do aplicativo. Em um aplicativo fragmentado, essas conexões agora devem estar associadas com o banco de dados de destino correto. Isso é conhecido como **Roteamento dependente de dados**.  Para esses aplicativos, instancie um objeto do Gerenciador do mapa de fragmento de fábrica usando as credenciais que têm acesso somente leitura no banco de dados GSM. As solicitações de conexões individuais posteriormente fornecerá credenciais necessárias para conectar-se ao banco de dados apropriado do fragmento.

Observe que esses aplicativos (usando **ShardMapManager** aberto com as credenciais somente leitura) não poderão alterar os mapeamentos ou mapas.  Para essas necessidades, crie aplicativos administrativos específicos ou scripts do PowerShell que fornecem credenciais de privilégios mais altos, conforme discutido anteriormente.   

Para obter mais detalhes, consulte [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). 

## Modificando um mapa do fragmento 

Um mapa do fragmento pode ser alterado de diferentes maneiras. Todos os seguintes métodos de modificar os metadados que descrevem os fragmentos e seus mapeamentos, mas eles não modificam dados dentro de fragmentos fisicamente, nem que criar ou excluir os bancos de dados reais.  Algumas das operações no mapa de fragmento descrito abaixo talvez precisem ser coordenada com ações administrativas que mover fisicamente os dados ou que adicionar e remover bancos de dados que serve como fragmentos.

Esses métodos funcionam juntos como blocos de construção disponíveis para modificar a distribuição global de dados em seu ambiente de banco de dados fragmentado.  

* Para adicionar ou remover fragmentos: use **CreateShard** e **DeleteShard**. 
    
    O servidor e o banco de dados que representa o fragmento de destino já devem existir para executar essas operações. Esses métodos não têm qualquer impacto nos bancos de dados, apenas nos metadados no mapa do fragmento.

* Para criar ou remover pontos ou intervalos que são mapeados para os fragmentos: use **CreateRangeMapping**, **DeleteMapping**, **CreatePointMapping**. 
    
    Vários pontos diferentes ou intervalos podem ser mapeados para o mesmo fragmento. Esses métodos afetam somente metadados - elas não afetam todos os dados que podem já estar presentes em fragmentos. Se os dados precisarem ser removidas do banco de dados para ser consistente com as operações **DeleteMapping**, você precisará realizar essas operações separadamente, mas em conjunto com o uso desses métodos.  

* Para dividir intervalos existentes em duas ou mesclar intervalos adjacentes em um: use **SplitMapping** e **MergeMappings**.  

    Observe que as operações de divisão e mesclagem **não alteram o fragmento no qual os valores de chave são mapeados**. Uma divisão divide um intervalo existente em duas partes, mas deixa ambos como mapeada para o mesmo fragmento. Uma mesclagem opera em dois intervalos adjacentes que já são mapeados para o mesmo fragmento, juntando-os em um único intervalo.  A movimentação de pontos ou nos intervalos próprios entre fragmentos precisa ser coordenadas usando **UpdateMapping** em conjunto com a movimentação de dados real.  Você pode usar o serviço de **﻿Divisão/Mesclagem** que faz parte da visualização de escala elástica para coordenar as alterações de mapa do fragmento com a movimentação de dados, quando o movimento é necessária. 

* Para remapear (ou mover) pontos individuais ou intervalos de fragmentos diferentes: use **UpdateMapping**.  

    Uma vez que os dados podem precisar ser movidos de um fragmento para outro para ser consistente com as operações de **UpdateMapping**, você precisará executar esse movimento separadamente, mas em conjunto com o uso desses métodos.

* Se os mapeamentos online e offline: use **MarkMappingOffline** e **MarkMappingOnline** para controlar o estado on-line de um mapeamento. 

    Algumas operações nos mapeamentos de fragmento são permitidas apenas quando um mapeamento está em um estado "offline", incluindo UpdateMapping e DeleteMapping. Quando um mapeamento estiver offline, uma solicitação dependente de dados com base em uma chave incluída nesse mapeamento retornará um erro. Além disso, quando um intervalo primeiro fica offline, todas as conexões para o fragmento afetado serão eliminadas automaticamente para evitar resultados inconsistentes ou incompletos para consultas direcionadas com intervalos que está sendo alterados. 

## Adicionando um fragmento 

Geralmente, os aplicativos precisam simplesmente adicionar novos fragmentos para lidar com dados que são esperados de novas chaves ou intervalos de chaves para um mapa do fragmento que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário talvez tenha provisionar um novo fragmento para um novo locatário ou dados mensalmente fragmentados talvez precisem de um novo fragmento provisionado antes do início de cada novo mês. 

Se o novo intervalo de valores de chave já não é parte de um mapeamento existente e nenhuma movimentação de dados é necessária, é muito simples adicionar o novo fragmento e associar a nova chave ou o intervalo para esse fragmento. Para obter detalhes sobre como adicionar novos fragmentos, consulte [Adicionando um novo fragmento](sql-database-elastic-scale-add-a-shard.md).

Para cenários que exigem a movimentação de dados, no entanto, o serviço de Divisão-Mesclagem é necessário para orquestrar a movimentação dos dados entre os fragmentos em combinação com as atualizações necessárias de mapa do fragmento. Para obter detalhes sobre como usar o serviço de divisão e mesclagem, consulte [Visão geral da Divisão-Mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--HONumber=47-->
 