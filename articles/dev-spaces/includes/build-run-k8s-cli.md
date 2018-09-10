---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: b18cfce173da562aa7cffa48f336ff623c868f21
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129189"
---
## <a name="build-and-run-code-in-kubernetes"></a>Compilar e executar um código no Kubernetes
Vamos executar nosso código! Na janela do terminal, execute este comando na **pasta de código raiz**, webfrontend:

```cmd
azds up
```

Fique atento à saída do comando; você verá várias coisas durante sua execução:
- O código-fonte é sincronizado com o espaço de desenvolvimento no Azure.
- Uma imagem de contêiner é criada no Azure, conforme especificado pelos ativos do Docker na pasta de código.
- Os objetos do Kubernetes que utilizam a imagem de contêiner são criados, conforme especificado pelo gráfico do Helm na pasta de código.
- As informações sobre os pontos de extremidade do contêiner são exibidas. Em nosso caso, estamos esperando uma URL HTTP pública.
- Supondo que os estágios acima foram concluídos com êxito, você deverá começar a ver a saída de `stdout` (e `stderr`) conforme o contêiner é iniciado.

> [!Note]
> Essas etapas levarão mais tempo na primeira vez em que o comando `up` for executado, mas as execuções seguintes deverão ser mais rápidas.

### <a name="test-the-web-app"></a>Testar o aplicativo Web
Examine a saída do console para obter informações sobre a URL pública criada com o comando `up`. Ela estará neste formato: 

`Service 'webfrontend' port 'http' is available at <url>` 

Abra essa URL em uma janela do navegador e você deverá ver o aplicativo Web ser carregado. Conforme o contêiner é executado, a saída de `stdout` e `stderr` é transmitida para a janela do terminal.

> [!Note]
> Na primeira execução, pode demorar vários minutos para o DNS público estar pronto. Se a URL pública não for resolvida, você poderá usar a URL http://localhost:<portnumber> alternativa, exibida na saída do console. Se você usar a URL de host local, poderá parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está sendo executado no AKS. Para sua conveniência e para facilitar a interação com o serviço em sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contêiner em execução no Azure. Você poderá voltar e experimentar a URL pública quando o registro DNS estiver pronto.
