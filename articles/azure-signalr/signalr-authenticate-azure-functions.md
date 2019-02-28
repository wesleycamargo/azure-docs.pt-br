---
title: 'Tutorial: Autenticação do Serviço Azure SignalR com o Azure Functions'
description: Neste tutorial, você aprenderá como autenticar clientes de serviço do Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 0ed30b9ad38db71798edd75ee133a08670fd33fe
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890191"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Tutorial: Autenticação do Serviço Azure SignalR com o Azure Functions

Um tutorial passo a passo para criar uma sala de chat com autenticação e mensagens privadas usando o Azure Functions, a Autenticação do Serviço de Aplicativo e o Serviço SignalR.

## <a name="introduction"></a>Introdução

### <a name="technologies-used"></a>Tecnologias usadas

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): API de back-end para autenticar usuários e enviar mensagens de chat
* [Serviço Azure SignalR](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): transmite novas mensagens para clientes de chat conectados
* [Armazenamento do Azure](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): hospeda o site estático para a interface do usuário do cliente de chat

### <a name="prerequisites"></a>Pré-requisitos

O software a seguir é necessário para criar este tutorial.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (Versão 10.x)
* [SDK do .NET](https://www.microsoft.com/net/download) (versão 2.x, obrigatório para extensões do Functions)
* [Ferramentas básicas do Azure Functions](https://github.com/Azure/azure-functions-core-tools) (Versão 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) com as extensões a seguir
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions): funciona com o Azure Functions no VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer): fornece páginas da Web localmente para teste

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Vá para o [portal do Azure](https://portal.azure.com/) e entre com suas credenciais.

## <a name="create-an-azure-signalr-service-instance"></a>Criar uma instância do Serviço Azure SignalR

Você vai compilar e testar o aplicativo do Azure Functions localmente. O aplicativo acessará uma instância do Serviço SignalR no Azure que precisa ser criada antecipadamente.

1. Clique no botão **Criar um recurso** (**+**) para criar um novo recurso do Azure.

1. Pesquise **Serviço SignalR** e selecione-o. Clique em **Criar**.

    ![Novo Serviço SignalR](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Insira as seguintes informações.

    | NOME | Valor |
    |---|---|
    | Nome do recurso | Um nome exclusivo para a instância do Serviço SignalR |
    | Grupo de recursos | Criar um novo grupo de recursos |
    | Local padrão | Escolha um local perto de você |
    | Camada de preços | Grátis |

1. Clique em **Criar**.

## <a name="initialize-the-function-app"></a>Inicializar o aplicativo de funções

### <a name="create-a-new-azure-functions-project"></a>Criar um novo projeto do Azure Functions

1. Em uma nova janela do VS Code, use `File > Open Folder` no menu para criar e abrir uma pasta vazia em um local apropriado. Essa será a pasta de projeto principal do aplicativo que você criará.

1. Usando a extensão do Azure Functions no VS Code, inicialize um aplicativo de funções na pasta do projeto principal.
    1. Abra a paleta de comandos no VS Code selecionando **Exibição > Paleta de Comandos** no menu (atalho `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
    1. Pesquise o comando **Azure Functions: Criar Novo Projeto** e selecione-o.
    1. A pasta de projeto principal deve aparecer. Selecione-a (ou use "Procurar" para localizá-la).
    1. No prompt para escolher uma linguagem, selecione **JavaScript**.

    ![Criar um aplicativo de funções](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Instalar extensões do aplicativo de funções

Este tutorial usa as associações do Azure Functions para interagir com o Serviço Azure SignalR. Como a maioria das outras associações, as associações do Serviço SignalR estão disponíveis como uma extensão que precisa ser instalada usando a CLI das Ferramentas Essenciais do Azure Functions antes de poderem ser usadas.

1. Abra um terminal no VS Code selecionando **Exibir > Terminal Integrado** no menu (Ctrl-\`).

1. Verifique se a pasta de projeto principal é o diretório atual.

1. Instale a extensão do aplicativo de funções do Serviço SignalR.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Definir as configurações do aplicativo

Ao executar e depurar o tempo de execução do Azure Functions localmente, as configurações do aplicativo são lidas de **local.settings.json**. Atualize esse arquivo com a cadeia de conexão da instância do Serviço SignalR que você criou anteriormente.

1. No VS Code, selecione **local.settings.json** no painel do Explorer para abri-lo.

1. Substitua o conteúdo pelo código a seguir.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Insira a cadeia de conexão de Serviço Azure SignalR em uma configuração chamada `AzureSignalRConnectionString`. Obter o valor da página **Chaves** no recurso do Serviço Azure SignalR no portal do Azure; a cadeia de conexão primária ou secundária pode ser usada.
    * A configuração `WEBSITE_NODE_DEFAULT_VERSION` não é usada localmente, mas é obrigatória quando implantada no Azure.
    * A seção `Host` configura a porta e as configurações de CORS para o host do Functions local (essa configuração não tem efeito quando em execução no Azure).

    ![Obter chave do Serviço SignalR](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Salve o arquivo.

    ![Atualizar configurações locais](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Crie uma função para autenticar usuários no Serviço SignalR

Quando o aplicativo de chat é aberto pela primeira vez no navegador, ele exige credenciais de conexão válidas para se conectar ao Serviço Azure SignalR. Você criará uma função disparada por HTTP chamada *SignalRInfo* no aplicativo de funções para retornar essas informações de conexão.

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Pesquise e selecione o comando **Azure Functions: Criar Função**.

1. Quando solicitado, forneça as seguintes informações:

    | NOME | Valor |
    |---|---|
    | Pasta do aplicativo de funções | Selecionar a pasta de projeto principal |
    | Modelo | Gatilho de HTTP |
    | NOME | SignalRInfo |
    | Nível de autorização | Anônima |

    Uma pasta chamada **SignalRInfo** é criada contendo a nova função.

1. Abra **SignalRInfo/function.json** para configurar associações para a função. Modifique o conteúdo do arquivo como mostrado abaixo. Isso adiciona uma associação de entrada que gera credenciais válidas para um cliente poder se conectar a um hub do Serviço Azure SignalR denominado `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    A propriedade `userId` na associação `signalRConnectionInfo` é usada para criar uma conexão com o Serviço SignalR autenticada. Deixe a propriedade em branco no desenvolvimento local. Você irá usá-la quando o aplicativo de funções for implantado no Azure.

1. Abra **SignalRInfo/index.js** para exibir o corpo da função. Modifique o conteúdo do arquivo como mostrado abaixo.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Essa função usa as informações de conexão SignalR da associação de entrada e a retorna para o cliente no corpo da resposta HTTP.

## <a name="create-a-function-to-send-chat-messages"></a>Criar uma função para enviar mensagens de chat

O aplicativo Web também requer uma API HTTP para enviar mensagens de chat. Você criará uma função disparada por HTTP nomeada *SendMessage* que envia mensagens para todos os clientes conectados usando Serviço SignalR.

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Pesquise e selecione o comando **Azure Functions: Criar Função**.

1. Quando solicitado, forneça as seguintes informações:

    | NOME | Valor |
    |---|---|
    | Pasta do aplicativo de funções | selecione a pasta de projeto principal |
    | Modelo | Gatilho de HTTP |
    | NOME | SendMessage |
    | Nível de autorização | Anônima |

    Uma pasta chamada **SendMessage** é criada contendo a nova função.

1. Abra **SendMessage/function.json** para configurar associações para a função. Modifique o conteúdo do arquivo como mostrado abaixo.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Isso cria duas alterações na função original:
    * Altera a rota para `messages` e restringe o gatilho HTTP para método HTTP **POST**.
    * Adiciona uma associação de saída do Serviço SignalR que envia mensagens para todos os clientes conectados a um hub do Serviço SignalR denominado `chat`.

1. Salve o arquivo.

1. Abra **SendMessage/index.js** para exibir o corpo da função. Modifique o conteúdo do arquivo como mostrado abaixo.

    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```

    Essa função usa o corpo da solicitação HTTP e o envia para clientes conectados ao Serviço SignalR, invocando uma função chamada `newMessage` em cada cliente.

    A função pode ler a identidade do remetente e pode aceitar um valor de *destinatário* no corpo da mensagem para permitir que uma mensagem seja enviada em particular a um único usuário. Essas funcionalidades serão usadas posteriormente no tutorial.

1. Salve o arquivo.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Criar e executar a interface do usuário Web do cliente de chat

A interface do usuário do aplicativo de chat é um SPA (aplicativo de página única) criado com a estrutura Vue JavaScript. Ele será hospedado separadamente do aplicativo de funções. Localmente, você executará a interface da Web usando a extensão VS Code do Live Server.

1. No VS Code, crie uma nova pasta chamada **conteúdo** na raiz da pasta do projeto principal.

1. Na pasta **conteúdo**, crie um novo arquivo chamado **index.html**.

1. Copie e cole o conteúdo de **[index. HTML](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Salve o arquivo.

1. Pressione **F5** para executar o aplicativo de funções localmente e anexar um depurador.

1. Com o **index.html** aberto, inicie o Live Server abrindo a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) e selecionando **Live Server: Abrir com Live Server**. O Live Server abrirá o aplicativo em um navegador.

1. O aplicativo é aberto. Digite uma mensagem na caixa de chat e pressione Enter. Atualize o aplicativo para ver novas mensagens. Como nenhuma autenticação foi configurada, todas as mensagens serão enviadas como "anônimo".

## <a name="deploy-to-azure-and-enable-authentication"></a>Implantar no Azure e habilitar autenticação

Você estava executando o aplicativo de chat e o aplicativo de funções localmente. Agora você os implantará no Azure e habilitará a autenticação e o sistema de mensagens privadas no aplicativo.

### <a name="log-into-azure-with-vs-code"></a>Faça logon no Azure com o VS Code

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Pesquise e selecione o comando **Azure: Entrar**.

1. Siga as instruções na tela para concluir o processo de entrada em seu navegador.

### <a name="configure-function-app-for-authentication"></a>Configurar o aplicativo de funções para autenticação

Até agora, o aplicativo de chat funciona anonimamente. No Azure, você usará a [Autenticação do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) para autenticar o usuário. A ID ou o nome do usuário autenticado pode ser passado para a associação *SignalRConnectionInfo* para gerar informações de conexão que são autenticadas como o usuário.

Quando uma mensagem é enviada, o aplicativo pode decidir se deseja enviá-la para todos os clientes conectados ou somente para os clientes que foram autenticados em determinado usuário.

1. No VS Code, abra **SignalRInfo/function.json**.

1. Insira uma [expressão de associação](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings) na propriedade *userId* da associação *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Isso define o valor para o nome de usuário do usuário autenticado. O atributo deve ficar assim.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Salve o arquivo.

### <a name="deploy-function-app"></a>Implantar o aplicativo de funções

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) e selecione **Azure Functions: Implantar para Aplicativo de Funções**.

1. Quando solicitado, forneça as seguintes informações:

    | NOME | Valor |
    |---|---|
    | Pasta para implantar | Selecionar a pasta de projeto principal |
    | Assinatura | Selecione sua assinatura |
    | Aplicativo de funções | Selecione **Criar Novo Aplicativo de Funções** |
    | Nome do aplicativo de funções | Insira um nome exclusivo |
    | Grupo de recursos | Selecione o mesmo grupo de recursos da instância do Serviço SignalR |
    | Conta de armazenamento | Selecione **Criar nova conta de armazenamento** |
    | Nome da conta de armazenamento | Insira um nome exclusivo (3 a 24 caracteres, somente alfanuméricos) |
    | Local padrão | Escolha um local perto de você |

    Um novo aplicativo de funções é criado no Azure e a implantação é iniciada. Aguarde até que a implantação seja concluída.

### <a name="upload-function-app-local-settings"></a>Carregar configurações locais do aplicativo de funções

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Pesquise e selecione o comando **Azure Functions: Carregar configurações locais**.

1. Quando solicitado, forneça as seguintes informações:

    | NOME | Valor |
    |---|---|
    | Arquivo de configurações local | local.settings.json |
    | Assinatura | Selecione sua assinatura |
    | Aplicativo de funções | Selecione o aplicativo de funções implantado anteriormente |
    | Nome do aplicativo de funções | Insira um nome exclusivo |

As configurações locais são carregadas para o aplicativo de funções no Azure. Se solicitado a substituir as configurações existentes, selecione **Sim para todos**.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Habilitar o CORS (compartilhamento de recursos entre origens) do aplicativo de funções

Embora haja uma configuração de CORS no **local.settings.json**, ela não será propagada no aplicativo de funções no Azure. Você precisará defini-la separadamente.

1. Abra a paleta de comandos do VS Code (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Pesquise e selecione o comando **Azure Functions: Abrir no portal**.

1. Selecione a assinatura e o nome do aplicativo de funções para abrir o aplicativo de funções no portal do Azure.

1. No portal que foi aberto, na guia **Recursos da plataforma**, selecione **CORS**.

    ![Localizar CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Adicione uma entrada com o valor `*`.

1. Remova todas as outras entradas existentes.

1. Clique em **Salvar** para persistir as configurações de CORS.

    ![Definir CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> Em um aplicativo real, em vez de permitir CORS em todas as origens (`*`), uma abordagem mais segura é inserir entradas específicas de CORS para cada domínio que precisa dele.

### <a name="update-the-web-app"></a>Atualizar o aplicativo Web

1. No portal do Azure, navegue até a página de visão geral do aplicativo de funções.

1. Copie a URL do aplicativo de funções.

    ![Obter URL](media/signalr-authenticate-azure-functions/signalr-get-url.png)

1. No VS Code, abra **index.html** e substitua o valor de `apiBaseUrl` pela URL do aplicativo de funções.

1. O aplicativo pode ser configurado com autenticação usando Azure Active Directory, Facebook, Twitter, Conta da Microsoft ou Google. Selecione o provedor de autenticação que será usado definindo o valor de `authProvider`.

1. Salve o arquivo.

### <a name="deploy-the-web-application-to-blob-storage"></a>Implantar o aplicativo Web no armazenamento de blobs

O aplicativo Web será hospedado usando o recurso de sites estáticos do Armazenamento de Blobs do Azure.

1. Clique no botão **Novo** (**+**) para criar um novo recurso do Azure.

1. Em **Armazenamento**, selecione **Conta de armazenamento**.

1. Insira as seguintes informações.

    | NOME | Valor |
    |---|---|
    | NOME | Um nome exclusivo para a conta de armazenamento de blobs |
    | Tipo de conta | StorageV2 (Uso geral V2) |
    | Local padrão | Selecione a mesma região de seus outros recursos |
    | Replicação | Armazenamento com redundância local (LRS) |
    | Desempenho | Standard |
    | Camada de acesso | Dinâmica |
    | Grupo de recursos | Selecione o mesmo grupo de recursos dos outros recursos neste tutorial |

1. Clique em **Criar**.

    ![Criar armazenamento](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. Quando a conta de armazenamento for criada, abra-a no portal do Azure.

1. Selecione **Site estático (versão prévia)** no painel de navegação à esquerda.

1. Selecione **Habilitar**.

1. Digite `index.html` tal como está escrito o **nome do documento de índice**.

1. Clique em **Salvar**.

    ![Configurar sites estáticos](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Clique no link **$web** na página para abrir o contêiner de blobs.

1. Clique em **Carregar** e carregue **index.html** na pasta **conteúdo**.

1. Volte para a página **Site estático**. Anote o **Ponto de extremidade primário**. Essa é a URL do aplicativo Web.

### <a name="enable-app-service-authentication"></a>Habilitar autenticação do Serviço de Aplicativo

A Autenticação do Serviço de Aplicativo dá suporte à autenticação com Azure Active Directory, Facebook, Twitter, Conta da Microsoft e Google.

1. Com o aplicativo de funções ainda aberto no portal, localize a guia **Recursos da plataforma** e selecione **Autenticação/Autorização**.

1. **Habilite** a Autenticação do Serviço de Aplicativo.

1. Em **Ação a ser tomada quando a solicitação não for autenticada**, selecione "Fazer logon com {provedor de autenticação que você selecionou anteriormente}".

1. Em **URLs de redirecionamento externo Permitidas**, insira a URL do ponto de extremidade Web primário de sua conta de armazenamento anotada anteriormente.

1. Siga a documentação do provedor de logon de sua escolha para concluir a configuração.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Conta da Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="try-the-application"></a>Experimentar o aplicativo

1. Em um navegador, navegue até o ponto de extremidade Web primário da conta de armazenamento.

1. Selecione **Login** para autenticar com o provedor de autenticação escolhido.

1. Envie mensagens públicas inserindo-as na caixa de chat principal.

1. Envie mensagens privadas clicando em um nome de usuário no histórico de chats. Somente o destinatário selecionado receberá essas mensagens.

Parabéns! Você implantou um aplicativo de chat em tempo real sem servidor!

![Demonstração](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados neste tutorial, exclua o grupo de recursos usando o portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o Azure Functions com o Serviço Azure SignalR. Leia mais sobre a criação de aplicativos sem servidor em tempo real com associações do Serviço SignalR para o Azure Functions.

> [!div class="nextstepaction"]
> [Compilar aplicativos em tempo real com o Azure Functions](signalr-overview-azure-functions.md)
