<properties 
	title="Upgrade to the Latest Elastic Scale Client Library" 
	pageTitle="Atualizar para a biblioteca de cliente de dimensionamento Elástico mais recente" 
	description="Atualizar instruções usando o PowerShell e C#" 
	metaKeywords="sharding,elastic scale, Azure SQL DB sharding" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="stuarto", "sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto" />

# Atualizar para a biblioteca de cliente de dimensionamento Elástico mais recente

Novas versões da biblioteca de cliente de dimensionamento elástico estão disponíveis por meio de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e da interface do Gerenciador NuGetPackage no Visual Studio. Atualizações contêm correções de bugs e suporte para novos recursos de dimensionamento Elástico.

## Etapas de atualização

A atualização requer a recriação de seu aplicativo com a nova biblioteca, bem como alteração dos metadados do Gerenciador de mapa do fragmento existentes armazenados em seus bancos de dados do SQL Azure para oferecer suporte a novos recursos.

Siga a sequência abaixo para atualizar seus aplicativos, o banco de dados do Gerenciador de mapa do fragmento e os metadados do Gerenciador de Mapa do fragmento em cada fragmento.  Executar as etapas de atualização nesta ordem garante que as versões antigas da biblioteca de cliente não estejam mais presentes em seu ambiente quando objetos de metadados são atualizados, que significa que os objetos de metadados de versão antiga não serão criados após a atualização.   

**1. Atualize seus aplicativos.** No Visual Studio, baixe e referencie a versão mais recente da biblioteca do cliente em todos os seus projetos de dimensionamento elástico. Em seguida, recrie e implante. 

 * Em sua solução do Visual Studio, selecione **Ferramentas** --> **NuGet Package Manager** -->**Gerenciar pacotes NuGet para solução**. 
 * No painel esquerdo, selecione **Atualizações**e, em seguida, selecione o botão **Atualizar** no pacote **Biblioteca de cliente de dimensionamento elástico do Banco de dados SQL do Azure** que aparece na janela.
	![Upgrade Nuget Pacakges][1]
 
 * Criar e implantar. 

**2. Atualize seus scripts.** Se você estiver usando scripts do **PowerShell** para gerenciar os fragmentos, [baixe a nova versão da biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e a copie para o diretório de onde você executa scripts. 

**3. Atualize o seu serviço de mesclagem de divisão.** Se você usar o serviço de mesclagem de divisão de dimensionamento elástico para reorganizar dados fragmentados, [baixe e implante a versão mais recente do serviço](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Etapas detalhadas de atualização para o serviço podem ser encontradas [aqui](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-overview-split-and-merge/). 

**4. Atualizar seus bancos de dados do Gerenciador do mapa de fragmento**. Atualizar os metadados que suportam seus mapas de fragmento no banco de dados SQL do Azure.  Há duas maneiras que você pode fazer isso, usando o PowerShell ou C#. Ambas as opções são mostradas abaixo.

***Opção 1: Atualizar os metadados usando o PowerShell***

1. Baixar o utilitário de linha de comando mais recente para NuGet [aqui](http://nuget.org/nuget.exe) e salvar em uma pasta. 

2. Abra um Prompt de Comando, navegue para a mesma pasta e execute o comando:
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Navegue até a subpasta que contém a nova versão DLL do cliente que acabou de baixar, por exemplo:
`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.0.8.0\lib\net45`

4. Baixe o scriptlet da atualização de clientes de dimensionamento elástico do [Script Center](http://go.microsoft.com/?linkid=9876343)e salve-o na mesma pasta que contém a DLL.

5. Nessa pasta, execute "PowerShell .\upgrade.ps1" no prompt de comando e siga os prompts.
 
***Opção 2: Atualizar os metadados usando C#***

Como alternativa, crie um aplicativo do Visual Studio que abre sua ShardMapManager, itera em todos os fragmentos e executa a atualização de metadados chamando os métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) e [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) como neste exemplo: 

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

Essas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem danos. Por exemplo, se uma versão mais antiga do cliente cria inadvertidamente um fragmento depois que você atualizou, você pode executar a atualização novamente entre todos os fragmentos para garantir que a versão mais recente de metadados está presente em toda a infraestrutura. 

**Obs.:** a data de publicação de novas versões da biblioteca de cliente continua a funcionar com as versões anteriores dos metadados do Gerenciador de mapa do fragmento no banco de dados SQL do Azure e vice-versa. No entanto, para aproveitar alguns dos novos recursos no cliente mais recente, os metadados precisam ser atualizados. Observe que as atualizações de metadados não afetarão quaisquer dados do usuário ou dados específicos do aplicativo, somente os objetos criados e usados pelo Gerenciador de mapa do fragmento. E os aplicativos continuam a operar a sequência de atualização descrita acima.

## Histórico de versão do cliente de dimensionamento elástico 

**Versão 0.8 - março de 2015**

* Suporte assíncrono adicionado para roteamento dependente de dados com os novos métodos ShardMap.OpenConnectionForKeyAsync. 
* Propriedade KeyType pública adicionada ao ShardMap 
* Melhorias adicionadas que suportam cenários de recuperação de desastres e restauração de banco de dados para fragmentos 

**Versão 0.7 - outubro de 2014**

Versão de visualização inicial 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

<!--HONumber=47-->
