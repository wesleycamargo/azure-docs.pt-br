<properties 
	pageTitle="Perguntas frequentes sobre a Escala elástica do SQL Azure" 
	description="Perguntas frequentes sobre a Escala Elástico do banco de dados SQL do Azure." 
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
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# Pergunta Frequentes das ferramentas de banco de dados elástico 

#### Se eu tiver um único locatário por fragmento e nenhuma chave de fragmentação, como preencher a chave de fragmentação para as informações do esquema?
O objeto de informações de esquema só é usado para dividir os cenários de mesclagem. Se um aplicativo for basicamente o único locatário, ele não exige a ferramenta de divisão e mesclagem e, portanto, não é necessário para preencher o objeto de informações de esquema.

#### Eu provisionei um banco de dados e já tenho um Gerenciador de mapa de fragmento, como registro o novo banco de dados como um fragmento?
Consulte **[Adicionar um fragmento de um aplicativo usando a biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-add-a-shard.md)**.

#### Quanto custam as ferramentas de banco de dados elástico?
Usar a biblioteca de cliente do banco de dados elástico não incorrerá em todos os custos. Custos se acumulam apenas para os bancos de dados do SQL Azure que você usa para fragmentos e o Gerenciador de mapa de fragmento, bem como as funções web/de trabalho configuradas para a ferramenta de divisão e mesclagem.

#### Por que minhas credenciais não funcionam quando eu adiciono um fragmento de um servidor diferente?
Não use credenciais na forma de “Usuário ID=username@servername”, em vez disso, basta usar “User ID = username”. Além disso, certifique-se de que o logon “username” tem permissões no fragmento.

#### É necessário criar um Gerenciador de mapa de fragmento e preencher os fragmentos sempre que iniciar meus aplicativos?
Não, a criação do Gerenciador de mapa de fragmento (por exemplo, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) é uma operação única. Seu aplicativo deve usar a chamada **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** em tempo de inicialização do aplicativo. Deve existir apenas uma chamada por domínio de aplicativo.

#### Tenho dúvidas sobre o uso das ferramentas de banco de dados elástico, como obter respostas para elas? 
Vá ao [Fórum do banco de dados SQL do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### Quando recebo uma conexão de banco de dados usando uma chave de fragmentação, ainda posso consultar dados para outras chaves de fragmentação no mesmo fragmento. Isso é proposital?
As API de escala elástica oferecem uma conexão com o banco de dados correto para sua chave de fragmentação, mas não fornece filtragem de chave de fragmentação. Adicione cláusulas **WHERE** à sua consulta para restringir o escopo para a chave de fragmentação fornecidos, se necessário.

#### Posso usar uma edição diferente do banco de dados do Azure para cada fragmento no meu conjunto de fragmentos?
Sim, um fragmento é um banco de dados individual e, portanto, um fragmento poderia ser uma edição Premium e o outro ser uma edição Standard. Além disso, a edição de um fragmento pode aumentar ou diminuir várias vezes durante o tempo de vida do fragmento.

#### O ferramenta de divisão e mesclagem provisiona (ou exclui) um banco de dados durante uma operação de divisão ou mesclagem? 
Não. Para operações de **divisão**, o banco de dados de destino deve existir com o esquema apropriado e ser registrado com o Gerenciador de mapa do fragmento. Para operações de **mesclagem**, você deve excluir o fragmento do Gerenciador do mapa de fragmento e, em seguida, excluir o banco de dados.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=62-->