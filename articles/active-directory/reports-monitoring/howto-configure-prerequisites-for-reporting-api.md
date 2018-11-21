---
title: Pré-requisitos para acessar a API de relatório do Azure Active Directory | Microsoft Docs
description: Aprenda sobre os pré-requisitos para acessar a API de relatório do Azure AD
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f72d15707d9f56b9e9b5a5d527d1204007c40afa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621965"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Pré-requisitos para acessar a API de relatório do Azure Active Directory

As [APIs de relatório do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web.

Para preparar seu acesso à API de relatório, você precisa:

1. [Atribuir funções](#assign-roles)
2. [Registrar um aplicativo](#register-an-application)
3. [Conceder permissões](#grant-permissions)
4. [Reunir definições de configuração](#gather-configuration-settings)

## <a name="assign-roles"></a>Atribuir funções

Para obter acesso aos dados de relatórios por meio da API, você precisa ter uma das seguintes funções atribuídas:

- Leitor de segurança

- Administrador de segurança

- Administrador global


## <a name="register-an-application"></a>Registrar um aplicativo

Você precisa registrar um aplicativo mesmo se estiver acessando a API de relatórios usando um script. Isso fornece um **ID de Aplicativo**, que é necessário para as chamadas de autorização e permite que seu código receba tokens.

Para configurar seu diretório para acessar a API de relatórios do Microsoft Azure Active Directory, você deve entrar no [Portal do Azure](https://portal.azure.com) com uma conta de administrador do Azure que também é membro da função de diretório **Administrador Global** no seu Locatário do Azure AD.

> [!IMPORTANT]
> Aplicativos em execução sob credenciais com privilégios de administrador podem ser muito poderosos, portanto, certifique-se de manter o ID do aplicativo e as credenciais secretas em um local seguro.
> 

**Para registrar um aplicativo do Microsoft Azure Active Directory:**

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** do painel de navegação à esquerda.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página **Azure Active Directory**, selecione **Registros de aplicativo**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na página **Registros do aplicativo**, selecione **Novo Registro do aplicativo**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Na página **Criar**, execute as seguintes etapas:

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/04.png)

     a. Na caixa de texto **Nome**, digite `Reporting API application`.

    b. Como **Tipo de aplicativo**, selecione **Aplicativo/API Web**.

    c. Na caixa de texto **URL de Entrada**, digite `https://localhost`.

    d. Selecione **Criar**. 


## <a name="grant-permissions"></a>Conceder permissões 

Dependendo da API que deseja acessar, você precisa garantir ao seu aplicativo as permissões a seguir:  

| API | Permissão |
| --- | --- |
| Windows Azure Active Directory | Ler dados do diretório |
| Microsoft Graph | Ler todos os dados de log de auditoria |


![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A seção a seguir lista as etapas para ambas as APIs. Se não quiser acessar uma das APIs, você poderá ignorar as etapas relacionadas.

**Para conceder ao seu aplicativo permissões para usar as APIs:**

1. Selecione seu aplicativo na página **Registros de aplicativo** e selecione **Configurações**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na página **Configurações**, selecione **Permissões necessárias**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na página **Permissões necessárias**, na lista **API**, clique em **Microsoft Azure Active Directory**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. No página **Habilitar acesso**, selecione **Ler dados do diretório** e desmarque **Entrar e ler o perfil do usuário**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Na barra de ferramentas na parte superior, clique em **Salvar**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Na página **Permissões necessárias**, na barra de ferramentas na parte superior, clique em **Adicionar**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Na página **Adicionar acesso à API**, clique em **Selecionar uma API**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Na página **Selecionar uma API**, clique em **Microsoft Graph** e em **Selecionar**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Na página **Habilitar acesso**, selecione **Ler todos os dados de log de auditoria** e, em seguida, clique em **Selecionar**.  

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Na página **Adicionar acesso à API**, clique em **Concluído**.  

11. Na página **Permissões necessárias**, na barra de ferramentas na parte superior clique em **Conceder Permissões** e, em seguida, em **Sim**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Reunir definições de configuração 

Esta seção mostra como obter as seguintes configurações de seu diretório:

- Nome de domínio
- ID do cliente
- Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatórios. 

### <a name="get-your-domain-name"></a>Obter seu nome de domínio

**Para obter seu nome de domínio:**

1. No [Portal do Azure Microsoft](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na página do **Active Directory do Azure**, selecione **Nomes de domínio personalizados**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copie o nome do domínio da lista de domínios.


### <a name="get-your-applications-client-id"></a>Obtenha a ID do cliente do aplicativo

**Para obter a ID do cliente do aplicativo:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecione seu aplicativo na página **Registros de aplicativo**.

3. Na página do aplicativo, navegue até **ID do aplicativo** e selecione **clique para copiar**.

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Obter seu segredo do cliente do aplicativo
Para obter o segredo do cliente do aplicativo, você precisa criar uma nova chave e salvar seu valor ao salvar a nova chave, pois não é possível recuperar este valor posteriormente.

**Para obter seu segredo do cliente do aplicativo:**

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Azure Active Directory**.
   
    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecione seu aplicativo na página **Registros de aplicativo**.

3. Na página do aplicativo, na barra de ferramentas na parte superior, selecione **Configurações**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Na página **Configurações**, na seção **Acesso à API**, clique em **Chaves**. 

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na página **Chaves**, execute as seguintes etapas:

    ![Registrar aplicativo](./media/howto-configure-prerequisites-for-reporting-api/14.png)

     a. Na caixa de texto **Descrição**, digite `Reporting API`.

    b. Como **Expira**, selecione **Em 2 anos**.

    c. Clique em **Salvar**.

    d. Copie o valor da chave.


## <a name="next-steps"></a>Próximas etapas

* [Obter dados usando a API de Relatório do Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
