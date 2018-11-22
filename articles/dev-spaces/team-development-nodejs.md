---
title: Equipe de desenvolvimento com Azure Dev Spaces com o VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
ms.openlocfilehash: 4b45cf6d986aa8bf404e77e7a6cf0005183f1133
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706561"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em equipe com Azure Dev Spaces

Neste tutorial, você aprenderá a usar vários espaços de desenvolvimento para trabalhar simultaneamente em ambientes de desenvolvimento diferentes, tendo o trabalho separado em espaços de desenvolvimento diferentes no mesmo cluster.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução em um contêiner separado

Nesta seção, você criará um segundo serviço, `mywebapi`, e fará com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Abrir exemplo de código para *mywebapi*
Você já deve ter o exemplo de código para `mywebapi` para esse guia em uma pasta chamada `samples` (se não, vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub). O código para essa seção está em `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Executar *mywebapi*
1. Abra a pasta `mywebapi` em uma *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Esse comando não é deve ser confundido com o `azds prep`, que configura o projeto para a implantação.
1. Pressione F5 e aguarde a criação e implantação do serviço. Você saberá que está pronto quando a barra de depuração do VS Code for exibida.
1. Anote a URL do ponto de extremidade, ele será parecida com http://localhost:\<portnumber\>. **Dica: A barra de status do VS Code exibirá uma URL clicável.** Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução em seu ambiente de desenvolvimento no Azure. O motivo para o endereço do localhost é porque `mywebapi` não definiu nenhum ponto de extremidade público e somente pode ser acessado de dentro da instância de Kubernetes. Para sua conveniência e para facilitar a interação com o serviço privado em sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contêiner em execução no Azure.
1. Quando `mywebapi` estiver pronto, abra seu navegador para o endereço do localhost. Você deve ver uma resposta do serviço `mywebapi` (“Olá do mywebapi”).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Faça uma solicitação de *webfrontend* para *mywebapi*
Vamos escrever código agora em `webfrontend`, que faz uma solicitação para `mywebapi`.
1. Alterne para a janela do VS Code para `webfrontend`.
1. Adicione estas linhas de código na parte superior do `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Substitua* o código pelo manipulador GET `/api`. Ao lidar com uma solicitação, ele faz uma chamada para `mywebapi` e, em seguida, retorna os resultados de ambos os serviços.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
 4. *Remova* a linha `server.close()` do final do `server.js`

O exemplo de código anterior encaminha o cabeçalho `azds-route-as` da solicitação de entrada para a solicitação de saída. Você verá posteriormente como isso ajuda as equipes de desenvolvimento colaborativas.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Definir um ponto de interrupção no manipulador GET `/` padrão.
1. No projeto `webfrontend`, defina um ponto de interrupção antes que ele envie uma solicitação GET para `http://mywebapi`.
1. Pressione F5 no projeto `webfrontend`.
1. Abra o aplicativo Web e percorra o código em ambos os serviços. O aplicativo Web deve exibir uma mensagem concatenada pelos dois serviços: “Olá do webfrontend e Olá do mywebapi”.

Muito bem! Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe

[!INCLUDE [](../../includes/team-development-1.md)]

Agora veja-o em ação:
1. Vá para a janela do VS Code para `mywebapi` e faça uma edição de código no manipulador GET `/` padrão, por exemplo:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>Muito bem!
Você concluiu o guia de introdução! Você aprendeu como:

> [!div class="checklist"]
> * Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
> * Desenvolva iterativamente o código em contêineres.
> * Desenvolva independentemente dois serviços separados e use a descoberta de serviço de DNS do Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolva e teste o código produtivamente em um ambiente de equipe.

Agora que você explorou o Azure Dev Spaces, [compartilhe seu espaço de desenvolvimento com um membro da equipe](how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```




