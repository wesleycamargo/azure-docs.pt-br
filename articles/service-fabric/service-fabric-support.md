---
title: "Saiba mais sobre as opções de Suporte do Azure Service Fabric | Microsoft Docs"
description: "Versões de cluster do Azure Service Fabric com suporte e links para transmitir tíquetes de suporte."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 05e433b028762016759637b2afa8c741b04f31ff
ms.openlocfilehash: a31a297f909ffb1c6da748623b0050415c43486b


---
# <a name="azure-service-fabric-support-options"></a>Opções de suporte do Azure Service Fabric

Para fornecer o suporte apropriado para os clusters do Service Fabric nos quais suas cargas de trabalho do aplicativo são executadas, organizamos várias opções para você. Dependendo do nível de suporte necessário e da severidade do problema, escolha as opções ideais. 


<a id="getlivesitesupportonazure"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-azure"></a>Relatar problemas de produção ou de site ativo ou solicitar o suporte pago para o Azure

Para relatar problemas de site ativo no cluster do Service Fabric implantado no Azure, abra um tíquete de suporte profissional [no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ou no [portal de suporte da Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:
 
- [Suporte Profissional da Microsoft para o Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Relatar problemas de produção ou de site ativo ou solicitar o suporte pago para clusters independentes do Service Fabric

Para relatar problemas de site ativo no cluster do Service Fabric implantado localmente ou em outras nuvens, abra um tíquete de suporte profissional no [portal de suporte da Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Saiba mais sobre:

- [Suporte Profissional da Microsoft para o local](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Suporte premier da Microsoft](https://support.microsoft.com/en-us/premier).


<a id="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>Relatar problemas do Azure Service Fabric 
Organizamos um repositório GitHub para relatar problemas do Service Fabric.  Também estamos monitorando de forma ativa os fóruns a seguir.

### <a name="github-repo"></a>Repositório GitHub 
Relate problemas do Azure Service Fabric no [repositório Git de problemas do Service Fabric](https://github.com/Azure/service-fabric-issues). Esse repositório destina-se a relatar e acompanhar problemas com o Azure Service Fabric e a ser usado para fazer pequenas solicitações de recursos. **Não use o repositório para relatar problemas de site ativo**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow e fóruns do MSDN

A [marcação Service Fabric no StackOverflow][stackoverflow] e o [fórum do Service Fabric no MSDN][msdn-forum] devem ser usados para fazer perguntas sobre o funcionamento da plataforma e como determinadas tarefas podem ser realizadas com ela.

### <a name="azure-feedback-forum"></a>Fórum de Comentários do Azure

O [Fórum de Comentários do Azure sobre o Service Fabric][uservoice-forum] é o melhor local para enviar grandes ideias de recursos para o produto, pois examinamos as solicitações mais populares como parte de nosso planejamento de médio a longo prazo. Incentivamos você a conseguir suporte para suas sugestões na comunidade.


<a id="releasesuport"></a>
## <a name="supported-service-fabric-versions"></a>Versões do Service Fabric com suporte.

Verifique se o cluster sempre executa uma versão do Service Fabric com suporte. Quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir da data do comunicado. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os documentos a seguir para obter detalhes sobre como manter o cluster executando uma versão do Service Fabric com suporte.

- [Atualizar a versão do Service Fabric em um cluster do Azure ](service-fabric-cluster-upgrade.md)
- [Atualizar a versão do Service Fabric em um cluster de servidores independente do Windows Server ](service-fabric-cluster-upgrade-windows-server.md)
 
Veja a seguir a lista das versões do Service Fabric com suporte e as datas de fim de suporte.

| **Cluster de tempo de execução do Service Fabric** | **Data de fim do suporte** |
| --- | --- |
| Todas as versões de cluster anteriores a 5.3.121 |20 de janeiro de 2017 |
| 5.3.* |24 de fevereiro de 2017 |
| 5.4.* |17 de abril de 2017     |
| 5.5.* |Versão atual e, portanto, sem data de término


## <a name="next-steps"></a>Próximas etapas

- [Atualizar a versão do Service Fabric em um cluster do Azure ](service-fabric-cluster-upgrade.md)
- [Atualizar a versão do Service Fabric em um cluster de servidores independente do Windows Server ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples



<!--HONumber=Feb17_HO3-->


