---
title: "Tutorial: integração do Azure Active Directory com o OnTrack | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o OnTrack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: e1262e276a87bc9a4b6d43de2bf6b3f7d8882213
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Tutorial: integração do Azure Active Directory com o OnTrack

Neste tutorial, você aprenderá a integrar o OnTrack com o Azure AD (Azure Active Directory).

A integração do OnTrack com o Azure AD oferece os seguintes benefícios:

- Você pode controlar quem terá acesso ao OnTrack pelo Azure AD.
- Você pode permitir que os usuários façam logon automaticamente no OnTrack (logon único) com suas respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o OnTrack, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do OnTrack

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o OnTrack pela galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-ontrack-from-the-gallery"></a>Adicionar o OnTrack pela galeria
Para configurar a integração do OnTrack com o Azure AD, você precisará adicionar o OnTrack pela galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OnTrack pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **OnTrack**, selecione **OnTrack** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![OnTrack na lista de resultados](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o OnTrack com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do OnTrack é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OnTrack.

No OnTrack, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o OnTrack, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do OnTrack](#create-an-ontrack-test-user)** – para ter um equivalente de Brenda Fernandes no OnTrack vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo OnTrack.

**Para configurar o logon único do Azure AD com o OnTrack, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **OnTrack**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_samlbase.png)

3. Na seção **URLs e Domínio do OnTrack**, execute as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do OnTrack](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_url.png)

    a. Na caixa de texto **Identificador**,
    
    Para o ambiente de teste, digite a URL:`https://staging.insigniagroup.com/sso`

    Para o ambiente de produção, digite a URL:`https://oeaccessories.com/sso`

    b. Na caixa de texto **URL de Resposta**,
    
    Para o ambiente de teste, digite a URL:`https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Para o ambiente de produção, digite a URL:`https://igaccessories.com/sso/autonation.aspx`

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_certificate.png)

5. O aplicativo OnTrack espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. 

    ![Configurar Logon Único](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|    
    | Função do usuário      | “42F432” |
    | Código Hyperion  | “12345” |

    > [!NOTE]
    > Os atributos **função do usuário** e **código Hyperion** são mapeados com a função do usuário do Autonation e o código do revendedor, respectivamente. Esses valores são apenas exemplos. Use o código correto para a integração. Você pode entrar em contato com o [suporte da Autonation](mailto:CustomerService@insigniagroup.com) para obter esses valores.
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_04.png)   

    ![Configurar Logon Único](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-ontrack-tutorial/tutorial_general_400.png)

8. Para configurar o logon único no lado do **OnTrack**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do OnTrack](mailto:CustomerService@insigniagroup.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-ontrack-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-ontrack-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-ontrack-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-ontrack-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-ontrack-test-user"></a>Criar um usuário de teste do OnTrack

Nesta seção, você criará um usuário chamado Brenda Fernandes no OnTrack. Trabalhe com a [equipe de suporte do OnTrack](mailto:CustomerService@insigniagroup.com) para adicionar os usuários na plataforma do OnTrack. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo-lhe acesso ao OnTrack.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao OnTrack, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **OnTrack**.

    ![O link do OnTrack na lista de Aplicativos](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco OnTrack no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo OnTrack.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_203.png

