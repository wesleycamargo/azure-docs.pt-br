---
title: Criar um plano de continuidade de negócios para seu serviço QnA Maker - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Como criar um plano de continuidade de negócios para o seu serviço QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364219"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Criar um plano de continuidade de negócios para o seu serviço QnA Maker

O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.

![Plano de backup do QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível como representada acima é a seguinte:

1. Configurar dois [serviços QnA Maker](../How-To/set-up-qnamaker-service-azure.md) em paralelo nas [regiões emparelhadas do Azure](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Manter os índices de pesquisa primários e secundários do Azure em sincronia. Usar o exemplo do github [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como fazer o backup e a restauração dos índices do Azure.

3. Fazer backup do Application Insights usando [exportação contínua](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. Depois que as pilhas primárias e secundárias forem configuradas, use o [gerenciador de tráfego](https://docs.microsoft.com/en-us/azure/traffic-manager/) para configurar os dois pontos de extremidade e configurar um método de roteamento.

5. Você precisará criar um certificado SSL para o ponto de extremidade do gerenciador de tráfego. [Associar o certificado SSL](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) nos serviços de Aplicativo.

6. Por fim, use o ponto de extremidade do gerenciador de tráfego em seu bot ou aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolher a capacidade para sua implantação do QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
