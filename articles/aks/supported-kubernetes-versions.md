---
title: Versões do Kubernetes com suporte no Serviço de Kubernetes do Azure
description: Entender a política de suporte de versão do Kubernetes e o ciclo de vida dos clusters no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: 6b55825107ae8872b146b3ad4fde0ef4b917b71d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045555"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões do Kubernetes com suporte no AKS (Serviço de Kubernetes do Azure)

A comunidade Kubernetes libera versões secundárias aproximadamente a cada três meses. Essas versões incluem novos recursos e melhorias. As versões de patch são mais frequentes (às vezes, semanais) e são destinadas apenas a correções de bugs críticas em uma versão secundária. Essas versões de patch incluem correções de vulnerabilidades de segurança ou de bugs importantes que afetam um grande número de clientes e produtos em execução na produção com base no Kubernetes.

Uma nova versão secundária do Kubernetes foi disponibilizada em [acs-engine][acs-engine] no primeiro dia. O SLO (objetivo de nível de serviço) do AKS é lançar a versão secundária dos clusters do AKS em até 30 dias, sujeito à estabilidade da versão.

## <a name="kubernetes-version-support-policy"></a>Política de suporte de versão do Kubernetes

O AKS dá suporte a quatro versões secundárias do Kubernetes:

- A versão secundária atual que é lançada upstream (n)
- Três versões secundárias anteriores. Cada versão secundária com suporte também dá suporte a dois patches estáveis.

Por exemplo, se o AKS introduz a *1.11.x* hoje, ele também dá suporte para *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f* (em que as versões de patch indicadas com letras são os dois builds estáveis mais recentes).

Quando uma nova versão secundária é introduzida, as versões secundárias e de patch mais antigas com suporte são desativadas. 15 dias antes do lançamento da nova versão secundária e da futura desativação da versão, um comunicado é feito por meio dos canais de atualização do Azure. No exemplo acima, em que *1.11.x* é lançado, as versões desativadas são *1.7.g* + *1.7.h*.

Quando você implanta um cluster do AKS no portal ou com a CLI do Azure, o cluster é sempre definido como a versão secundária n-1 e o patch mais recente. Por exemplo, se o AKS dá suporte à *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f*, a versão padrão dos novos clusters é *1.10.b*.

## <a name="faq"></a>Perguntas frequentes

**O que acontece quando um cliente atualiza um cluster do Kubernetes com uma versão secundária sem suporte?**

Se estiver usando a versão *n-4*, você estará fora do SLO. Se a atualização da versão n-4 para a n-3 for bem-sucedida, você retornará ao SLO. Por exemplo: 

- Se as versões com suporte do AKS forem *1.10.a* + *1.10.b*, *1.9.c* + *1.9d*, *1.8.e* + *1.8f* e você estiver usando *1.7.g* ou *1.7.h*, você estará fora do SLO.
- Se a atualização da *1.7.g* ou *1.7.h* para *1.8.e* ou *1.8.f* for bem-sucedida, você retornará ao SLO.

Não há suporte para atualizações para versões anteriores à *n-4*. Nesses casos, é recomendado que os clientes criem clusters do AKS e reimplantem suas cargas de trabalho.

**O que acontece quando um cliente dimensiona um cluster do Kubernetes com uma versão secundária sem suporte?**

Para as versões secundárias sem suporte no AKS, a escala ou a redução horizontal continua a funcionar sem problemas.

**Um cliente pode permanecer em uma versão do Kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver em uma das versões com suporte no AKS, o cluster estará fora do SLO do AKS. O Azure não atualiza nem exclui o cluster automaticamente.

**A qual versão a mestre dá suporte quando o cluster do agente não está em uma das versões com suporte do AKS?**

O mestre é atualizado automaticamente para a versão mais recente com suporte.

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como atualizar seu cluster, confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-upgrade].

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md