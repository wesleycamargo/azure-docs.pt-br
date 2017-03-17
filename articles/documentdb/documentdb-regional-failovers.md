---
title: Failovers regionais no Azure DocumentDB | Microsoft Docs
description: "Saiba mais sobre como failovers manuais e automáticos funcionam com o Azure DocumentDB."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 4af4d30a3378e1aea66309a1d757be1c1da2ea0d
ms.lasthandoff: 03/07/2017


---
# <a name="automatic-regional-failovers-for-business-continuity-in-documentdb"></a>Failovers regionais automáticos para continuidade de negócios no DocumentDB
O Azure DocumentDB simplifica a distribuição global de dados oferecendo [contas de banco de dados em várias regiões](documentdb-distribute-data-globally.md) totalmente gerenciadas que fornecem claras compensações entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. As contas do DocumentDB oferecem alta disponibilidade, latências de milissegundos de digito único, [níveis bem definidos de consistência](documentdb-consistency-levels.md), failover regional transparente com APIs de hospedagem múltipla e a capacidade de ajustar elasticamente escala de taxa de transferência e armazenamento ao redor do mundo. 

O Azure DocumentDB dá suporte tanto a failovers explícitos quanto àqueles controlados por política, que permitem controlar o comportamento do sistema de ponta a ponta em caso de falhas. Neste artigo, veremos:

* Como funcionam os failovers manuais no DocumentDB?
* Como funcionam os failovers automáticos no DocumentDB?
* Como você pode usar failovers manuais em arquiteturas de aplicativo?

Saiba mais sobre failovers regionais nesse vídeo do Azure Friday com Scott Hanselman e o gerente de engenharia Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a id="ConfigureMultiRegionApplications"></a>Configurando aplicativos de várias regiões
Antes de nos aprofundarmos nos modos de failover, examinaremos como você pode configurar um aplicativo para aproveitar a disponibilidade de várias regiões e ser resiliente diante de failovers regionais.

* Primeiro, implante seu aplicativo em várias regiões
* Para garantir o acesso de baixa latência de todas as regiões nas quais seu aplicativo é implantado, configure a [lista de regiões preferenciais](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) correspondente para cada região por meio de um dos SDKs com suporte.

O trecho a seguir mostra como inicializar um aplicativo de várias regiões. Aqui, a conta do DocumentDB `contoso.documents.azure.com` está configurada com duas regiões – Oeste dos EUA e Europa Setentrional. 

* O aplicativo é implantado na região Oeste dos EUA (usando os Serviços de Aplicativos do Azure, por exemplo) 
* Configurado com `West US` como a primeira região preferencial para leituras de baixa latência
* Configurado com `North Europe` como a segunda região preferencial (para alta disponibilidade durante falhas regionais)

No .NET, essa configuração se parece com o trecho de código a seguir:

    ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
    { 
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    };

    usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
    usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient usClient = new DocumentClient(
        new Uri("https://contosodb.documents.azure.com"),
        "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
        usConnectionPolicy);

O aplicativo também é implantado na região da Europa Setentrional, com a ordem das regiões preferenciais invertida. Ou seja, a região da Europa Setentrional é especificada primeiro para leituras de baixa latência. Em seguida, a região Oeste dos EUA é especificada como a segunda região preferencial para alta disponibilidade durante falhas regionais.

O diagrama de arquitetura a seguir mostra uma implantação de aplicativo de várias regiões em que o DocumentDB e o aplicativo estão configurados para estarem disponíveis em quatro regiões geográficas do Azure.  

![Implantação de aplicativos globalmente distribuídos com o Azure DocumentDB](./media/documentdb-regional-failovers/app-deployment.png)

Agora, vejamos como o serviço DocumentDB lida com falhas regionais por meio de failovers automáticos. 

## <a id="AutomaticFailovers"></a>Failovers Automáticos
No caso de uma interrupção regional do Azure, o DocumentDB aciona automaticamente failovers de todas as contas de DocumentDB com uma presença na região afetada. 

**O que acontece se uma região de leitura sofre uma interrupção?**

As contas do DocumentDB com uma região de leitura em uma das regiões afetadas são automaticamente desconectadas de sua região de gravação e marcadas como offline. Os SDKs do DocumentDB implementam um protocolo de descoberta regionais que permite que eles detectem automaticamente quando uma região está disponível e redirecionem chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. Nenhuma alteração é necessária no código do aplicativo para tratar de failovers regionais. Durante todo esse processo, garantias de consistência continuam a ser cumpridas pelo DocumentDB.

![Falhas de região de leitura no Azure DocumentDB](./media/documentdb-regional-failovers/read-region-failures.png)

Depois que a região afetada se recupera da interrupção, todas as contas de DocumentDB afetadas na região são recuperadas automaticamente pelo serviço. Contas do DocumentDB que tinham uma região de leitura na região afetada sincronizarão automaticamente com a região de gravação atual e passarão a ficar online. Os SDKs do DocumentDB descobrem a disponibilidade da nova região e avaliam se ela deve ser selecionada como a região de leitura atual com base na lista de regiões preferenciais configurada pelo aplicativo. Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo.

**O que acontece se uma região de gravação sofre uma interrupção?**

Se a região afetada for a região de gravação atual de uma determinada conta do Azure DocumentDB, essa região será automaticamente marcada como offline. Em seguida, uma região alternativa será promovida a região de gravação de cada conta do DocumentDB afetada. Você pode controlar totalmente a ordem de seleção de região para suas contas do DocumentDB por meio do Portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Prioridades de failover para o Azure DocumentDB](./media/documentdb-regional-failovers/failover-priorities.png)

Durante failovers automáticos, o DocumentDB escolhe automaticamente a próxima região de gravação para uma determinada conta do Azure DocumentDB com base na ordem de prioridade especificada. 

![Falhas de região de gravação no Azure DocumentDB](./media/documentdb-regional-failovers/write-region-failures.png)

Depois que a região afetada se recupera da interrupção, todas as contas de DocumentDB afetadas na região são recuperadas automaticamente pelo serviço. 

* As contas do DocumentDB com sua região de gravação anterior na área afetada permanecerão em um modo offline com disponibilidade de leitura mesmo após a recuperação da região. 
* Você pode consultar essa região para computar quaisquer gravações não replicadas durante a interrupção por meio da comparação dos dados presentes nela com os dados disponíveis na região de gravação atual. Com base nas necessidades de seu aplicativo, você pode executar a mesclagem e/ou resolução de conflitos e gravar o conjunto final de alterações de volta na região de gravação atual. 
* Depois de concluir as alterações de mesclagem, você pode colocar a região afetada online novamente, removendo e readicionando a região à sua conta do DocumentDB. Quando a região é adicionada novamente, você pode configurá-lo como a região de gravação, executando um failover manual por meio do Portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

## <a id="ManualFailovers"></a>Failovers Manuais

Além de failovers automáticos, a região de gravação atual de uma determinada conta do DocumentDB pode ser alterada manualmente de modo dinâmico para uma das regiões de leitura existente. Failovers manuais podem ser iniciados por meio do Portal do Azure ou [programaticamente](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Failovers manuais asseguram **zero perda de dados** e **zero perda de disponibilidade** e, na conta do DocumentDB especificada, transferem normalmente o status de gravação da região de gravação antiga para a nova. Como em failovers automáticos, o SDK do Azure DocumentDB manipula automaticamente as alterações de região de gravação durante failovers manuais e garante que as chamadas sejam redirecionadas automaticamente para a nova região de gravação. Nenhuma alteração de configuração ou código é necessária em seu aplicativo para gerenciar failovers. 

![Failovers manuais no Azure DocumentDB](./media/documentdb-regional-failovers/manual-failovers.png)

Estes são alguns dos cenários comuns em que o failover manual pode ser útil:

**Modelo siga o relógio**: se os aplicativos tiverem padrões de tráfego previsíveis com base na hora do dia, você poderá alterar periodicamente o status de gravação para a região geográfica mais ativa com base na hora do dia.

**Atualização de serviço**: determinada implantação de aplicativos distribuídos globalmente pode envolver o redirecionamento de tráfego para uma região diferente por meio do Gerenciador de Tráfego durante sua atualização de serviço planejada. Essa implantação de aplicativo agora pode usar o failover manual para manter o status de gravação para a região em que haverá tráfego ativo durante a janela de tempo da atualização de serviço.

**Simulações de BCDR (continuidade dos negócios e recuperação de desastre) detalhada**: a maioria dos aplicativos empresariais incluem testes de continuidade de negócios como parte do seu processo de desenvolvimento e lançamento. Testes de BCDR geralmente são uma etapa importante no que se refere a certificações de conformidade e garantia da disponibilidade do serviço no caso de interrupções regionais. Você pode testar a preparação para BCDR de seus aplicativos que usam o DocumentDB para armazenamento disparando um failover manual de sua conta do DocumentDB e/ou adicionando e removendo uma região dinamicamente.

Neste artigo, examinamos como failovers manuais e automáticos funcionam no Azure DocumentDB e como você pode configurar suas contas e aplicativos do DocumentDB para que estejam disponíveis globalmente. Usando o suporte de replicação global do Azure DocumentDB, você pode melhorar a latência de ponta a ponta e garantir que eles estejam altamente disponíveis, até mesmo no caso de falhas regionais. 

## <a id="NextSteps"></a>Próximas etapas
* Saiba mais sobre como o DocumentDB dá suporte à [distribuição global](documentdb-distribute-data-globally.md)
* Saiba mais sobre [consistência global com o DocumentDB](documentdb-consistency-levels.md)
* Desenvolver com várias regiões usando o [SDK do Azure DocumentDB](documentdb-developing-with-multiple-regions.md)
* Aprenda a criar [arquiteturas de gravador de várias regiões](documentdb-multi-region-writers.md) com o Azure DocumentDB


