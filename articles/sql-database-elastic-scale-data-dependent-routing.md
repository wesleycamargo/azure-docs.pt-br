<properties 
	pageTitle="Roteamento dependente de dados" 
	description="Como usar o ShardMapManager para roteamento dependente de dados, um recurso de escala elástica para DB do SQL Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="stuartozer" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto"/>

#Roteamento dependente de dados

A classe **ShardMapManager** fornece a capacidade de direcionar facilmente consultas de banco de dados e comandos no banco de dados físico apropriado em um ambiente fragmentado de aplicativos ADO.NET. Isso é chamado de **roteamento dependentes de dados** e é um padrão fundamental ao trabalhar com bancos de dados fragmentados. Cada consulta específica ou a transação em um aplicativo que usa o roteamento dependentes de dados é restrita a acessar um banco de dados por solicitação.  

Usando o roteamento de dados dependentes, não é necessário para o aplicativo controlar a várias cadeias de caracteres de conexão ou locais de banco de dados associados a diferentes subconjuntos de dados no ambiente fragmentado. Em vez disso, o [Gerenciador do mapa do fragmento](./sql-database-elastic-scale-shard-map-management.md) assume a responsabilidade pela distribuição das conexões abertas para o banco de dados correto quando necessário, com base nos dados do mapa do fragmento e o valor da chave de fragmentação é o destino da solicitação do aplicativo. (Essa chave é normalmente o *customer_id,* *tenant_id* *date_key*,, ou outro identificador específico que é um parâmetro fundamental da solicitação do banco de dados). 

## Usando um ShardMapManager em um aplicativo de roteamento dependente de dados 

Para aplicativos que usam roteamento dependente de dados, um **ShardMapManager** deve ser instanciado uma vez por domínio de aplicativo, durante a inicialização, utilizando a fábrica de chamar **GetSQLShardMapManager**.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

Neste exemplo, tanto um **ShardMapManager** quanto um **ShardMap** específico que ele contém são inicializados. 

Para um aplicativo que não está manipulando o fragmento mapear, as credenciais usadas no método de fábrica para obter o **ShardMapManager** (no exemplo acima,  *smmConnectionString*) deve ser credenciais que tenham permissões somente leitura apenas do banco de dados do **Mapa do fragmento global** referenciada pela cadeia de conexão. Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões com o Gerenciador de mapa do fragmento. Consulte também [Gerenciando credenciais de Escala Elástica](sql-database-elastic-scale-manage-credentials.md). 

## Invocando o roteamento dependente de dados 

O método **ShardMap.OpenConnectionForKey (key, connectionString, connectionOptions)** retorna uma conexão ADO.Net pronta para emitir comandos para o banco de dados apropriado com base no valor do parâmetro **chave**. Informações de fragmento são armazenadas em cache no aplicativo por **ShardMapManager**, para que essas solicitações normalmente não envolvem uma pesquisa de banco de dados no banco de dados do **Mapa do fragmento global**. 

* O parâmetro da **chave** é usado como uma chave de pesquisa para o mapa do fragmento para determinar o banco de dados apropriado para a solicitação. 

* A **connectionString** é usado para passar somente as credenciais do usuário para a conexão desejada. Nenhum nome de banco de dados ou do servidor incluídas nessa *connectionString* como o método determinará o banco de dados e o servidor usando o **ShardMap**. 

* A enumeração **connectionOptions** é usada para indicar se a validação ocorre ou não ao fornecer a conexão aberta. **Recomenda-se ConnectionOptions.Validate**. Em um ambiente onde mapas de fragmento podem ser alterado e linhas podem estar sendo movidos para outros bancos de dados como resultado de operações de divisão ou de mesclagem, a validação assegura que a pesquisa em cache do banco de dados com base em um valor de chave ainda está correta. A validação envolve uma consulta curta para o mapa do fragmento local de destino do banco de dados (não para o mapa do fragmento global) antes que a conexão é entregue ao aplicativo. 

Se a validação em relação ao mapa de fragmento local falhar (indicando que o cache está incorreto), o Gerenciador de mapa do fragmento consultará o mapa do fragmento global para obter o novo valor correto para a pesquisa, atualizar o cache e obtenha e retorne a conexão de banco de dados apropriado. 

A única vez que **ConnectionOptions.None** (não valida) é aceitável ocorre quando as alterações de mapeamento de fragmento não se espera enquanto um aplicativo está online. Nesse caso, os valores do cache podem ser considerados sempre corretas, e a chamada de validação extra ida e volta ao banco de dados de destino pode ser ignorada com segurança. Isso poderá reduzir latências de transação e o tráfego de banco de dados. O **connectionOptions** também pode ser definido por meio de um valor em um arquivo de configuração para indicar se as alterações de fragmentação devem ou não durante um período de tempo.  

Este é um exemplo de código que usa o Gerenciador de mapa do fragmento para executar roteamento de dependentes de dados com base no valor de uma chave de inteiro **CustomerID**, usando um objeto **ShardMap** nomeado **customerShardMap**.  

## Exemplo: roteamento dependente de dados 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Observe que, em vez de usar um construtor para um **SqlConnection**, seguido por uma chamada de **Open ()** para o objeto de conexão, o método **OpenConnectionForKey** é usado e oferece uma nova conexão já aberta no banco de dados correto. Conexões utilizados dessa forma ainda se beneficiar do pool de conexões do ADO.Net. Como transações e solicitações podem ser atendidas por um fragmento por vez, isso deve ser a única modificação necessária em um aplicativo já usando ADO.Net. 

O método **OpenConnectionForKeyAsync** também está disponível se seu aplicativo usa a programação assíncrona com o ADO.Net.  Seu comportamento é o equivalente ao roteamento dependente de dados do método **Connection.OpenAsync** do ADO.Net.

## Integrando a manipulação de falhas transitórias 

É uma prática recomendada no desenvolvimento de aplicativos de acesso de dados na nuvem garantir que as falhas transitórias na conexão com ou consultar o banco de dados são capturadas pelo aplicativo e que as operações serão repetidas várias vezes antes que ocorra um erro. A manipulação de falha transitória para aplicativos em nuvem é discutida em [Transient Fault Handling](http://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx ). 
 
A manipulação de falhas transitórias pode coexistir naturalmente com o padrão de Roteamento dependente de dados. O principal requisito é repetir a solicitação de acesso de dados inteira incluindo o **uso** de bloco que obteve a conexão de roteamento de dados dependentes. O exemplo anterior poderia ser reescrito da seguinte maneira (alteração de anotação realçada). 

### Exemplo - roteamento com falhas transitórias manipulação dependentes dos dados 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connection to the shard for that customer ID 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Pacotes necessários para implementar o tratamento de falhas transitórias são baixados automaticamente quando você compila o aplicativo Kit de início da Escala Elástica. Pacotes também estão disponíveis separadamente da [Biblioteca de negócios - Bloco do aplicativo de manipulação de falha transitória](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Use a versão 6.0 ou posterior. 

## Consistência transacional 

Propriedades transacionais são garantidas para todas as operações locais para um fragmento. Por exemplo, transações enviadas por meio do roteamento dependentes de dados executado dentro do escopo do fragmento de destino para a conexão. Neste momento, não há nenhum recurso fornecido para inscrever-se várias conexões em uma transação e, portanto, não há nenhuma garantia transacional para operações executadas pelos fragmentos.  

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
