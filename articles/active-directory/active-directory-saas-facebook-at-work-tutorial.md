---
title: "Tutorial: Integração do Azure Active Directory com o Workplace by Facebook| Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Azure Active Directory com o Workplace by Facebook
O objetivo desse tutorial é mostrar como integrar o Workplace by Facebook ao Azure AD (Azure Active Directory).

A integração do Workplace by Facebook ao Azure AD oferece os seguintes benefícios: 

* No Azure AD, você pode controlar quem tem acesso ao Workplace by Facebook 
* Você pode provisionar automaticamente uma conta para usuários que receberam acesso ao Workplace by Facebook
* Você pode habilitar o logon automático de seus usuários no Workplace by Facebook (Logon único) com as contas do Azure AD
* Você pode gerenciar suas contas em um local central 

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao CS Stars, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Workplace by Facebook com o logon único habilitado

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar o Workplace by Facebook da Galeria
Para configurar a integração do Workplace by Facebook ao Azure AD, você precisa adicionar o Workplace by Facebook da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workplace by Facebook da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Workplace by Facebook**.
7. No painel de resultados, selecione **Workplace by Facebook** e clique em **Concluir** para adicionar o aplicativo.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
Esta seção descreve como permitir que os usuários se autentiquem no Workplace by Facebook com a respectiva conta do Azure Active Directory usando federação baseada em protocolo SAML.

**Para configurar o logon único do Azure AD com o Workplace by Facebook, execute as seguintes etapas:**

1. Depois de adicionar o Workplace by Facebook no Portal Clássico do Azure, clique em **Configurar Logon Único**.
2. Na tela **Configurar URL do aplicativo** , digite a URL onde os usuários entrarão no seu aplicativo Workplace by Facebook. Esta é a URL de locatário do Workplace by Facebook (Exemplo: https://example.facebook.com/). Ao terminar, clique em **Avançar**.
3. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Workplace by Facebook como administrador.
4. Siga as instruções na seguinte URL para configurar o Workplace by Facebook para usar o Azure AD como um provedor de identidade: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. Depois de concluído, retorne para as janelas do navegador mostrando o Portal clássico do Azure, clique na caixa de seleção para confirmar que você concluiu o procedimento e clique em **Avançar** e em **Concluir**.


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>Provisionamento automático dos usuários do Workplace by Facebook
O Azure AD oferece suporte à capacidade de sincronizar automaticamente os detalhes da conta de usuários atribuídos ao Workplace by Facebook. Essa sincronização automática permite que o Workplace by Facebook obtenha os dados necessários para autorizar o acesso aos usuários antes que eles tentem entrar pela primeira vez. Ele também desprovisiona os usuários do Workplace by Facebook quando o acesso tiver sido revogado no Azure AD.

O provisionamento automático pode ser definido clicando em **Configurar provisionamento de conta** na janela do portal clássico do Azure.

Para obter detalhes adicionais sobre como configurar o provisionamento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>Atribuição de usuários ao Workplace by Facebook
Para que os usuários do AAD provisionados possam ver o Workplace by Facebook em seu painel de acesso, eles devem receber acesso no Portal Clássico do Azure.

**Para atribuir usuários ao Workplace by Facebook:**

1. Na página inicial do Workplace by Facebook no Portal Clássico do Azure, clique em **Atribuir contas**.
2. No menu **Mostrar**, selecione se deseja atribuir um usuário ou um grupo ao Workplace by Facebook e clique no botão Marca de Seleção.
3. Na lista resultante, selecione os usuários ou grupo aos quais você deseja atribuir o Workplace by Facebook.
4. No rodapé da página, clique no botão **Atribuir** .

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


