---
title: A escolha da capacidade para sua implantação do QnA Maker - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: um guia para escolher a capacidade para sua implantação do QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364245"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolher a capacidade para sua implantação do QnA Maker

O serviço QnA Maker depende de três recursos do Azure:
1.  Serviço de Aplicativo (para o tempo de execução)
2.  Azure Search (para armazenar QnAs)
3.  Informações do aplicativo (opcionais, para armazenar chatlogs e telemetria)

Antes de criar seu serviço QnA Maker, você deve decidir quais camadas dos serviços acima são adequadas. 

Normalmente, há três parâmetros que você precisa considerar:
1. **A taxa de transferência de que você precisa do serviço**: selecione as [Plano de Aplicativo](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) para o serviço de aplicativo com base em suas necessidades. Você pode [escalar verticalmente](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) ou reduzir verticalmente o Aplicativo. Isso também deve influenciar a seleção da SKU do Azure Search, veja mais detalhes [aqui](https://docs.microsoft.com/en-us/azure/search/search-sku-tier).

2. **Tamanho e o número de bases de dados de conhecimento**: escolha a [SKU de pesquisa do Azure](https://azure.microsoft.com/en-in/pricing/details/search/) apropriada para seu cenário. Você pode publicar as bases de dados de conhecimento N-1 em uma camada específica, onde N representa os índices máximos permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

3. **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento do QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs a 3 (cada uma com 1 MB de tamanho). O padrão de SKU não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                        | Gerenciamento do QnA Maker | Serviço de Aplicativo | Azure Search | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Camada Gratuita   | Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| Ambiente de Desenvolvimento/Teste   | SKU Standard         | Compartilhado      | Basic        | Publicar até 4 KBs, tamanho de 2GB    |
| Ambiente de Produção | SKU Standard         | Basic       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

Para fazer upgrade de sua pilha do QnA Maker, veja [Fazer upgrade do seu serviço QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Fazer upgrade do seu serviço QnA Maker](../How-To/upgrade-qnamaker-service.md)
