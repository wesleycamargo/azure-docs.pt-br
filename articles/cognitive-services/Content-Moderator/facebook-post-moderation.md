---
title: 'Tutorial: moderação de conteúdo do Facebook – Azure Content Moderator'
titlesuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a usar o Content Moderator baseado em aprendizado de máquina para ajudar a moderar postagens e comentários do Facebook.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: b43f3645a2fb9cc6ede4b27b4412b9e70e2b26f8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213830"
---
# <a name="tutorial-facebook-content-moderation-with-content-moderator"></a>Tutorial: Moderação de conteúdo do Facebook com o Content Moderator

Neste tutorial, você aprenderá a usar o Content Moderator baseado em aprendizado de máquina para ajudar a moderar postagens e comentários do Facebook.

Este tutorial orienta pelas etapas a seguir:

1. Criar uma equipe no Content Moderator.
2. Criar Azure Functions que escuta para eventos HTTP do Content Moderator e Facebook.
3. Criar uma página e aplicativo do Facebook e conectá-los ao Content Moderator.

Depois que terminar, o Facebook enviará o conteúdo publicado por visitantes ao Content Moderator. Com base nos limites de correspondência, seus fluxos do Content Moderator publica o conteúdo ou cria análises dentro da ferramenta de análise. 

A figura a seguir mostra os blocos de construção da solução.

![Moderação de postagem do Facebook](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Criar uma equipe no Content Moderator

Consulte o início rápido [Experimentar o Content Moderator na Web](quick-start.md) para se inscrever no Content Moderator e criar uma equipe.

## <a name="configure-image-moderation-workflow-threshold"></a>Configurar o fluxo de trabalho de moderação de imagem (limite)

Consultar a página [Fluxos de trabalho](review-tool-user-guide/workflows.md) para configurar um fluxo de trabalho de imagem personalizada (limite). Observar o nome do fluxo de **trabalho**.

## <a name="3-configure-text-moderation-workflow-threshold"></a>3. Configurar o fluxo de trabalho de moderação de texto (limite)

Usar etapas semelhantes à página [Fluxos de Trabalho](review-tool-user-guide/workflows.md) para configurar um limite de texto personalizado e o fluxo de trabalho. Observar o nome do fluxo de **trabalho**.

![Configurar Fluxo de trabalho de Texto](images/text-workflow-configure.PNG)

Testar seu fluxo de trabalho usando o botão "Executar fluxo de trabalho".

![Testar o Fluxo de trabalho de Texto](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Criar Azure Functions

Entrar no [Portal de Gerenciamento do Microsoft Azure](https://portal.azure.com/) para criar o Azure Functions. Siga estas etapas:

1. Crie um Aplicativo de funções do Azure, conforme mostrado na página [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
2. Abra o aplicativo de funções recém-criado.
3. Dentro do aplicativo, navegue até **Recursos de plataforma -> Configurações de aplicativo**
4. Defina as seguintes [configurações de aplicativo](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings):

> [!NOTE]
> O **cm: Região** deve ser o nome da região (sem espaços).
> Por exemplo, **europaocidental**, não Europa Ocidental, **centraloestedoseua**, não Central-Oeste dos EUA e assim por diante.
>

| Configurações de Aplicativo | DESCRIÇÃO   | 
| -------------------- |-------------|
| cm:TeamId   | ID da sua equipe do Content Moderator  | 
| cm:SubscriptionKey | Sua chave de assinatura Content Moderator - Consulte [Credenciais](review-tool-user-guide/credentials.md) | 
| cm:Região | O nome da região do Content Moderator, sem espaços. Consulte a observação anterior. |
| cm:ImageWorkflow | Nome do fluxo de trabalho para executar em Imagens |
| cm:TextWorkflow | Nome do fluxo de trabalho para executar em Texto |
| cm:CallbackEndpoint | URL para o aplicativo de funções CMListener que você criará mais adiante neste guia |
| fb:VerificationToken | O token de segurança, também é usado para assinar os eventos do feed do Facebook |
| fb:PageAccessToken | O token de acesso de grafo do Facebook não expira e permite a função Ocultar/Excluir as postagens em seu nome. |

5. Criar uma nova função **HttpTrigger CSharp** nomeada **FBListener**. Esta função recebe eventos do Facebook. Crie esta função seguindo estas etapas:

    1. Mantenha a página [Criação do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) aberta para referência.
    2. Clique em **+** adicionar para criar a nova função.
    3. Em vez de modelos internos, escolha **Comece por conta própria em sua opção de função própria/personalizada**.
    4. Clique na peça que diz **HttpTrigger-CSharp**.
    5. Digite o nome **FBListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    6. Clique em **Criar**.
    7. Substitua o conteúdo do **run.csx** com o conteúdo do [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Crie uma nova função **HttpTrigger-CSharp** nomeada **CMListener**. Esta função recebe eventos do Content Moderator. Execute estas etapas para criar esta função.

    1. Mantenha a página [Criação do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) aberta para referência.
    2. Clique em **+** adicionar para criar a nova função.
    3. Em vez de modelos internos, escolha **Comece por conta própria em sua opção de função própria/personalizada**.
    4. Clique ne peça que diz **HttpTrigger-CSharp**
    5. Digite o nome **CMListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    6. Clique em **Criar**.
    7. Substitua o conteúdo do **run.csx** com o conteúdo do [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).

## <a name="configure-the-facebook-page-and-app"></a>Configure o aplicativo e página do Facebook
1. Crie um aplicativo do Facebook.

    1. Navegue até o [site de desenvolvedor do Facebook](https://developers.facebook.com/)
    2. Clique em **Meus Aplicativos**.
    3. Adicionar um Novo Aplicativo.
    4. Selecione **Webhooks -> Introdução**
    5. Selecione **Página -> Inscrever-se neste tópico**
    6. Forneça a **URL FBListener** como a URL de retorno de chamada e o **Verificar Token** configurado em**Configurações do Aplicativo de Funções**
    7. Depois que se inscreveu, vá até o feed e selecione **assinar**.

2. Crie uma página do Facebook.

    1. Navegue até [Facebook](https://www.facebook.com/bookmarks/pages) e crie uma nova **página do Facebook**.
    2. Permita que o aplicativo do Facebook acesse esta página seguindo estas etapas:
        1. Navegue até o [Explorer do API do Graph](https://developers.facebook.com/tools/explorer/).
        2. Selecione **Aplicativo**.
        3. Selecione o **Token de Acesso da Página**, envie uma solicitação **Obter**.
        4. Clique em **ID de Página** na resposta.
        5. Agora acrescente **/subscribed_apps** à URL e Envie uma solicitação **Obter** (resposta vazia).
        6. Envie uma solicitação de **Postagem**. Obtenha a resposta como **sucesso: verdadeiro**.

3. Crie um token de acesso de API do Graph sem expiração.

    1. Navegue até o [Explorer do API do Graph](https://developers.facebook.com/tools/explorer/).
    2. Selecione a opção do **Aplicativo**.
    3. Selecione a opção **Obter Token de Acesso do Usuário**.
    4. Em **Selecionar Permissões**, selecione as opções **manage_pages** e **publish_pages**.
    5. Usaremos o **token de acesso** (Token de Vida Útil Curta) na próxima etapa.

4. Usamos o Portman para as próximas etapas.

    1. Abra o **Postman** (ou obtenha-o [aqui](https://www.getpostman.com/)).
    2. Importe esses dois arquivos:
        1. [Coleção do Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Ambiente Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Atualize essas variável de ambiente:
    
    | Chave | Valor   | 
    | -------------------- |-------------|
    | appId   | Insira seu Identificador do Aplicativo do Facebook aqui  | 
    | appSecret | Insira seu segredo do aplicativo do Facebook aqui | 
    | short_lived_token | Insira o token de acesso de usuário de vida útil curta gerado na etapa anterior |
    4. Agora execute as 3 APIs listadas na coleção: 
        1. Selecione **Gerar Token de acesso de vida útil longa** e clique em **Enviar**.
        2. Selecione **Obter a ID do usuário** e clique em **Enviar**.
        3. Selecione **Obter Token de acesso da página permanente** e clique em **Enviar**.
    5. Copie o valor **access_token** da resposta e atribua-a à configuração do aplicativo, **fb:PageAccessToken**.

É isso!

A solução envia todas as imagens e texto publicados na sua página do Facebook para o Content Moderator. Os fluxos de trabalho que você configurou anteriormente são chamados. O conteúdo que não passa pelos critérios definidos em fluxos de trabalho resulta em análises dentro da ferramenta de análise. O restante do conteúdo é publicado.

## <a name="license"></a>Licença

Todos os SDKs dos Serviços Cognitivos da Microsoft e exemplos são licenciados com a licença do MIT. Para obter mais detalhes, consulte [LICENÇA](https://microsoft.mit-license.org/).

## <a name="next-steps"></a>Próximas etapas

1. [Assista a uma demonstração (vídeo)](https://channel9.msdn.com/Events/Build/2017/T6033) dessa solução da Microsoft Build 2017.
1. [Amostra do Facebook no GitHub](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
