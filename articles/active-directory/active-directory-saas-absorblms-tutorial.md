---
title: "Tutorial: Integração do Azure Active Directory ao Absorb LMS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f5f20fee648cf26b4db49b9a8f5f5e5bcecd453e
ms.contentlocale: pt-br
ms.lasthandoff: 09/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Azure Active Directory ao Absorb LMS

Neste tutorial, você aprenderá a integrar o Absorb LMS ao Azure AD (Azure Active Directory).

A integração do Absorb LMS ao Azure AD oferece os seguintes benefícios:

- É possível controlar no Azure AD quem tem acesso ao Absorb LMS
- Você pode permitir que seus usuários entrem automaticamente no Absorb LMS (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte. [O que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Absorb LMS, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Absorb LMS com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Absorb LMS da Galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-absorb-lms-from-the-gallery"></a>Adicionar o Absorb LMS da Galeria
Para configurar a integração do Absorb LMS com o Azure AD, você precisa adicionar o Absorb LMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Absorb LMS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Absorb LMS**, selecione **Absorb LMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Absorb LMS na lista de resultados](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Absorb LMS, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Absorb LMS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Absorb LMS.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Absorb LMS.

Para configurar e testar o logon único do Azure AD com o Absorb LMS, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Absorb LMS](#create-an-absorb-lms-test-user)** – para ter um equivalente de Brenda Fernandes no Absorb LMS que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo Absorb LMS.

**Para configurar o logon único do Azure AD com o Absorb LMS, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Absorb LMS**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Na seção **Domínio e URLs do Absorb LMS**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.myabsorb.com/Account/SAML`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.myabsorb.com/Account/SAML`
     
    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do cliente Absorb LMS](https://www.absorblms.com/support) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do Absorb LMS**, clique em **Configurar Absorb LMS** para abrir a janela **Configurar logon**. Copie a **URL de Logoff** da **seção de Referência rápida.**

    ![Configuração do Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. Em outra janela do navegador da Web, faça logon em seu site de empresa Absorb LMS como um administrador.

9. Clique no **Ícone de Conta** na interface de administrador. 

    ![Configurar Logon Único](./media/active-directory-saas-absorblms-tutorial/1.png)

10. Clique em **Configurações do Portal**.

    ![Configurar o logon único](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. Clique na guia **Usuários** .

    ![Configurar Logon Único](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Para acessar os campos de configuração de logon único, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Selecione **Iniciado pelo provedor de identidade**.

    b. Abra o certificado que você baixou do portal do Azure no bloco de notas, remova as marcas **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** e, em seguida, cole o conteúdo restante na caixa de texto **Chave**.
    
    c. Na **Propriedade ID**, selecione o atributo apropriado que você configurou como o identificador de usuário no Azure AD (por exemplo, se o userprinciplename fosse selecionado no Azure AD, o nome de usuário seria selecionado aqui.)

    d. Na **URL de logon**, cole a **URL de acesso do usuário** do aplicativo da página **Propriedades** do portal do Azure.

    e. Na **URL de logoff**, cole o valor de **URL de saída** copiado da janela **Configurar logon** do portal do Azure.

13. Habilitar **"Permitir apenas logon SSO"**.

    ![Configurar Logon Único](./media/active-directory-saas-absorblms-tutorial/5.png)

14. Clique em **"Salvar".**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![O botão Adicionar](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="create-an-absorb-lms-test-user"></a>Criar um usuário de teste do Absorb LMS

Para permitir que os usuários do Azure AD façam logon no Absorb LMS, eles devem ser provisionados no Absorb LMS.  
Para o Absorb LMS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Absorb LMS como administrador.

2. Clique na guia **Usuários**.

    ![Convidar Pessoas](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. Clique em **Usuários** sob a guia **Usuários**.

    ![Convidar Pessoas](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  Selecione **Usuário** da lista suspensa **Adicionar Novo**.

    ![Convidar Pessoas](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Na página **Adicionar Usuário**, realize as seguintes etapas:

    ![Convidar Pessoas](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário como Brenda.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como Fernandes.
    
    c. Na caixa de texto **Nome de usuário**, digite o nome de usuário como Brenda Fernandes.

    d. Na caixa de texto **Senha**, digite a senha de Brenda Fernandes.

    e. Na caixa de texto **Confirmar Senha**, digite a mesma senha.
    
    f. Torne-a **ATIVA**.   

6. Clique em **"Salvar".**
 
### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concederá acesso ao Absorb LMS a Brenda Fernandes para habilitá-la a usar o logon único do Azure.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Absorb LMS, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Absorb LMS**.

    ![O link do Absorb LMS na lista Aplicativos](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Clique no bloco Absorb LMS no Painel de Acesso, você será conectado automaticamente ao seu aplicativo Absorb LMS. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


