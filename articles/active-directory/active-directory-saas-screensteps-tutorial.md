---
title: "Tutorial: Integração do Azure Active Directory ao ScreenSteps | Microsoft Docs"
description: "Saiba como usar o ScreenSteps com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração do Active Directory do Azure com o ScreenSteps
O objetivo deste tutorial é mostrar a integração do Azure ao ScreenSteps.
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do ScreenSteps

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ScreenSteps poderão entrar no aplicativo usando SSO (logon único) em seu site de empresa do ScreenSteps (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o ScreenSteps
2. Configuração do SSO (logon único) 
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Cenário")

## <a name="enable-the-application-integration-for-screensteps"></a>Habilitar a integração de aplicativos para o ScreenSteps
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ScreenSteps.

**Para habilitar a integração de aplicativos com o ScreenSteps, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **ScreenSteps**.

    ![Galeria de aplicativos](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galeria de aplicativos")
7. No painel de resultados, selecione **ScreenSteps** e clique em **Concluir** para adicionar o aplicativo.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ScreenSteps com sua conta do AD do Azure usando federação baseada no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **ScreenSteps**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

    ![Configurar logon único](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no ScreenSteps**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar logon único")

3. Em outra janela do navegador da Web, faça logon em seu site de empresa ScreenSteps como um administrador.

4. Clique em **Gerenciamento de Contas**.

    ![Gerenciamento de contas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gerenciamento de contas")

5. Clique em **Logon Único**.

    ![Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticação Remota")

6. Clique em  **Criar Ponto de Extremidade de Logon Único**.

    ![Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticação Remota")

7. Na seção **Criar um Ponto de Extremidade de Logon Único**, realize as seguintes etapas:

    ![Criar um Ponto de Extremidade de Autenticação](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Criar um Ponto de Extremidade de Autenticação")

    1. Na caixa de texto **Título** , digite um título.
    2. Na lista **Modo**, selecione **SAML**.
    3. Clique em **Criar**.

8. Edite o novo ponto de extremidade.

    ![Editar ponto de extremidade](./media/active-directory-saas-screensteps-tutorial/IC778528.png "Editar ponto de extremidade")

9. Na seção **Editar um Ponto de Extremidade de Logon Único**, realize as seguintes etapas:

    ![Ponto de Extremidade de Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Ponto de Extremidade de Autenticação Remota")

    1. Copie a **URL de consumidor SAML** para a área de transferência.

10. Na página **Configurar URL do Aplicativo** do Portal Clássico do Azure, na caixa de texto **URL de Entrada do ScreenSteps**, cole a **URL de consumidor SAML** e, em seguida, clique em **Avançar**.

    ![Configurar URL do Aplicativo](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar URL do Aplicativo")

11. Na página **Configurar logon único no ScreenSteps**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar logon único")


12. Retorne à seção **Editar um Ponto de Extremidade de Logon Único** e realize as seguintes etapas:

    ![Ponto de Extremidade de Autenticação Remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Ponto de Extremidade de Autenticação Remota")

    1. Clique em **Carregar novo arquivo de certificado SAML** e carregue o certificado baixado.
    2. No portal clássico do Azure, na página **Configurar logon único no ScreenSteps**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon Remoto**.
    3. No portal clássico do Azure, na página **Configurar logon único no ScreenSteps**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff**.
    4. Selecione um **Grupo** para o qual atribuir usuários quando eles são provisionados.
    5. Clique em **Atualizar**.
    6. Retorne para **Editar Ponto de Extremidade de Logon Único**.
    7. Clique no botão **Tornar padrão para a conta** para usar esse ponto de extremidade para todos os usuários que fizerem logon no ScreenSteps. Como alternativa, você pode clicar no botão **Adicionar ao Site** usar esse ponto de extremidade para sites específicos em **ScreenSteps**.


12. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar logon único](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar logon único")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários



## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao ScreenSteps, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **ScreenSteps**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.


Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


