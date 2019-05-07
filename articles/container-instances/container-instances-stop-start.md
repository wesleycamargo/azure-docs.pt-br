---
title: Interromper ou iniciar contêineres em instâncias de contêiner do Azure manualmente
description: Saiba como interromper ou iniciar um grupo de contêineres em instâncias de contêiner do Azure manualmente.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 8e62d106a42dfbec897e5e14cf68fd3d7fd823c4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070809"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Interromper ou iniciar contêineres em instâncias de contêiner do Azure manualmente

O [política de reinicialização](container-instances-restart-policy.md) configuração de um grupo de contêiner determina como as instâncias de contêiner iniciar ou parar por padrão. Você pode substituir a configuração por manualmente parar ou iniciar um grupo de contêiner padrão.

## <a name="stop"></a>Stop

Pare manualmente um grupo de contêineres em execução - por exemplo, usando o [parar de contêiner do az] [ az-container-stop] comando ou o portal do Azure. Para determinadas cargas de trabalho de contêiner, você talvez queira parar um grupo de contêineres de longa execução após um período definido para economizar nos custos. 

*Quando um grupo de contêineres entra no estado parado, ele encerra e recicla todos os contêineres no grupo. Ele não preserva o estado do contêiner.*

Quando os contêineres são reciclados, o [recursos](container-instances-container-groups.md#resource-allocation) são desalocadas e a cobrança é interrompido para o grupo de contêineres.

A ação de parada não tem nenhum efeito se o grupo de contêineres já finalizado (está no estado de um êxito ou falha). Por exemplo, um grupo de contêiner com as tarefas de execução única de contêiner que foi executado com êxito termina com o estado de êxito. Tenta interromper o grupo de estado não alteram o estado. 

## <a name="start"></a>Iniciar

Quando um grupo de contêineres é interrompido - porque os contêineres concluídos por conta própria ou é interrompido manualmente o grupo – você pode iniciar os contêineres. Por exemplo, use o [início do contêiner az] [ az-container-start] comando ou o portal do Azure para iniciar manualmente os contêineres no grupo. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será extraída. 

Iniciar um grupo de contêineres inicia uma nova implantação com a mesma configuração de contêiner. Essa ação pode ajudar você a reutilizar rapidamente uma configuração de grupo de contêineres conhecido que funciona conforme o esperado. Você não precisa criar um novo grupo de contêineres para executar a mesma carga de trabalho.

Todos os contêineres em um grupo de contêineres são iniciados por esta ação. Você não pode iniciar um contêiner específico no grupo.

Após você iniciar ou reiniciar manualmente um grupo de contêineres, o grupo de contêineres é executado de acordo com a política de reinicialização configurada.
  
## <a name="restart"></a>Reiniciar

Você pode reiniciar um grupo de contêineres, enquanto ele está em execução - por exemplo, usando o [reinicialização de contêiner do az] [ az-container-restart] comando. Essa ação reinicia todos os contêineres no grupo de contêineres. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será extraída. 

Reiniciar um grupo de contêineres é útil quando você deseja solucionar um problema de implantação. Por exemplo, se uma limitação de recursos temporária impedir que seus contêineres sejam executados com êxito, reiniciar o grupo poderá resolver o problema.

Todos os contêineres em um grupo de contêineres são reiniciados por esta ação. Você não poderá reiniciar um contêiner específico no grupo.

Depois de reiniciar manualmente um grupo de contêineres, o grupo de contêiner é executado acordo com o configurado política de reinicialização.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [reiniciar as configurações de política](container-instances-restart-policy.md) nas instâncias de contêiner do Azure.

Além de manualmente parar e iniciar um grupo de contêineres com a configuração existente, você pode [atualize as configurações de](container-instances-update.md) de um grupo de contêineres em execução.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
