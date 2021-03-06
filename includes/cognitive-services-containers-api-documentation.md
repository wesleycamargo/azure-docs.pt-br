---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522496"
---
## <a name="validate-container-is-running"></a>Validar contêiner está em execução 

Há várias maneiras para validar o contêiner está em execução: 

|Solicitação|Finalidade|
|--|--|
|`http://localhost:5000/`|O contêiner não fornece uma home page.|
|`http://localhost:5000/status`|Solicitado com GET validar o contêiner está em execução sem fazer com que uma consulta de ponto de extremidade. Isso pode ser usado para o Kubernetes [testes de preparação e execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|O contêiner fornece um conjunto completo de documentação para os pontos de extremidade, bem como um recurso `Try it now`. Esse recurso permite que você insira suas configurações em um formulário HTML baseado na Web e faça a consulta sem precisar escrever nenhum código. Depois que a consulta é retornada, um exemplo de comando CURL é fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. |

![Homepage do contêiner](./media/cognitive-services-containers-api-documentation/container-webpage.png)
