<properties
   pageTitle="Distribuir dados globalmente com o Banco de Dados de Documentos | Microsoft Azure"
   description="Saiba mais sobre replicação geográfica em nível mundial, failover e recuperação de dados usando bancos de dados globais do Banco de Dados de Documentos do Azure, um serviço de banco de dados NoSQL totalmente gerenciado."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/14/2016"
   ms.author="kipandya"/>
   
   
# Distribute data globally with DocumentDBs (Distribuir dados globalmente com o Banco de Dados de Documentos)

O Banco de Dados de Documentos foi desenvolvido para atender às necessidades de aplicativos IoT que consistem em milhões de dispositivos globalmente distribuídos e aplicativos de escala de Internet que proporcionam experiências altamente responsivas aos usuários no mundo todo. Esses sistemas de banco de dados enfrentam o desafio de conseguir acesso de baixa latência a dados de aplicativo de várias regiões geográficas com garantias bem definidas de consistência e disponibilidade de dados. Como um sistema de banco de dados globalmente distribuído, o Banco de Dados de Documentos simplifica a distribuição global de dados oferecendo contas de banco de dados em várias regiões totalmente gerenciadas que fornecem claras compensações entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. As contas de banco de dados do Banco de Dados de Documentos são oferecidas com alta disponibilidade, latências de milissegundos de digito único, vários [níveis bem definidos de consistência][consistency], failover regional transparente com APIs de hospedagem múltipla e a capacidade de escalar elasticamente a produtividade e o armazenamento ao redor do mundo.

  
## Configurando contas de várias regiões

A configuração da conta do Banco de Dados de Documentos para escalar no nível mundial pode ser realizada em menos de um minuto pelo Portal do Azure. Tudo o que você precisa fazer é selecionar o nível certo de consistência entre os diversos níveis de consistência bem definidos compatíveis, bem como associar qualquer número de regiões do Azure à sua conta de banco de dados. Os níveis de consistência do Banco de Dados de Documentos fornecem claras compensações entre garantia de consistência específica e desempenho.

![O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha][1]

O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha.

A seleção do nível certo de consistência depende da garantia da consistência dos dados de que seu aplicativo precisa. O Banco de Dados de Documentos replica automaticamente os dados em todas as regiões especificadas e garante a consistência que você selecionou para sua conta de banco de dados.


## Usando o failover de várias regiões 

O Banco de Dados de Documentos é capaz de fazer failover de modo transparente de contas de banco de dados em várias regiões do Azure — as novas [APIs de hospedagem múltipla][developingwithmultipleregions] garantem que o aplicativo possa continuar usando um ponto de extremidade lógico e não seja interrompido pelo failover. O failover é controlado por você, fornecendo a flexibilidade para hospedar novamente sua conta de banco de dados no caso de ocorrer qualquer condição de falha, incluindo falhas de aplicativo, infraestrutura, serviço ou regionais (reais ou simuladas). Em caso de falha regional de um Banco de Dados de Documentos, o serviço fará failover de modo transparente da sua conta de banco de dados e o aplicativo continuará acessando os dados sem perder a disponibilidade. Embora o Banco de Dados de Documentos ofereça [SLAs de disponibilidade de 99,99%][sla], você pode testar as propriedades de disponibilidade completa do aplicativo simulando uma falha regional, tanto de [modo programático][arm], como pelo Portal do Azure.


## Escalando em nível mundial
O Banco de Dados de Documentos permite que você provisione a produtividade de forma independente e consuma armazenamento de cada coleção do Banco de Dados de Documentos em qualquer escala, globalmente em todas regiões associadas à sua conta de banco de dados. Uma coleção do Banco de Dados de Documentos é distribuída automaticamente de forma global e gerenciada em todas regiões associadas à sua conta de banco de dados. As coleções na sua conta de banco de dados podem ser distribuídas em qualquer um das regiões do Azure na qual o [serviço do Banco de Dados de Documentos esteja disponível][serviceregions].

A produtividade comprada e o armazenamento consumido para cada coleção do Banco de Dados de Documentos são provisionados automaticamente em todas as regiões igualmente. Isso permite que o aplicativo escale perfeitamente ao redor do mundo [pagando apenas pela produtividade e pelo armazenamento que você está usando a cada hora][pricing]. Por exemplo, se você tiver provisionado 2 milhões de RUs para uma coleção do Banco de Dados de Documentos, e cada uma das regiões associadas à conta do banco de dados obtiver 2 milhões de RUs para essa coleção. Isso é ilustrado abaixo.

![Escalando a produtividade para uma coleção do Banco de Dados de Documentos entre quatro regiões][2]

O Banco de Dados de Documentos garante latências de leitura inferiores a 10 ms e de gravação inferiores a 15 ms em P99. As solicitações de leitura nunca ultrapassam o limite do datacenter para garantir os [requisitos de consistência que você selecionou][consistency]. As gravações sempre são confirmadas pelo quorum localmente antes de serem confirmadas para os clientes. Cada conta de banco de dados é configurada com prioridade de região de gravação. A região designada com prioridade mais alta atuará como a região de gravação atual para a conta. Todos os SDKs encaminharão de modo transparente as gravações da conta do banco de dados para a região de gravação atual.

Por fim, uma vez que o Banco de Dados de Documentos é completamente [independente de esquema][vldb], você nunca terá que se preocupar com o gerenciamento/a atualização de esquemas ou índices secundários em vários datacenters. Suas [consultas de SQL][sqlqueries] continuarão funcionando enquanto os modelos de aplicativo e dados continuarem evoluindo.


## Habilitando a distribuição global 

Você pode optar por tornar os dados local ou globalmente distribuídos associando uma ou mais regiões do Azure a uma conta do banco de dados do Banco de Dados de Documentos. Você pode optar por distribuir os dados globalmente ou confiná-los a uma única região adicionando ou removendo regiões em sua conta de banco de dados a qualquer momento. As contas de banco de dados do Banco de Dados de Documentos que permitem atribuição de várias regiões podem ser criadas no Azure Marketplace, bastando selecionar 'Banco de Dados de Documentos — conta de banco de dados de várias regiões'.



## Próximas etapas

Saiba mais sobre como distribuir dados globalmente com o Banco de Dados de Documentos nos seguintes artigos:

* [Provisionando de produtividade e armazenamento para uma coleção][throughputandstorage]
* [Multi-homing APIs via REST. .NET, Java, Python, and Node SDKs (APIs de hospedagem múltipla via SDKs para REST, .NET, Java, Python e Node)][developingwithmultipleregions]
* [Níveis de consistência no Banco de Dados de Documentos][consistency]
* [SLAs de disponibilidade][sla]
* [Gerenciando uma conta de banco de dados][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: https://azure.microsoft.com/documentation/articles/documentdb-partition-data/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: https://azure.microsoft.com/documentation/articles/documentdb-developing-with-multiple-regions/
[createaccount]: https://azure.microsoft.com/documentation/articles/documentdb-create-account/
[manageaccount]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/
[manageaccount-consistency]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#consistency
[manageaccount-addregion]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#addregion
[throughputandstorage]: https://azure.microsoft.com/documentation/articles/documentdb-manage/
[arm]: https://azure.microsoft.com/documentation/articles/documentdb-automation-resource-manager-cli/
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/regions/#services
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: https://azure.microsoft.com/documentation/articles/documentdb-sql-query/

<!---HONumber=AcomDC_0615_2016-->