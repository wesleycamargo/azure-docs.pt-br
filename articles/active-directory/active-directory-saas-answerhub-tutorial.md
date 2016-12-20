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
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 067d47bdfa5459a8e751292339a0e7ff4a8ad46b


---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Active Directory do Azure ao AnswerHub
O objetivo deste tutorial é mostrar a integração do Azure ao [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software)

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao AnswerHub poderão fazer logon único no aplicativo em seu site de empresa do AnswerHub (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o AnswerHub
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario")

## <a name="enabling-the-application-integration-for-answerhub"></a>Habilitando a integração de aplicativos para o AnswerHub
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o AnswerHub.

### <a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o AnswerHub, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Add application")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Add an application from gallerry")
6. Na **caixa de pesquisa**, digite **AnswerHub**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Application Gallery")
7. No painel de resultados, selecione **AnswerHub** e clique em **Concluir** para adicionar o aplicativo.
   
   ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no AnswerHub com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **AnswerHub**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configure single sign-on")
2. Na página **Como você deseja que os usuários façam logon no AnswerHub**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configure single sign-on")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do AnswerHub**, digite a URL usando o seguinte padrão "*https://company.answerhub.com*", depois clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configure App URL")
4. Na página **Configurar logon único no AnswerHub**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configure single sign-on")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa AnswerHub como um administrador.
   
   > [!NOTE]
   > Se você precisar de ajuda para configurar o AnswerHub, entre em contato com a [equipe de suporte do AnswerHub](mailto:success@answerhub.com.).
   > 
   > 
6. Vá para **Administração**.
7. Clique na guia **Usuário e Grupo** .
8. No painel de navegação à esquerda, na seção **Configurações Sociais**, clique em **Configuração do SAML**.
9. Clique na guia **Config. de IDP** .
10. Na guia **Config. de IDP** , realize as seguintes etapas:
    
    ![Configuração do SAML](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")
    
    1. No portal clássico do Azure, na página de caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do IDP**.
    2. No portal clássico do Azure, na página de caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor da **URL de Logout Remoto** e cole-o na caixa de texto **URL de Logout do IDP**.
    3. No portal clássico do Azure, na página de caixa de diálogo **Configurar logon único no AnswerHub**, copie o valor do **Formato do Identificador de Nome** e cole-o na caixa de texto **Formato do Identificador de Nome do IDP**.
    4. Clique em **Chaves e Certificados**.
11. Na guia Chaves e Certificados, execute as seguintes etapas:
    
    ![Chaves e Certificados](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")
    
    1. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
       
       > [!TIP]
       > Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
       > 
       > 
    2. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Chave Pública do IDP (Formato x509)** .
    3. Clique em **Salvar**.
12. Na guia **Config. de IDP**, clique em **Salvar**.
13. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o logon único](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no AnswerHub, eles devem ser provisionados no AnswerHub.  
No caso do AnswerHub, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **AnswerHub** como administrador.
2. Vá para **Administração**.
3. Clique na guia **Usuários e Grupos**.
4. No painel de navegação à esquerda, na seção **Gerenciar Usuários**, clique em **Criar ou importar usuários**.
   
   ![Usuários e Grupos](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")
5. Digite **Endereço de email**, **Nome de usuário** e **Senha** de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas e clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do AnswerHub ou as APIs fornecidas pelo AnswerHub para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Para atribuir usuários ao AnswerHub, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **AnswerHub**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assign users")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Yes")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


