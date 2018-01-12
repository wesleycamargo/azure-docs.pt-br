---
title: "Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph | Microsoft Docs"
description: "Saiba como consultar o Microsoft Graph para obter uma lista de eventos de risco e as informações associadas do Azure Active Directory."
services: active-directory
keywords: "azure active directory identity protection, evento de risco, vulnerabilidade, política de segurança, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: fafad74f46baaf56a8220dab05028781b2f2258e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph
O Microsoft Graph é o ponto de extremidade de API unificado da Microsoft e a página inicial das APIs do [Azure Active Directory Identity Protection](active-directory-identityprotection.md). A primeira API, **identityRiskEvents**, permite que você consulte o Microsoft Graph para obter uma lista de [eventos de risco](active-directory-identityprotection-risk-events-types.md) e informações associadas. Este artigo mostra como começar a consultar essa API. Para obter uma introdução detalhada, a documentação completa e acesso ao Explorador do Graph, consulte o [site do Microsoft Graph](https://graph.microsoft.io/).


Há quatro etapas para acessar dados de Proteção de Identidade por meio do Microsoft Graph:

1. Recupere seu nome de domínio.
2. Crie um novo registro de aplicativo. 
2. Use esse segredo e algumas outras informações para se autenticar no Microsoft Graph, onde você recebe um token de autenticação. 
3. Use esse token para fazer solicitações para o ponto de extremidade de API e para obter dados do Identity Protection.

Antes de começar, será necessário:

* Privilégios de administrador para criar o aplicativo no Azure AD
* O nome do domínio do seu locatário (por exemplo, contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Recuperar seu nome de domínio 

1. [Entre](https://portal.azure.com) no Portal do Azure como administrador. 

2. No painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. Na seção **Gerenciar**, clique em **Propriedades**.

    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Copie seu nome de domínio.


## <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

1. Na página **Active Directory**, na seção **Gerenciar**, clique em **Registros de aplicativo**.

    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. No menu na parte superior, clique em **Novo registro do aplicativo**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Na página **Criar**, realize as seguintes etapas:
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. Na caixa de texto **Nome** , digite um nome para seu aplicativo (por exemplo: AADIP Aplicativo de API de Evento de Risco).
   
    b. Como **Tipo**, selecione **Aplicativo Web E/Ou API Web**.
   
    c. Na caixa de texto **URL de Entrada**, digite `http://localhost`.

    d. Clique em **Criar**.

4. Para abrir a página **Configurações**, na lista de aplicativos, clique no registro de aplicativo recém-criado. 

5. Copie a **ID do Aplicativo**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda permissão ao aplicativo para usar a API

1. Na página **Configurações**, clique em **Permissões necessárias**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Na página **Permissões necessárias**, na barra de ferramentas na parte superior, clique em **Adicionar**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. Na página **Selecionar uma API**, selecione **Microsoft Graph** e clique em **Selecionar**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Na página **Adicionar acesso à API**, clique em **Selecionar permissões**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. Na página **Habilitar acesso**, clique em **Ler todas as informações de risco de identidade** e clique em **Selecionar**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Na página **Adicionar acesso à API**, clique em **Concluído**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Na página **Permissões necessárias**, clique em **Conceder Permissões** e em **Sim**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Obter uma chave de acesso

1. Na página **Configurações**, clique em **Chaves**.
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Na página **Chaves**, execute as seguintes etapas:
   
    ![Criação de um aplicativo](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. Na caixa de texto **Descrição da chave**, digite uma descrição (por exemplo, *AADIP Risk Event*).
    
    b. Como **Duração**, selecione **Em um ano**.

    c. Clique em **Salvar**.
   
    d. Copie o valor da chave e cole em um local seguro.   
   
   > [!NOTE]
   > Se você perder esta chave, precisará retornar a esta seção e criar uma nova chave. Mantenha essa chave em segredo: qualquer pessoa que a tenha poderá acessar seus dados.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticar-se no Microsoft Graph e consultar a API de Eventos de Risco de Identidade
Neste ponto, você deve ter:

- O nome do domínio do locatário

- A ID do cliente 

- A chave 


Para autenticar, envie uma solicitação post para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: “**client_credentials**”

-  resource: “**https://graph.microsoft.com**”

- client_id: \<a ID do cliente\>

- client_secret: \<a chave\>


Se for bem-sucedido, será retornado um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

    `Authorization`=”<token_type> <access_token>"


Durante a autenticação, você poderá encontrar o tipo de token e o token de acesso no token retornado.

Envie este cabeçalho como uma solicitação para a seguinte URL de API: `https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se obtiver êxito, será uma coleção de eventos de risco de identidade e de dados associados no formato JSON OData, que poderá ser analisado e manipulado como for melhor.

Veja um código de exemplo para autenticação e chamada da API usando o PowerShell.  
Basta adicionar sua ID do cliente, a chave secreta e o domínio do locatário.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Próximas etapas

Parabéns, você acabou de criar sua primeira chamada para o Microsoft Graph!  
Agora você pode consultar os eventos de risco de identidade e usar os dados como quiser.

Para saber mais sobre o Microsoft Graph e como criar aplicativos usando a API do Graph, confira a [documentação](https://graph.microsoft.io/docs) e muito mais no [site Microsoft Graph](https://graph.microsoft.io/). Além disso, marque a página [API do Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) que lista todas as APIs do Identity Protection disponíveis no Graph. À medida que adicionarmos novas maneiras de trabalhar com o Identity Protection via API, você as verá nessa página.

Para obter informações relacionadas. consulte:

-  [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

-  [Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Visão geral do Microsoft Graph](https://graph.microsoft.io/docs)

- [Raiz do Serviço Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

