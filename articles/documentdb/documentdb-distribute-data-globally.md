---
title: Distribuir dados globalmente com o DocumentDB | Microsoft Docs
description: "Saiba mais sobre replicação geográfica em nível mundial, failover e recuperação de dados usando bancos de dados globais do Banco de Dados de Documentos do Azure, um serviço de banco de dados NoSQL totalmente gerenciado."
services: documentdb
documentationcenter: 
author: kiratp
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: kipandya
translationtype: Human Translation
ms.sourcegitcommit: 2d833a559b72569983340972ba3b905b9e42e61d
ms.openlocfilehash: 4147bf0a5f4a3c41de53ef5b3e05d8409f9a8c87


---
# <a name="distribute-data-globally-with-documentdb"></a>Distribute data globally with DocumentDBs (Distribuir dados globalmente com o Banco de Dados de Documentos)
> [!NOTE]
> A distribuição global dos bancos de dados do Banco de Dados de Documentos geralmente é disponibilizada e habilitada automaticamente para contas do Banco de Dados de Documentos recentemente criadas. Estamos trabalhando para habilitar a distribuição global em todas as contas existentes, mas nesse ínterim, se quiser que a distribuição global seja habilitada para sua conta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e nós a habilitaremos para você agora mesmo.
> 
> 

O Banco de Dados de Documentos foi desenvolvido para atender às necessidades de aplicativos IoT que consistem em milhões de dispositivos globalmente distribuídos e aplicativos de escala de Internet que proporcionam experiências altamente responsivas aos usuários no mundo todo. Esses sistemas de banco de dados enfrentam o desafio de conseguir acesso de baixa latência a dados de aplicativo de várias regiões geográficas com garantias bem definidas de consistência e disponibilidade de dados. Como um sistema de banco de dados globalmente distribuído, o Banco de Dados de Documentos simplifica a distribuição global de dados oferecendo contas de banco de dados em várias regiões totalmente gerenciadas que fornecem claras compensações entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. As contas de banco de dados do DocumentDB são oferecidas com alta disponibilidade, latências de milissegundos de digito único, vários [níveis bem definidos de coerência][consistency], failover regional transparente com APIs de hospedagem múltipla e a capacidade de escalar elasticamente a produtividade e o armazenamento ao redor do mundo. 

Recomendamos começar assistindo ao vídeo a seguir, em que Karthik Raman explica a distribuição geográfica com o Azure DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]
> 
> 

## <a name="configuring-multi-region-accounts"></a>Configurando contas de várias regiões
A configuração da conta do Banco de Dados de Documentos para escalar no nível mundial pode ser realizada em menos de um minuto pelo [Portal do Azure](documentdb-portal-global-replication.md). Tudo o que você precisa fazer é selecionar o nível certo de consistência entre os diversos níveis de consistência bem definidos compatíveis, bem como associar qualquer número de regiões do Azure à sua conta de banco de dados. Os níveis de consistência do Banco de Dados de Documentos fornecem claras compensações entre garantia de consistência específica e desempenho. 

![O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha][1]

O Banco de Dados de Documentos oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha.

A seleção do nível certo de consistência depende da garantia da consistência dos dados de que seu aplicativo precisa. O Banco de Dados de Documentos replica automaticamente os dados em todas as regiões especificadas e garante a consistência que você selecionou para sua conta de banco de dados. 

## <a name="using-multi-region-failover"></a>Usando o failover de várias regiões
O Azure DocumentDB pode executar failover de modo transparente de contas de banco de dados em várias regiões do Azure – as novas [APIs de hospedagem múltipla][developingwithmultipleregions] garantem que o aplicativo pode continuar usando um ponto de extremidade lógico e não é interrompido pelo failover. O failover é controlado por você, fornecendo a flexibilidade para hospedar novamente sua conta de banco de dados no caso de ocorrer qualquer condição de falha, incluindo falhas de aplicativo, infraestrutura, serviço ou regionais (reais ou simuladas). Em caso de falha regional de um Banco de Dados de Documentos, o serviço fará failover de modo transparente da sua conta de banco de dados e o aplicativo continuará acessando os dados sem perder a disponibilidade. Embora o DocumentDB ofereça [SLAs de disponibilidade de 99,99%][sla], você poderá testar as propriedades de disponibilidade de ponta a ponta do aplicativo simulando uma falha regional, tanto de [forma programática][arm], como pelo Portal do Azure.

## <a name="scaling-across-the-planet"></a>Escalando em nível mundial
O Banco de Dados de Documentos permite que você provisione a produtividade de forma independente e consuma armazenamento de cada coleção do Banco de Dados de Documentos em qualquer escala, globalmente em todas regiões associadas à sua conta de banco de dados. Uma coleção do Banco de Dados de Documentos é distribuída automaticamente de forma global e gerenciada em todas regiões associadas à sua conta de banco de dados. As coleções de sua conta de banco de dados podem ser distribuídas em qualquer uma das regiões do Azure na qual o [serviço do DocumentDB está disponível][serviceregions]. 

A produtividade comprada e o armazenamento consumido para cada coleção do Banco de Dados de Documentos são provisionados automaticamente em todas as regiões igualmente. Isso permite que o aplicativo seja escalado diretamente em todo o mundo [apenas pelo preço da produtividade e do armazenamento usados por hora][preço]. Por exemplo, se você tiver provisionado 2 milhões de RUs para uma coleção do Banco de Dados de Documentos, e cada uma das regiões associadas à conta do banco de dados obtiver 2 milhões de RUs para essa coleção. Isso é ilustrado abaixo.

![Escalando a produtividade para uma coleção do Banco de Dados de Documentos entre quatro regiões][2]

O Banco de Dados de Documentos garante latências de leitura inferiores a 10 ms e de gravação inferiores a 15 ms em P99. As solicitações de leitura nunca ultrapassam o limite do datacenter para garantir os [requisitos de coerência selecionados][consistency]. As gravações sempre são confirmadas pelo quorum localmente antes de serem confirmadas para os clientes. Cada conta de banco de dados é configurada com prioridade de região de gravação. A região designada com prioridade mais alta atuará como a região de gravação atual para a conta. Todos os SDKs encaminharão de modo transparente as gravações da conta do banco de dados para a região de gravação atual. 

Para concluir, já que o DocumentDB é completamente [independente de esquema][vldb], você nunca precisará se preocupar com o gerenciamento ou a atualização de esquemas ou de índices secundários em vários datacenters. Suas [consultas SQL][sqlqueries] continuarão funcionando enquanto os modelos de aplicativo e dados continuarem evoluindo. 

## <a name="enabling-global-distribution"></a>Habilitando a distribuição global
Você pode optar por tornar os dados local ou globalmente distribuídos associando uma ou mais regiões do Azure a uma conta do banco de dados do Banco de Dados de Documentos. Você pode adicionar ou remover regiões à sua conta de banco de dados a qualquer momento. Para habilitar a distribuição global usando o portal, veja [Como executar a replicação global de banco de dados do Banco de Dados de Documentos usando o Portal do Azure](documentdb-portal-global-replication.md). Para habilitar a distribuição global de forma programática, consulte [Desenvolvendo com contas do DocumentDB de várias regiões](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como distribuir dados globalmente com o Banco de Dados de Documentos nos seguintes artigos:

* [Provisionando a produtividade e o armazenamento para uma coleção][throughputandstorage]
* [APIs de Hospedagem múltipla por meio de SDKs da REST, do .NET, Java, Python e Node][developingwithmultipleregions]
* [Níveis de coerência no DocumentDB][consistency]
* [SLAs de disponibilidade][sla]
* [Gerenciando uma conta de banco de dados][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regiões]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[preço]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md




<!--HONumber=Nov16_HO3-->


