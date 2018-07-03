---
title: Criar um ambiente de desenvolvimento Node.js no Kubernetes na nuvem com VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: efd19393a661a48a566e85a058dad071c3bdb63c
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945982"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Introdução ao Azure Dev Spaces com Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Agora você está pronto para criar um ambiente de desenvolvimento baseado em Kubernetes no Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces requer somente uma configuração mínima do computador local. A maior parte da configuração do seu ambiente de desenvolvimento é armazenada na nuvem e pode ser compartilhada com outros usuários. Comece baixando e executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

> [!IMPORTANT]
> Se você já tiver a CLI do Azure instalada, verifique se está usando a versão 2.0.38 ou superior.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Você pode começar a escrever código enquanto aguarda a criação do cluster.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Criar um contêiner de Node. js no Kubernetes

Nesta seção, você criará um aplicativo Web Node. js e o executará em um contêiner no Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Criar um aplicativo Web Node.js
Baixe código do GitHub navegando até https://github.com/Azure/dev-spaces e selecionando **Clonar ou Baixar** para baixar o repositório do GitHub em seu ambiente local. O código para este guia está em `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Atualizar um arquivo de conteúdo
O Azure Dev Spaces não serve apenas para executar o código em Kubernetes; ele também serve para permitir que você veja as alterações de código entrarem em vigor de forma rápida e iterativa em um ambiente Kubernetes na nuvem.

1. Localize o arquivo `./public/index.html` e edite o HTML. Por exemplo, altere a cor da tela de fundo da página para um tom de azul:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Salve o arquivo. Em seguida, na janela do Terminal, você verá uma mensagem informando que um arquivo no contêiner em execução foi atualizado.
1. Volte para o navegador e atualize a página. Você verá a cor atualizada.

O que aconteceu? Edições em arquivos de conteúdo, como HTML e CSS, não exigem a reinicialização do processo do Node.js em portanto, um comando `azds up` ativo sincronizam automaticamente os arquivos de conteúdo modificados diretamente no contêiner em execução no Azure, permitindo uma maneira rápida de ver suas edições de conteúdo.

### <a name="test-from-a-mobile-device"></a>Testar de um dispositivo móvel
Abra o aplicativo Web em um dispositivo móvel usando a URL pública para webfrontend. Você deseja copiar e enviar a URL do desktop para seu dispositivo para evitar a inserção do endereço longo. Quando o aplicativo Web é carregado em um dispositivo móvel, você verá que a interface do usuário não é exibida corretamente em um dispositivo pequeno.

Para corrigir isso, adicione uma metamarca `viewport`:
1. Abra o arquivo `./public/index.html`
1. Adicione uma metamarca `viewport` ao elemento `head` existente:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Salve o arquivo.
1. Atualize o navegador do dispositivo. Agora você deve ver o aplicativo Web renderizado corretamente. 

Este é um exemplo de como alguns problemas não são encontramos até que você teste os dispositivos onde o aplicativo deverá ser usado. Com o Azure Dev Spaces, você pode iterar em seu código rapidamente e validar as alterações nos dispositivos de destino.

### <a name="update-a-code-file"></a>Atualizar um arquivo de código
A atualização dos arquivos de código do lado do servidor exige um pouco mais de trabalho, já que um aplicativo Node.js precisa ser reiniciado.

1. Na janela do terminal, pressione `Ctrl+C` (para parar `azds up`).
1. Abra o arquivo de código chamado `server.js` e edite a mensagem de saudação do serviço: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Salve o arquivo.
1. Execute `azds up` na janela do terminal. 

Isso recria a imagem de contêiner e reimplanta o gráfico Helm. Recarregue a página do navegador para ver as alterações de código em vigor.

Mas existe um *método ainda mais rápido* para desenvolver código, que você irá explorar na próxima seção. 

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contêiner no Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração AZDS
1. Para abrir o modo de exibição Depuração, clique no ícone Depuração na **Barra de Atividades** no lado do VS Code.
1. Selecione **Iniciar Programa (AZDS)** como a configuração de depuração ativa.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Se você não vir comandos do Azure Dev Spaces na Paleta de Comandos, verifique se [instalou a extensão do VS Code para o Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Depurar o contêiner no Kubernetes
Pressione **F5** para depurar seu código no Kubernetes!

Semelhante ao comando `up`, o código é sincronizado com o ambiente de desenvolvimento quando você inicia a depuração, e um contêiner é criado e implantado no Kubernetes. Desta vez, o depurador está anexado ao contêiner remoto.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Defina um ponto de interrupção em um arquivo de código do lado do servidor, por exemplo, dentro de `app.get('/api'...` em `server.js`. Atualize a página do navegador ou pressione o botão 'Diga Novamente' para poder alcançar o ponto de interrupção e poder percorrer o código.

Você tem acesso completo às informações de depuração exatamente como teria se o código fosse executado localmente, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção etc.

### <a name="edit-code-and-refresh-the-debug-session"></a>Editar código e atualizar a sessão de depuração
Com o depurador ativo, edite o código. Por exemplo, modifique a mensagem de saudação novamente:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Salve o arquivo e, no **painel Ações de depuração**, clique no botão **Atualizar**. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Em vez de recompilar e reimplantar uma nova imagem de contêiner sempre que houver edições de código, o que geralmente levará um tempo considerável, o Azure Dev Spaces reiniciará o processo do Node.js entre as sessões de depuração para fornecer um loop de edição/depuração mais rápido.

Atualize o aplicativo Web no navegador ou pressione o botão *Diga Novamente*. Sua mensagem personalizada deverá aparecer na interface de usuário.

### <a name="use-nodemon-to-develop-even-faster"></a>Use NodeMon para desenvolver ainda mais rapidamente
*Nodemon* é uma ferramenta popular que os desenvolvedores de Node.js usam para desenvolvimento rápido. Em vez de reiniciar manualmente o processo do Node sempre que for feita uma edição de código do lado do servidor, os desenvolvedores geralmente configurarão o projeto do Node para fazer com que o *nodemon* monitore alterações de arquivo e reinicie automaticamente o processo do servidor. Nesse estilo de trabalho, o desenvolvedor apenas atualiza o navegador depois de fazer uma edição de código.

Com o Azure Dev Spaces, você pode usar vários dos mesmos fluxos de trabalho de desenvolvimento usados no desenvolvimento local. Para ilustrar isso, o projeto de exemplo `webfrontend` foi configurado para usar *nodemon* (ele é configurado como uma dependência de desenvolvimento em `package.json`).

Experimente as etapas a seguir:
1. Pare o depurador do VS Code.
1. Clique no ícone Depuração na **Barra de Atividades** no lado do VS Code. 
1. Selecione **Anexar (AZDS)** como a configuração de depuração ativa.
1. Pressione F5.

Nessa configuração, o contêiner está configurado para iniciar o *nodemon*. Quando forem feitas edições de código do servidor, *nodemon* reinicia automaticamente o processo do Node, assim como faz quando desenvolve localmente. 
1. Edite a mensagem de saudação novamente em `server.js` e salve o arquivo.
1. Atualize o navegador ou clique no botão *Diga novamente* para ver as alterações entrarem em vigor!

**Agora você tem um método para iterar em código rapidamente e depurar diretamente no Kubernetes!** Em seguida, você verá como você pode criar e chamar um segundo contêiner.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução em um contêiner separado

Nesta seção, você criará um segundo serviço, `mywebapi`, e fará com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Abrir exemplo de código para *mywebapi*
Você já deve ter o exemplo de código para `mywebapi` para esse guia em uma pasta chamada `samples` (se não, vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub). O código para essa seção está em `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Executar *mywebapi*
1. Abra a pasta `mywebapi` em uma *janela separada do VS Code*.
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

O exemplo de código anterior encaminha o cabeçalho `azds-route-as` da solicitação de entrada para a solicitação de saída. Você verá posteriormente como isso ajuda as equipes de desenvolvimento colaborativas.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Definir um ponto de interrupção no manipulador GET `/` padrão.
1. No projeto `webfrontend`, defina um ponto de interrupção antes que ele envie uma solicitação GET para `http://mywebapi`.
1. Pressione F5 no projeto `webfrontend`.
1. Abra o aplicativo Web e percorra o código em ambos os serviços. O aplicativo Web deve exibir uma mensagem concatenada pelos dois serviços: “Olá do webfrontend e Olá do mywebapi”.

Muito bem! Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="learn-about-team-development"></a>Saiba mais sobre o desenvolvimento em equipe

[!INCLUDE[](includes/team-development-1.md)]

Agora veja-o em ação:
1. Vá para a janela do VS Code para `mywebapi` e faça uma edição de código no manipulador GET `/` padrão, por exemplo:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




