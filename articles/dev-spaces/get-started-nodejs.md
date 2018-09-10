---
title: Criar um ambiente de desenvolvimento Node.js no Kubernetes na nuvem com VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: bb8914b524846b1df5d8955bb4717873004ca4a5
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920809"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Introdução ao Azure Dev Spaces com Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Agora você está pronto para criar um ambiente de desenvolvimento baseado em Kubernetes no Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces requer somente uma configuração mínima do computador local. A maior parte da configuração do seu espaço de desenvolvimento é armazenada na nuvem e pode ser compartilhada com outros usuários. Seu computador local pode executar Windows, Mac ou Linux. Para o Linux, há suporte para as seguintes distribuições: Ubuntu (18.04, 16.04 e 14.04), Debian 8 e 9, RHEL 7, Fedora 26 +, CentOS 7, openSUSE 42.2 e SLES 12.

Comece baixando e executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Se você já tiver a CLI do Azure instalada, verifique se está usando a versão 2.0.43 ou superior.

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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento em equipe](team-development-nodejs.md)

