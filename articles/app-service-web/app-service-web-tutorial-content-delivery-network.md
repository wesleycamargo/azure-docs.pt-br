---
title: "Conectar um aplicativo Web a uma Rede de distribuição de conteúdo | Microsoft Docs"
description: "Conecte um aplicativo Web a uma Rede de distribuição de conteúdo para entregar os arquivos estáticos dos nós de borda."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a12eab1f7bc4177f659771d58a58f749507c994c
ms.lasthandoff: 04/12/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Conectar um aplicativo Web a uma Rede de distribuição de conteúdo

Neste tutorial, você criará um perfil e um ponto de extremidade de CDN do Azure para fornecer os arquivos estáticos de seu aplicativo Web por meio de locais pop da CDN do Azure.

> [!TIP]
> Revise a lista atualizada dos [Locais POP da CDN do Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Etapa 1: Fazer logon no Portal do Azure

Primeiro, abra seu navegador favorito e navegue até o [Portal](https://portal.azure.com) do Azure.

## <a name="step-2---create-a-cdn-profile"></a>Etapa 2: Criar um perfil de CDN

Clique no botão `+ New` no painel de navegação esquerdo, clique em **Web + Móvel**. Na categoria Web + Móvel, selecione **CDN**.

Especifique a **Nome**, o **Local**, o **Grupo de recursos**, **Tipo de preço** e clique em **Criar**.

Abra o hub de grupos de recursos do painel de navegação esquerdo e selecione **myResourceGroup**. Na lista de recursos, selecione **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Etapa 3: Criar um novo ponto de extremidade de CDN

Clique em `+ Endpoint` nos comandos ao lado da caixa de pesquisa para iniciar a folha de Criação do ponto de extremidade.

Especifique o **Nome**, o **Tipo de origem**, o **Nome do host de origem** e clique em **Adicionar**.

O Ponto de extremidade será criado e, após a criação do ponto de extremidade da Rede de distribuição de conteúdo, o status será atualizado para **executando**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Etapa 4: Aproveitar a CDN

Agora que o ponto de extremidade está em execução, vamos verificar se o conteúdo está disponível no servidor pop navegando até um arquivo estático no servidor Web e, depois, alterando o nome do host de `http://<app_name>.azurewebsites.net/path/to-static-file` para `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Próximas etapas


