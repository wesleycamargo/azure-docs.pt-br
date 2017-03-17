---
title: "Tutorial: integração do Azure Active Directory ao AnswerHub | Microsoft Docs"
description: "Saiba como usar o AnswerHub com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ff8a2bc6350be35fa368e11d2ebd4a994f01cedc
ms.openlocfilehash: af99741f5f5f8b2fb1c4fc8975571c65c2fc98c6
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Active Directory do Azure ao AnswerHub
O objetivo deste tutorial é mostrar a integração do Azure ao [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software)

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao AnswerHub poderão acessar o aplicativo por meio de logon único (SSO) no site da empresa do AnswerHub (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o AnswerHub
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Cenário")

## <a name="enable-the-application-integration-for-answerhub"></a>Habilitar a integração de aplicativos para o AnswerHub
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AnswerHub.

**Para habilitar a integração de aplicativos para o AnswerHub, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **AnswerHub**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **AnswerHub** e clique em **Concluir** para adicionar o aplicativo.
   
   ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

## <a name="configure-single-sign-on"></a>Configurar o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AnswerHub com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.  

Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base&64;. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **AnswerHub**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no AnswerHub**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurar Logon Único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do AnswerHub**, digite a URL usando o seguinte padrão "*https://company.answerhub.com*", depois clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no AnswerHub**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurar Logon Único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa AnswerHub como um administrador.
   
   >[!NOTE]
   >Se você precisar de ajuda para configurar o AnswerHub, entre em contato com a [equipe de suporte do AnswerHub](mailto:success@answerhub.com.).
   > 
6. Vá para **Administração**.
7. Clique na guia **Usuário e Grupo** .
8. No painel de navegação à esquerda, na seção **Configurações Sociais**, clique em **Configuração do SAML**.
9. Clique na guia **Config. de IDP** .
10. Na guia **Config. de IDP** , realize as seguintes etapas:

  ![Instalação do SAML](./media/active-directory-saas-answerhub-tutorial/IC785172.png "Instalação do SAML")  
  1. No portal clássico do Azure, na página de caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IDP**.
  2. No portal clássico do Azure, na página de caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor da **URL de Logout Remoto** e cole-o na caixa de texto **URL de Logout do IDP**.
  3. No portal clássico do Azure, na página de caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor do **Formato do Identificador de Nome** e cole-o na caixa de texto **Formato do Identificador de Nome do IDP**.
  4. Clique em **Chaves e Certificados**.
11. Na guia Chaves e Certificados, execute as seguintes etapas:
    
  ![Chaves e Certificados](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Chaves e Certificados")  
  1. Crie um arquivo **codificado em base&64;** usando o certificado baixado.
  
    >[!TIP]
    >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o). 
    > 
  2. Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Chave Pública do IDP (Formato x509)** .
  3. Clique em **Salvar**.
12. Na guia **Config. de IDP**, clique em **Salvar**.
13. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
  ![Configurar logon único](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurar logon único")

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
Para permitir que os usuários do AD do Azure façam logon no AnswerHub, eles devem ser provisionados no AnswerHub.  

* No caso do AnswerHub, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **AnswerHub** como administrador.
2. Vá para **Administração**.
3. Clique na guia **Usuários e Grupos**.
4. No painel de navegação à esquerda, na seção **Gerenciar Usuários**, clique em **Criar ou importar usuários**.
   
   ![Usuários e Grupos](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Usuários e Grupos")
5. Digite **Endereço de email**, **Nome de usuário** e **Senha** de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas e clique em **Salvar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do AnswerHub ou as APIs fornecidas pelo AnswerHub para provisionar as contas de usuário do AAD.
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

**Para atribuir usuários ao AnswerHub, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **AnswerHub**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


