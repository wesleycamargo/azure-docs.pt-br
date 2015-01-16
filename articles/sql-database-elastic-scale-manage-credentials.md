<properties title="Managing Elastic Scale Credentials" pageTitle="Gerenciamento de credenciais da Escala Elástica" description="Como definir o nível certo de credenciais de administrador somente leitura para aplicativos de escala elástica." metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Gerenciamento de credenciais da Escala Elástica  

As [APIs de cliente de escala elástica](http://go.microsoft.com/?linkid=9862605) usam credenciais para diferentes tipos de operações - em particular criando ou manipulando um [Gerenciador de mapa do fragmento](http://go.microsoft.com/?linkid=9862595), fazendo referência a um Gerenciador de mapa do fragmento existente para obter informações sobre fragmentos e conectando-se a fragmentos. Credenciais para esses tipos de operações são discutidas abaixo. 


* **Credenciais de gerenciamento para acesso de mapa do fragmento**: As credenciais de gerenciamento são usadas ao instanciar um objeto **ShardMapManager** para aplicativos que manipulam o fragmento são mapeadas. O usuário das APIs de ﻿Escala Elástica deve criar os usuários necessários do SQL e logons do SQL Server e verifique se que eles são concedidos as permissões de leitura/gravação, o banco de dados de mapa do fragmento global e todos os fragmentos bancos de dados também. Essas credenciais são usadas para manter o mapa do fragmento globais e os mapas de fragmento local quando as alterações para o mapa do fragmento são executadas. Por exemplo, use as credenciais de gerenciamento para instanciar o objeto de gerenciador do mapa do fragmento, como mostra o seguinte código: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     A variável **smmAdminConnectionString** é uma cadeia de conexão que contém as credenciais de gerenciamento. A ID de usuário e senha fornece acesso de leitura/gravação ao banco de dados de mapa de fragmento e fragmentos individuais. A cadeia de conexão de gerenciamento também inclui o nome do servidor e o nome do banco de dados para identificar o banco de dados de mapa do fragmento global. Aqui está uma cadeia de conexão típica para essa finalidade:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

* **Credenciais de usuário para Acesso de gerenciamento de mapa do fragmento**:  Ao instanciar o Gerenciador de mapa do fragmento em um aplicativo que não vai administrar mapas de fragmento, use credenciais que tenham permissões somente leitura no mapa de fragmento global. As informações recuperadas do mapa do fragmento global sob essas credenciais são usadas para [roteamento dependente de dados](./sql-database-elastic-scale-data-dependent-routing.md) e popular o cache de mapa do fragmento no cliente. As credenciais são fornecidas por meio do mesmo padrão de chamada para **GetSqlShardMapManager**, conforme mostrado acima: 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Observe o uso de **smmReadOnlyConnectionString** para refletir o uso de credenciais diferentes para esse acesso em nome dos usuários **não administradores** - essas credenciais não devem fornecer permissões de gravação no mapa de fragmento global. 

* **Credenciais de usuário para Acesso aos dados de usuário**: Credenciais adicionais são necessárias ao usar o método **OpenConnectionForKey** para acessar um fragmento associado com uma chave. Essas credenciais precisam fornecer permissões para acesso somente leitura às tabelas de mapa de fragmento local que residem no fragmento. Isso é necessário para executar a validação de conexão para roteamento dependentes de dados sobre o fragmento. O trecho de código a seguir ilustra o uso das credenciais do usuário para acesso aos dados de usuário no contexto do roteamento dependente de dados: 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

     Neste exemplo, **smmUserConnectionString** contém a cadeia de conexão para as credenciais do usuário. Para o banco de dados SQL Azure, eis uma cadeia de conexão típica para as credenciais do usuário: 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

    Observe que a cadeia de conexão não contém um nome do servidor e banco de dados. Isso ocorre porque a chamada **OpenConnectionForKey** redirecionará automaticamente a conexão com o fragmento correto com base na chave. Portanto, o nome do servidor e o nome do banco de dados não devem ser fornecidos. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
