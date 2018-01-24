---
title: "Tutorial: Integração do Microsoft Azure Active Directory com Vodeclic | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Vodeclic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Tutorial: integração do Microsoft Azure Active Directory com o Vodeclic

Neste tutorial, você aprenderá a integrar o Vodeclic ao Microsoft Azure AD (Microsoft Azure Active Directory).

A integração do Vodeclic ao Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem terá acesso ao Vodeclic.
- Você pode permitir que os usuários entrem automaticamente no Vodeclic (logon único ou SSO) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD com o Vodeclic, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada pelo SSO do Vodeclic

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, [receba uma versão de avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. adicionar o Vodeclic da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-vodeclic-from-the-gallery"></a>Adicionar o Vodeclic da Galeria
Para configurar a integração do Vodeclic no Microsoft Azure AD, é necessário adicionar o Vodeclic da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Vodeclic da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Vodeclic**. Selecione **Vodeclic** do painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Vodeclic na lista de resultados](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Vodeclic, com base em uma usuária de teste chamada "Brenda Fernandes."

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do Vodeclic é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado no Vodeclic.

No Vodeclic, atribua ao **Username** o mesmo valor que o **nome de usuário** no Microsoft Azure AD. Agora, você estabeleceu o vínculo entre os dois usuários.

Para configurar e testar o logon único do Microsoft Azure AD com o Vodeclic, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Microsoft Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usarem esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do Vodeclic](#create-a-vodeclic-test-user) para ter um equivalente de Brenda Fernandes no Vodeclic vinculado à representação do usuário no Microsoft Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Testar o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no Portal do Azure e configurará o logon único no aplicativo portal Vodeclic.

**Para configurar o logon único do Microsoft Azure AD com o Vodeclic, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Vodeclic**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, no **Modo de Logon Único**, selecione **	Logon com base em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Caso queira configurar o aplicativo no modo iniciado pelo **IDP** na seção **Domínio e URLs do Vodeclic**, execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Se você quiser configurar o aplicativo no modo iniciado pelo **SP**, selecione a caixa de seleção **Mostrar configurações avançadas de URL** e execute a seguinte etapa:

    ![Informações de logon único de domínio e URLs do Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    Na caixa **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [Equipe de suporte ao cliente do Vodeclic](mailto:hotline@vodeclic.com) para obter esses valores.

5. Na seção **Certificado de Autenticação SAML**, selecione **XML de Metadados**. Em seguida, salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Selecione **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Para configurar o logon único no lado do **Vodeclic**, envie o **Metadata XML** baixado para a [Equipe de suporte do Vodeclic](mailto:hotline@vodeclic.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com) enquanto você estiver configurando o aplicativo. Depois de adicionar esse aplicativo a partir da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e acesse a documentação inserida na seção **Configuração**, na parte inferior. Você pode ler mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Para exibir uma lista de usuários, vá para **Usuários e grupos**. Selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-vodeclic-test-user"></a>Criar um usuário de teste do Vodeclic

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Vodeclic. Trabalhe com a [Equipe de suporte do Vodeclic](mailto:hotline@vodeclic.com) para adicionar os usuários na plataforma do Vodeclic. Os usuários devem ser criados e ativados antes de usar o logon único.

> [!NOTE]
> De acordo com os requisitos do aplicativo, talvez seja necessário que sua máquina seja incluída na lista de permissões. Para que isso seja possível, será necessário compartilhar seu endereço IP público com a [Equipe de suporte do Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Vodeclic.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Vodeclic, execute as seguintes etapas:**

1. No portal do Azure, abra a exibição de aplicativos e vá para a exibição de diretório. Em seguida, vá para **Aplicativos empresariais**, em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Vodeclic**.

    ![O link do Vodeclic na lista de Aplicativos](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, selecione **Usuários e grupos**, na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de **Usuários**.

6. Na caixa de diálogo **Usuários e grupos**, selecione o botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar Vodeclic no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Vodeclic.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

