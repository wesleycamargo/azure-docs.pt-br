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
ms.openlocfilehash: f77a036d41ce551d9eab0250eaf4dc16444b24da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371126"
---
## <a name="build-and-run-code-in-kubernetes"></a>Compilar e executar um código no Kubernetes
Vamos executar nosso código! Na janela do terminal, execute este comando na **pasta de código raiz**, webfrontend:

```cmd
azds up
```

Fique atento à saída do comando; você verá várias coisas durante sua execução:
- O código-fonte é sincronizado com o ambiente de desenvolvimento no Azure.
- Uma imagem de contêiner é criada no Azure, conforme especificado pelos ativos do Docker na pasta de código.
- Os objetos do Kubernetes que utilizam a imagem de contêiner são criados, conforme especificado pelo gráfico do Helm na pasta de código.
- As informações sobre os pontos de extremidade do contêiner são exibidas. Em nosso caso, estamos esperando uma URL HTTP pública.
- Supondo que os estágios acima foram concluídos com êxito, você deverá começar a ver a saída de `stdout` (e `stderr`) conforme o contêiner é iniciado.

> [!Note]
> Essas etapas levarão mais tempo na primeira vez em que o comando `up` for executado, mas as execuções seguintes deverão ser mais rápidas.

## <a name="test-the-web-app"></a>Testar o aplicativo Web
Examine a saída do console para obter informações sobre a URL pública criada com o comando `up`. Ela estará neste formato: 

`Running at public URL: http://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

Abra essa URL em uma janela do navegador e você deverá ver o aplicativo Web ser carregado. Conforme o contêiner é executado, a saída de `stdout` e `stderr` é transmitida para a janela do terminal.
