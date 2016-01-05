<properties 
	pageTitle="Roteamento dependente de dados | Microsoft Azure" 
	description="Como usar o ShardMapManager para o roteamento dependente de dados, um recurso dos bancos de dados elásticos para o Banco de Dados SQL do Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="torsteng;sidneyh"/>

#Roteamento dependente de dados

A classe **ShardMapManager** fornece a aplicativos ADO.NET a capacidade de direcionar facilmente consultas de banco de dados e comandos para o banco de dados físico apropriado em um ambiente fragmentado. Isso é chamado de **roteamento dependentes de dados** e é um padrão fundamental ao trabalhar com bancos de dados fragmentados. Cada consulta específica ou a transação em um aplicativo que usa o roteamento dependentes de dados é restrita a acessar um banco de dados por solicitação.

Usando o roteamento de dados dependentes, não é necessário para o aplicativo controlar a várias cadeias de caracteres de conexão ou locais de banco de dados associados a diferentes subconjuntos de dados no ambiente fragmentado. Em vez disso, o [Gerenciador de mapa do fragmento](sql-database-elastic-scale-shard-map-management.md) assume a responsabilidade pelo envio de conexões abertas no banco de dados correto quando necessário, com base nos dados do mapa de fragmentos e no valor da chave de fragmentação que é o destino da solicitação do aplicativo. (Essa chave é normalmente o *customer\_id*, *tenant\_id*, *date\_key* ou outro identificador específico que é um parâmetro fundamental da solicitação de banco de dados).

## Baixar a biblioteca de cliente

Para instalar a biblioteca, vá para a [Biblioteca de cliente de Banco de Dados Elástico](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Usando um ShardMapManager em um aplicativo de roteamento dependente de dados 

Para aplicativos que usam o roteamento dependente de dados, um **ShardMapManager** deve ter uma instância criada uma vez por domínio de aplicativo, durante a inicialização, usando a chamada de fábrica **GetSQLShardMapManager**.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

Neste exemplo, tanto um **ShardMapManager** quanto um **ShardMap** específico que ele contém são inicializados.

Para um aplicativo que não está manipulando o próprio mapa de fragmentos, as credenciais usadas no método de fábrica para obter o **ShardMapManager** (no exemplo acima, *smmConnectionString*) devem ser credenciais que tenham permissões somente leitura no banco de dados do **Mapa de Fragmentos Global** referenciado pela cadeia de conexão. Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões com o Gerenciador de mapa do fragmento. Veja também [Usar as credenciais nas bibliotecas de cliente de banco de dados elástico](sql-database-elastic-scale-manage-credentials.md).

## Invocando o roteamento dependente de dados 

O método **ShardMap.OpenConnectionForKey (key, connectionString, connectionOptions)** retorna uma conexão do ADO.NET pronta para executar comandos para o banco de dados apropriado com base no valor do parâmetro **key**. As informações de fragmentos são armazenadas em cache no aplicativo pelo **ShardMapManager**, portanto, essas solicitações normalmente não envolvem uma pesquisa de banco de dados no banco de dados do **Mapa de Fragmentos Global**.

* O parâmetro **key** é usado como uma chave de pesquisa no mapa de fragmentos para determinar o banco de dados apropriado para a solicitação. 

* **connectionString** é usado para transmitir somente as credenciais do usuário para a conexão desejada. Nenhum nome de banco de dados ou de servidor está incluído nessa *connectionString*, pois o método determinará o banco de dados e o servidor usando o **ShardMap**.

* A enumeração **connectionOptions** é usada para indicar se a validação ocorre ou não ao fornecer a conexão aberta. **ConnectionOptions.Validate** é recomendado. Em um ambiente onde mapas de fragmento podem ser alterado e linhas podem estar sendo movidos para outros bancos de dados como resultado de operações de divisão ou de mesclagem, a validação assegura que a pesquisa em cache do banco de dados com base em um valor de chave ainda está correta. A validação envolve uma consulta curta para o mapa do fragmento local de destino do banco de dados (não para o mapa do fragmento global) antes que a conexão é entregue ao aplicativo.

Se a validação em relação ao mapa de fragmento local falhar (indicando que o cache está incorreto), o Gerenciador de mapa do fragmento consultará o mapa do fragmento global para obter o novo valor correto para a pesquisa, atualizar o cache e obtenha e retorne a conexão de banco de dados apropriado.

A única vez que **ConnectionOptions.None** (não validado) é aceitável ocorre quando as alterações de mapeamento de fragmentos não são esperadas enquanto um aplicativo estiver online. Nesse caso, os valores do cache podem ser considerados sempre corretas, e a chamada de validação extra ida e volta ao banco de dados de destino pode ser ignorada com segurança. Isso poderá reduzir latências de transação e o tráfego de banco de dados. **connectionOptions** também pode ser definido por meio de um valor em um arquivo de configuração para indicar se as alterações de fragmentação são ou não esperadas durante um período.

Este é um exemplo de código que usa o Gerenciador de Mapa de Fragmentos para executar o roteamento dependente de dados com base no valor de uma chave de inteiro **CustomerID** usando um objeto **ShardMap** chamado **customerShardMap**.

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

Observe que, em vez de usar um construtor para um **SqlConnection** seguido por uma chamada **Open()** para o objeto de conexão, o método **OpenConnectionForKey** é usado e oferece uma nova conexão já aberta no banco de dados correto. Conexões utilizados dessa forma ainda se beneficiar do pool de conexões do ADO.Net. Como transações e solicitações podem ser atendidas por um fragmento por vez, isso deve ser a única modificação necessária em um aplicativo já usando ADO.Net.

O método **OpenConnectionForKeyAsync** também está disponível se o seu aplicativo usar a programação assíncrona com o ADO.NET. Seu comportamento é o equivalente ao roteamento dependente de dados do método **Connection.OpenAsync** do ADO.NET.

## Integrando a manipulação de falhas transitórias 

É uma prática recomendada no desenvolvimento de aplicativos de acesso de dados na nuvem garantir que as falhas transitórias na conexão com ou consultar o banco de dados são capturadas pelo aplicativo e que as operações serão repetidas várias vezes antes que ocorra um erro. O tratamento de falha transitória para aplicativos em nuvem é discutido em [Tratamento de falhas transitórias](http://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx).
 
A manipulação de falhas transitórias pode coexistir naturalmente com o padrão de Roteamento dependente de dados. O principal requisito é repetir a solicitação de acesso de dados inteira incluindo o bloqueio **using** que obteve a conexão de roteamento dependente de dados. O exemplo anterior poderia ser reescrito da seguinte maneira (alteração de anotação realçada).

### Exemplo – roteamento com falhas transitórias manipulação dependentes dos dados 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
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

            Console.WriteLine("Atualização concluída"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Pacotes necessários para implementar o tratamento de falhas transitórias são baixados automaticamente quando você compila o aplicativo de exemplo de banco de dados elástico. Pacotes também estão disponíveis separadamente na [Biblioteca Corporativa - Bloco de aplicativo de tratamento de falha transitória](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Use a versão 6.0 ou posterior.

## Consistência transacional 

Propriedades transacionais são garantidas para todas as operações locais para um fragmento. Por exemplo, transações enviadas por meio do roteamento dependentes de dados executado dentro do escopo do fragmento de destino para a conexão. Neste momento, não há nenhum recurso fornecido para inscrever-se várias conexões em uma transação e, portanto, não há nenhuma garantia transacional para operações executadas pelos fragmentos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_1210_2015-->