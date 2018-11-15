---
title: Microsoft Graph para o Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como consultar o Microsoft Graph para obter uma lista de eventos de risco e as informações associadas do Azure Active Directory.
services: active-directory
keywords: azure active directory identity protection, evento de risco, vulnerabilidade, política de segurança, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: a415258b8127bb78d8a8d1b5ef228234b34b3e26
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287499"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph
O Microsoft Graph é o ponto de extremidade de API unificado da Microsoft e a página inicial das APIs do [Azure Active Directory Identity Protection](../active-directory-identityprotection.md). A primeira API, **identityRiskEvents**, permite que você consulte o Microsoft Graph para obter uma lista de [eventos de risco](../reports-monitoring/concept-risk-events.md) e informações associadas. Este artigo mostra como começar a consultar essa API. Para obter uma introdução detalhada, a documentação completa e acesso ao Explorador do Graph, consulte o [site do Microsoft Graph](https://developer.microsoft.com/graph/).


Há quatro etapas para acessar dados de Proteção de Identidade por meio do Microsoft Graph:

1. Recupere seu nome de domínio.
2. Crie um novo registro de aplicativo. 
2. Use esse segredo e algumas outras informações para se autenticar no Microsoft Graph, onde você recebe um token de autenticação. 
3. Use esse token para fazer solicitações para o ponto de extremidade de API e para obter dados do Identity Protection.

Antes de começar, será necessário:

- An Azure AD P2 tenant

- Privilégios de administrador para criar o aplicativo no Azure AD

- O nome do domínio do seu locatário (por exemplo, contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Recuperar seu nome de domínio 

1. [Entre](https://portal.azure.com) no Portal do Azure como administrador. 

2. No painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Criação de um aplicativo](./media/graph-get-started/41.png)

3. Clique em **nomes de domínio personalizados**.

    ![Nomes de domínio personalizados](./media/graph-get-started/71.png)

4. Na lista de nomes de domínio, copie o nome de domínio sinalizado como principal.

    ![Nomes de domínio personalizados](./media/graph-get-started/72.png)



## <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

1. Na página **Active Directory**, na seção **Gerenciar**, clique em **Registros de aplicativo**.

    ![Criação de um aplicativo](./media/graph-get-started/42.png)


2. No menu na parte superior, clique em **Novo registro do aplicativo**.
   
    ![Criação de um aplicativo](./media/graph-get-started/43.png)

3. Na página **Criar**, realize as seguintes etapas:
   
    ![Criação de um aplicativo](./media/graph-get-started/44.png)

    a. Na caixa de texto **Nome** , digite um nome para seu aplicativo (por exemplo: AADIP Aplicativo de API de Evento de Risco).
   
    b. Como **tipo de aplicativo**, selecione **Web Application e / ou Web API**.
   
    c. Na caixa de texto **URL de Entrada**, digite `http://localhost`.

    d. Clique em **Criar**.

4. Para abrir a página **Configurações**, na lista de aplicativos, clique no registro de aplicativo recém-criado. 

5. Copie a **ID do Aplicativo**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda permissão ao aplicativo para usar a API

1. Na página **Configurações**, clique em **Permissões necessárias**.
   
    ![Criação de um aplicativo](./media/graph-get-started/15.png)

2. Na página **Permissões necessárias**, na barra de ferramentas na parte superior, clique em **Adicionar**.
   
    ![Criação de um aplicativo](./media/graph-get-started/16.png)
   
3. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.
   
    ![Criação de um aplicativo](./media/graph-get-started/17.png)

4. Na página **Selecionar uma API**, selecione **Microsoft Graph** e clique em **Selecionar**.
   
    ![Criação de um aplicativo](./media/graph-get-started/18.png)

5. Na página **Adicionar acesso à API**, clique em **Selecionar permissões**.
   
    ![Criação de um aplicativo](./media/graph-get-started/19.png)

6. Na página **Habilitar acesso**, clique em **Ler todas as informações de risco de identidade** e clique em **Selecionar**.
   
    ![Criação de um aplicativo](./media/graph-get-started/20.png)

7. Na página **Adicionar acesso à API**, clique em **Concluído**.
   
    ![Criação de um aplicativo](./media/graph-get-started/21.png)

8. Na página **Permissões necessárias**, clique em **Conceder Permissões** e em **Sim**.
   
    ![Criação de um aplicativo](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Obter uma chave de acesso

1. Na página **Configurações**, clique em **Chaves**.
   
    ![Criação de um aplicativo](./media/graph-get-started/23.png)

2. Na página **Chaves**, execute as seguintes etapas:
   
    ![Criação de um aplicativo](./media/graph-get-started/24.png)

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

- grant_type: "**client_credentials**"

-  recurso: "**https://graph.microsoft.com**"

- client_id: \<a ID do cliente\>

- client_secret: \<a chave\>


Se for bem-sucedido, será retornado um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

    `Authorization`="<token_type> <access_token>"


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

Para saber mais sobre o Microsoft Graph e como criar aplicativos usando a API do Graph, confira a [documentação](https://developer.microsoft.com/graph/docs) e muito mais no [site Microsoft Graph](https://developer.microsoft.com/graph/). Além disso, marque a página [API do Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root) que lista todas as APIs do Identity Protection disponíveis no Graph. À medida que adicionarmos novas maneiras de trabalhar com o Identity Protection via API, você as verá nessa página.

Para obter informações relacionadas. consulte:

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Tipos de eventos de risco detectados pelo Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Visão geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)

- [Raiz do Serviço Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)

