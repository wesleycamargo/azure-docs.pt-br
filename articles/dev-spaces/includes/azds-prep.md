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
ms.openlocfilehash: e9f97f804985f948e5442c64a31d95e7931b03cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198819"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Preparar código para desenvolvimento Docker e Kubernetes
Até o momento, você tem um aplicativo Web básico que pode ser executado localmente. Agora, você colocará o aplicativo em contêiner criando ativos que definem o contêiner do aplicativo e como ele será implantado no Kubernetes. Isso é fácil de fazer usando o Azure Dev Spaces: 

1. Inicialize o VS Code e abra a pasta `webfrontend`. (Você pode ignorar todos os prompts padrão para adicionar ativos de depuração ou restaurar o projeto.)
1. Abra o Terminal Integrado no VS Code (usando o menu **Exibir > Terminal Integrado**).
1. Execute esse comando (certifique-se de que **webfrontend** seja a pasta atual):

    ```cmd
    azds prep --public
    ```

O comando `azds prep` da CLI do Azure gera ativos de Docker e Kubernetes com configurações padrão:
* `./Dockerfile` descreve a imagem de contêiner do aplicativo e como o código-fonte é compilado e executado no contêiner.
* Um [Gráfico Helm](https://docs.helm.sh) em `./charts/webfrontend` descreve como implantar o contêiner no Kubernetes.

Por enquanto, não é necessário entender o conteúdo completo desses arquivos. Vale ressaltar, no entanto, que **os mesmos ativos de configuração como código do Kubernetes e do Docker podem ser usados desde o desenvolvimento até a produção, fornecendo assim melhor consistência em diferentes ambientes.**
 
Um arquivo nomeado `./azds.yaml` também é gerado pelo comando `prep` e é o arquivo de configuração dos Azure Dev Spaces. Ele complementa os artefatos do Docker e do Kubernetes com configuração adicional que permite uma experiência de desenvolvimento iterativo no Azure. Por exemplo, o gráfico Helm padrão não expõe nenhum ponto de extremidade público. Às vezes, no entanto, é útil abrir temporariamente um ponto de extremidade público durante o desenvolvimento para que seja possível testar o código, digamos, em um dispositivo móvel ou em uma URL do Webhook. Um arquivo azds.yaml criado usando `azds prep --public` substitui os parâmetros padrão do Helm para expor um ponto de extremidade público apenas para o tempo de desenvolvimento.
