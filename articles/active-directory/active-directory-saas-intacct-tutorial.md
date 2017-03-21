---
title: "Tutorial: Integração do Azure Active Directory com o Intacct | Microsoft Docs"
description: "Saiba como usar o Intacct com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: Integração do Active Directory do Azure com o Intacct
O objetivo deste tutorial é mostrar a integração do Azure com o Intacct.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Intacct

Depois de concluir este tutorial, os usuários do Azure AD (Azure Active Directory) atribuídos ao Intacct poderão entrar no aplicativo em seu site de empresa do Intacct (logon iniciado pelo provedor de serviços) ou usando o [Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Intacct
* Configurando o logon único
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-intacct-tutorial/IC790030.png "Cenário")

## <a name="enable-the-application-integration-for-intacct"></a>Habilitar a integração de aplicativos para o Intacct
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Intacct.

**Para habilitar a integração de aplicativos para o Intacct, execute as seguintes etapas:**

1. No portal clássico do Azure, no painel esquerdo, clique no ícone do Active Directory.

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

   ![Aplicativos](./media/active-directory-saas-intacct-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.

   ![Adicionar aplicativo](./media/active-directory-saas-intacct-tutorial/IC749321.png "Adicionar aplicativo")
5. Na página **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-intacct-tutorial/IC749322.png "Adicionar um aplicativo de galeria")
6. Na caixa de pesquisa, insira **Intacct**.

   ![Galeria de Aplicativos](./media/active-directory-saas-intacct-tutorial/IC790031.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Intacct** e clique em **Concluir** para adicionar o aplicativo.

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Intacct com sua conta do Azure AD. A federação com base no protocolo SAML é usada para essa autenticação.  

Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base&64;. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Intacct**, clique em **Configurar logon único** para abrir a página **Configurar Logon Único**.

   ![Configurar logon único](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no Intacct**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.

   ![Configurar logon único](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurar logon único")
3. No **URL do aplicativo** página, o **URL de logon** , digite o URL que usa o *https://Intacct.com/company* padrão e, em seguida, clique em **próxima**.

   ![Configurar URL do Aplicativo](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Intacct**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

   ![Configurar logon único](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurar logon único")
5. Em outra janela do navegador da Web, entre em seu site de empresa do Intacct como administrador.
6. Clique na guia **Empresa** e em **Informações da Empresa**.

   ![Empresa](./media/active-directory-saas-intacct-tutorial/IC790037.png "Empresa")
7. Clique na guia **Segurança** e em **Editar**.

   ![Segurança](./media/active-directory-saas-intacct-tutorial/IC790038.png "Segurança")
8. Na seção **SSO (logon único)** , realize as seguintes etapas:

   ![Logon único](./media/active-directory-saas-intacct-tutorial/IC790039.png "logon único")

   1. Selecione **Habilitar logon único**.
   2. Para **Tipo de provedor de identidade**, selecione **SAML 2.0**.
   3. No portal clássico do Azure, na página **Configurar logon único no Intacct**, copie o valor da **URL do Emissor** e cole-o na caixa **URL do Emissor**.
   4. No portal clássico do Azure, na página **Configurar logon único no Intacct**, copie o valor de **URL de Logon Remoto** e cole-o na caixa **URL de Logon**.
   5. Crie um arquivo **codificado em base&64;** usando o certificado baixado. Para saber mais, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).      
   6. Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa **Certificado**.
   7. Clique em **Salvar**.
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a página **Configurar logon único**.

   ![Configurar Logon Único](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurar Logon Único")

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para configurar os usuários do Azure AD para que possam entrar no Intacct, eles devem ser provisionados no Intacct. Para o Intacct, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **Intacct**.
2. Clique na guia **Empresa** e em **Usuários**.

   ![Usuários](./media/active-directory-saas-intacct-tutorial/IC790041.png "Usuários")
3. Clique na guia **Adicionar**.

   ![Adicionar](./media/active-directory-saas-intacct-tutorial/IC790042.png "Adicionar")
4. Na seção **Informações do Usuário** , realize as seguintes etapas:

   ![Informações do Usuário](./media/active-directory-saas-intacct-tutorial/IC790043.png "informações do Usuário")

   1. Insira a **ID de Usuário**, o **Sobrenome**, o **Nome**, o **Endereço de email**, o **Título** e o **Telefone** de uma conta do Azure AD que você deseja provisionar na seção **Informações do Usuário**.
   2. Selecione os **privilégios de Administrador** de uma conta do Azure AD que você deseja provisionar.
   3. Clique em **Salvar**. O titular da conta do Azure AD receberá um email e um link para confirmar sua conta antes de se tornar ativo.

>[!NOTE]
>Para provisionar as contas de usuário do Azure AD, é possível usar qualquer outra ferramenta de criação da conta de usuário do Intacct ou APIs fornecidas pelo Intacct.
>

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, você precisa atribuir os usuários do Azure AD para o Intacct. Depois que um usuário é atribuído, ele pode acessar seu aplicativo.

**Para atribuir usuários ao Intacct, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Intacct**, clique em **Atribuir usuários**.

   ![Atribuir usuários](./media/active-directory-saas-intacct-tutorial/IC790044.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

   ![Sim](./media/active-directory-saas-intacct-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você configura Brenda Fernandes para usar o logon único do Azure concedendo-lhe acesso ao Intacct.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Intacct, execute as seguintes etapas:**

1. No portal do Azure, abra a exibição de aplicativos, vá para a exibição de diretório, para **Aplicativos da empresa**, então, clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Intacct**.

    ![Configurar o logon único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. No menu **Gerenciar**, clique em **Usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar** e, na folha **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Atribuir usuário][203]

5. Em **Usuários e grupos**, selecione **Brenda Fernandes** na lista do usuário.

6. Em **Usuários e grupos**, clique no botão **Selecionar**.

7. Em **Adicionar Atribuição**, clique no botão **Atribuir**.



### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Intacct no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Intacct.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

