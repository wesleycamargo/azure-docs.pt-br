---
title: Capacidade de recursos para implantação – QnA Maker
titleSuffix: Azure Cognitive Services
description: Um guia para escolher a capacidade para sua implantação do QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 01548cf2de8db8f4dc9984598a5e5544bf97fd49
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432640"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolher a capacidade para sua implantação do QnA Maker

O serviço QnA Maker depende de três recursos do Azure:
1.  Serviço de Aplicativo (para o tempo de execução)
2.  Azure Search (para armazenar QnAs)
3.  App Insights (opcional, para armazenar telemetria e logs de chat)

Antes de criar seu serviço QnA Maker, é preciso decidir qual camada dos serviços acima são adequadas para você. 

Normalmente, há três parâmetros que você precisa considerar:
1. **A taxa de transferência de que você precisa do serviço**: selecione as [Plano de Aplicativo](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) para o serviço de aplicativo com base em suas necessidades. Você pode [escalar verticalmente](https://docs.microsoft.com/azure/app-service/web-sites-scale) ou reduzir verticalmente o Aplicativo. Isso também deve influenciar a seleção da SKU do Azure Search, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

2. **Tamanho e o número de bases de dados de conhecimento**: escolha a [SKU de pesquisa do Azure](https://azure.microsoft.com/en-in/pricing/details/search/) apropriada para seu cenário. Você pode publicar as bases de dados de conhecimento N-1 em uma camada específica, onde N representa os índices máximos permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

3. **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento do QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs a três (cada uma com 1 MB de tamanho). O padrão de SKU não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                        | Gerenciamento do QnA Maker | Serviço de Aplicativo | Azure Search | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Camada Gratuita   | Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| Ambiente de Desenvolvimento/Teste   | SKU Standard         | Compartilhado      | Basic        | Publicar até 14 KBs, com tamanho de 2 GB    |
| Ambiente de Produção | SKU Standard         | Basic       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

Para fazer upgrade de sua pilha do QnA Maker, veja [Fazer upgrade do seu serviço QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Fazer upgrade do seu serviço QnA Maker](../How-To/upgrade-qnamaker-service.md)
