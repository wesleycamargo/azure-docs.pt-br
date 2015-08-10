<properties 
	pageTitle="Gerenciamento de credenciais na biblioteca de cliente do banco de dados elástico" 
	description="Como definir o nível certo de credenciais de administrador para somente leitura em aplicativos de banco de dados elástico." 
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
	ms.date="04/24/2015" 
	ms.author="sidneyh"/>

# Gerenciamento de credenciais na biblioteca de cliente do banco de dados elástico

A [biblioteca cliente do Banco de Dados Elástico](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) usa credenciais para diferentes tipos de operações, especialmente ao criar ou manipular um [gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md), fazendo referência a um Gerenciador de Mapa de Fragmentos existente para obter informações sobre fragmentos e conectar-se a eles. Credenciais para esses tipos de operações são discutidas abaixo.


* **Credenciais de gerenciamento de acesso ao mapa de fragmentos**: as credenciais de gerenciamento são usadas ao instanciar um objeto **ShardMapManager** para aplicativos que lidam com os mapas de fragmentos. O usuário da biblioteca de cliente de escala elástica deve criar os usuários necessários do SQL e logons do SQL Server, alem de verificar se que eles têm as permissões de leitura/gravação no banco de dados do mapa de fragmentos global e também em todos os bancos de dados de fragmentos. Essas credenciais são usadas para manter o mapa do fragmento globais e os mapas de fragmento local quando as alterações para o mapa do fragmento são executadas. Por exemplo, use as credenciais de gerenciamento para instanciar o objeto de gerenciador do mapa do fragmento, como mostra o seguinte código: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     A variável **smmAdminConnectionString** é uma cadeia de conexão que contém as credenciais de gerenciamento. A ID de usuário e senha fornece acesso de leitura/gravação ao banco de dados de mapa de fragmento e fragmentos individuais. A cadeia de conexão de gerenciamento também inclui o nome do servidor e o nome do banco de dados para identificar o banco de dados de mapa do fragmento global. Aqui está uma cadeia de conexão típica para essa finalidade:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

     Não use valores de ID de usuário na forma de “username@server”--em vez disso, basta usar o “username”. Isso ocorre porque as credenciais devem funcionar no banco de dados do Gerenciador de mapa do fragmento e fragmentos individuais, que podem estar em servidores diferentes.
     
* **Credenciais de usuário para acesso ao gerenciador de mapa de fragmentos**: ao instanciar o gerenciador de mapa de fragmentos em um aplicativo que não vai administrar mapas de fragmento, use credenciais que tenham permissões somente leitura ao mapa de fragmentos global. As informações recuperadas do mapa de fragmentos global sob essas credenciais são usadas para [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e para popular o cache do mapa de fragmentos no cliente. As credenciais são fornecidas por meio do mesmo padrão de chamada para **GetSqlShardMapManager**, conforme mostrado acima:
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Observe o uso de **smmReadOnlyConnectionString** para refletir o uso de credenciais diferentes para esse acesso em nome dos usuários **não administradores**; essas credenciais não devem fornecer permissões de gravação ao mapa de fragmentos global.

* **Credenciais de usuário para acesso aos dados de usuário**: credenciais adicionais são necessárias ao usar o método **OpenConnectionForKey** para acessar um fragmento associado a uma chave. Essas credenciais precisam fornecer permissões para acesso somente leitura às tabelas de mapa de fragmento local que residem no fragmento. Isso é necessário para executar a validação de conexão para roteamento dependentes de dados sobre o fragmento. O trecho de código a seguir ilustra o uso das credenciais do usuário para acesso aos dados de usuário no contexto do roteamento dependente de dados:
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    Neste exemplo, **smmUserConnectionString** contém a cadeia de conexão para as credenciais do usuário. Para o banco de dados SQL Azure, eis uma cadeia de conexão típica para as credenciais do usuário:

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

    Não use valores de ID de usuário na forma de “username@server”--em vez disso, basta usar o “username”. Observe que a cadeia de conexão não contém um nome do servidor e banco de dados. Isso ocorre porque a chamada **OpenConnectionForKey** redirecionará automaticamente a conexão com o fragmento correto com base na chave. Portanto, o nome do servidor e o nome do banco de dados não devem ser fornecidos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO5-->