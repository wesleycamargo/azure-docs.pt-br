---
title: Pré-requisitos para acessar a API de relatório do Azure Active Directory | Microsoft Docs
description: Aprenda sobre os pré-requisitos para acessar a API de relatório do Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ab05907f1f23c3856b41a941c1b95992ed5a79a4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929975"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Pré-requisitos para acessar a API de relatório do Azure Active Directory

As [APIs de relatório do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web.

Para preparar seu acesso à API de relatório, você precisa:

1. Atribuir funções
2. Registrar um aplicativo
3. Conceder permissões
4. Reunir definições de configuração



## <a name="assign-roles"></a>Atribuir funções

Para obter acesso aos dados de relatórios por meio da API, você precisa ter uma das seguintes funções atribuídas:

- Leitor de segurança

- Administrador de Segurança

- Administrador global




## <a name="register-an-application"></a>Registrar um aplicativo

Você precisa registrar um aplicativo mesmo que esteja acessando a API de relatório usando um script. Isso lhe dá uma **ID do aplicativo**, que é necessária para uma chamada de autorização e permite que seu código receba tokens.

Para configurar seu diretório para acessar a API de relatório do Azure AD, entre no Portal do Azure com uma conta de administrador do Azure que também seja membro da função de diretório de **Administrador Global** em seu locatário do Azure AD.

> [!IMPORTANT]
> Aplicativos em execução com credenciais com privilégios de "admin" como esse podem ser muito poderosos, portanto certifique-se de proteger as credenciais de ID/segredo do aplicativo.
> 


**Para registrar um aplicativo do Azure Active Directory:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na página **Azure Active Directory**, clique em **Registros do aplicativo**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Na página **Registros do aplicativo**, na barra de ferramentas na parte superior, clique em **Novo registro de aplicativo**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Na página **Criar**, realize as seguintes etapas:

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Na caixa de texto **Nome**, digite `Reporting API application`.

    b. Como **Tipo de aplicativo**, selecione **Aplicativo/API Web**.

    c. Na caixa de texto **URL de Entrada**, digite `https://localhost`.

    d. Clique em **Criar**. 


## <a name="grant-permissions"></a>Conceder permissões 

Dependendo da API que deseja acessar, você precisa garantir ao seu aplicativo as permissões a seguir:  

| API | Permissão |
| --- | --- |
| Windows Azure Active Directory | Ler dados do diretório |
| Microsoft Graph | Ler todos os dados de log de auditoria |


![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


A seção a seguir lista as etapas para ambas as APIs. Se não quiser acessar uma das APIs, você poderá ignorar as etapas relacionadas.
 

**Para conceder ao seu aplicativo permissões para usar as APIs:**

1. Na página **Registros do aplicativo**, na lista de aplicativos, clique em **Aplicativo da API de relatório**.

2. Na página **Aplicativo da API de relatório**, na barra de ferramentas na parte superior, clique em **Configurações**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Na página **Configurações**, clique em **Permissões necessárias**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Na página **Permissões necessárias**, na lista **API**, clique em **Microsoft Azure Active Directory**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. No página **Habilitar acesso**, selecione **Ler dados do diretório** e desmarque **Entrar e ler o perfil do usuário**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na barra de ferramentas na parte superior, clique em **Salvar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Na página **Permissões necessárias**, na barra de ferramentas na parte superior, clique em **Adicionar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Na página **Selecionar uma API**, clique em **Microsoft Graph** e em **Selecionar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Na página **Habilitar acesso**, selecione **Ler todos os dados de log de auditoria** e, em seguida, clique em **Selecionar**.  

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Na página **Adicionar acesso à API**, clique em **Concluído**.  

12. Na página **Permissões necessárias**, na barra de ferramentas na parte superior clique em **Conceder Permissões** e, em seguida, em **Sim**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Reunir definições de configuração 

Esta seção mostra como obter as seguintes configurações de seu diretório:

- Nome de domínio
- ID do cliente
- Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatórios. 

### <a name="get-your-domain-name"></a>Obter seu nome de domínio

**Para obter seu nome de domínio:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na página **Azure Active Directory**, clique em **Personalizar nomes de domínio**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copie o nome do domínio da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obtenha a ID do cliente do aplicativo

**Para obter a ID do cliente do aplicativo:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na página **Registros do aplicativo**, na lista de aplicativos, clique em **Aplicativo da API de relatório**.

3. Na página **Aplicativo da API de relatório**, em **ID do aplicativo**, clique em **Clique para copiar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obter seu segredo do cliente do aplicativo
Para obter o segredo do cliente do aplicativo, você precisa criar uma nova chave e salvar seu valor ao salvar a nova chave, pois não é possível recuperar este valor posteriormente.

**Para obter seu segredo do cliente do aplicativo:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na página **Registros do aplicativo**, na lista de aplicativos, clique em **Aplicativo da API de relatório**.


3. Na página **Aplicativo da API de relatório**, na barra de ferramentas na parte superior, clique em **Configurações**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Na página **Configurações**, na seção **Acesso APIR**, clique em **Chaves**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Na página **Chaves**, execute as seguintes etapas:

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Na caixa de texto **Descrição**, digite `Reporting API`.

    b. Como **Expira**, selecione **Em 2 anos**.

    c. Clique em **Salvar**.

    d. Copie o valor da chave.


## <a name="next-steps"></a>Próximas etapas

- [Obter dados usando a API de Relatório do Azure Active Directory com certificados](active-directory-reporting-api-with-certificates.md).

- [Tenha uma primeira impressão das APIs de relatórios](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Crie sua própria solução](active-directory-reporting-api-getting-started-azure-portal.md#customize)

