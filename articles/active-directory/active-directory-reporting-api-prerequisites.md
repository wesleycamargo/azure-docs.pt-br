---
title: "Pré-requisitos para acessar a API de relatório do Azure AD. | Microsoft Docs"
description: "Aprenda sobre os pré-requisitos para acessar a API de relatório do Azure AD"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1
ms.contentlocale: pt-br
ms.lasthandoff: 12/29/2016

---
# Pré-requisitos para acessar a API de relatório do Azure AD
<a id="prerequisites-to-access-the-azure-ad-reporting-api" class="xliff"></a>
As [APIs de relatório do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web. 

Para preparar seu acesso à API de relatório, faça o seguinte:

1. Crie um aplicativo em seu locatário do Azure AD 
2. Conceda as permissões apropriadas do aplicativo para acessar os dados do Azure AD
3. Reúna as definições de configuração de seu diretório

Para dúvidas, problemas ou comentários, entre em contato com a [Ajuda de relatório do AAD](mailto:aadreportinghelp@microsoft.com).

## Criar um aplicativo Azure AD
<a id="create-an-azure-ad-application" class="xliff"></a>
Para configurar seu diretório a fim de acessar a API de relatório do Azure AD, você deve entrar Portal Clássico do Azure com uma conta de administrador de assinatura do Azure que também é membro da função de diretório de Administrador Global em seu locatário do Azure AD.

> [!IMPORTANT]
> Aplicativos em execução com credenciais com privilégios de "admin" como esse podem ser muito poderosos, portanto certifique-se de proteger as credenciais de ID/segredo do aplicativo.
> 
> 

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Na lista de **diretórios ativos** , selecione o diretório.
3. No menu superior, clique em **Aplicativos**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Na barra de ferramentas inferior, clique em **Adicionar**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/03.png) 
5. Na caixa de diálogo **O que você deseja fazer?**, clique em **Adicionar um aplicativo que minha organização esteja desenvolvendo**. 
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/04.png) 
6. Na página de diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a. Na caixa de texto **Nome** , digite um nome (por exemplo: Aplicativo de API de Relatório).
   
    b. Selecione **Aplicativo Web e/ou API Web**.
   
    c. Clique em **Próximo**.
7. Na caixa de diálogo **Propriedades de aplicativo** , execute as seguintes etapas: 
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a. Na caixa de texto **URL de Entrada**, digite `https://localhost`.
   
    b. Na caixa de texto **URI da ID do Aplicativo**, digite ```https://localhost/ReportingApiApp```.
   
    c. Clique em **Concluído**.

## Conceda permissão ao aplicativo para usar a API
<a id="grant-your-application-permission-to-use-the-api" class="xliff"></a>
1. No [portal clássico do Azure](https://manage.windowsazure.com/), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Na lista de **diretórios ativos** , selecione o diretório.
3. No menu superior, clique em **Aplicativos**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png)
4. Na lista de aplicativos, selecione o aplicativo recém-criado.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)
5. No menu na parte superior, clique em **Configurar**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)
6. Na seção **Permissões para outros aplicativos**, para o recurso **Azure Active Directory**, clique na lista suspensa **Permissões de Aplicativo** e selecione **Ler dados de diretório**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/09.png)
7. Na barra inferior, clique em **Salvar**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/10.png)

## Reúna as definições de configuração de seu diretório
<a id="gather-configuration-settings-from-your-directory" class="xliff"></a>
Esta seção mostra como obter as seguintes configurações de seu diretório:

* Nome de domínio
* ID do cliente
* Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatórios. 

### Obter seu nome de domínio
<a id="get-your-domain-name" class="xliff"></a>
1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Na lista de **diretórios ativos** , selecione o diretório.
3. No menu na parte superior, clique em **Domínios**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/11.png) 
4. Na coluna **Nome de Domínio** copie seu nome de domínio no campo.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/12.png) 

### Obter a ID do cliente do aplicativo
<a id="get-the-applications-client-id" class="xliff"></a>
1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Na lista de **diretórios ativos** , selecione o diretório.
3. No menu superior, clique em **Aplicativos**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Na lista de aplicativos, selecione o aplicativo recém-criado.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)
5. No menu na parte superior, clique em **Configurar**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)
6. Copie a **ID do Cliente**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/13.png)

### Obter o segredo do cliente do aplicativo
<a id="get-the-applications-client-secret" class="xliff"></a>
Para obter o segredo do cliente do aplicativo, você precisa criar uma nova chave e salvar seu valor ao salvar a nova chave, pois não é possível recuperar este valor posteriormente.

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Na lista de **diretórios ativos** , selecione o diretório.
3. No menu superior, clique em **Aplicativos**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Na lista de aplicativos, selecione o aplicativo recém-criado.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)
5. No menu na parte superior, clique em **Configurar**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)
6. Na seção **Chaves** , execute as seguintes etapas: 
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a. Na lista duração, selecione uma duração
   
    b. Na barra inferior, clique em **Salvar**.
   
    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. Copie o valor da chave.

## Próximas etapas
<a id="next-steps" class="xliff"></a>
* Você gostaria de acessar os dados da API de relatório do Azure AD de uma maneira programática? Confira [Introdução à API de Relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Se você quiser saber mais sobre os relatórios do Azure Active Directory, confira o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).  


